Replica Gateway Drivers
=======================

Replica gateways are *chunk-oriented*.  They receive serialized blocks and
manifests from user gateways via HTTP POST, and later serve them back via HTTP
GET.  In addition, replica gateways process requests to delete chunks that are
no longer referenced, and process file-level rename hints from user gateways.

Replica gateways are implemented as a single HTTP server process
(`syndicate-rg`) and a set of subordinate driver chassis processes
(`rg-driver`).  Each `rg-driver` worker process loads and runs one method of the driver code
uploaded by the gateway owner.

Each `rg-driver` instance handles a single responsibility.  The `syndicate-rg`
process will forward requests to one of its `rg-driver` processes based on the
type of request.  Specifically, the `syndicate-rg` process will run at least
one `rg-driver` with the following settings.

* `rg-driver read` will be used to load a chunk from
  the backend storage service.
* `rg-driver write` will be used to store a chunk to the backend
  storage service.
* `rg-driver delete` will be used to delete a chunk from the
  backend storage service.
* `rg-driver rename` will be used to handle a file-level rename
  hint from a user gateway when a file gets renamed.  It is is not necessary
  for all storage providers to handle this request (e.g. our iRODS driver needs
  this, but our S3 driver does not).
* `rg-driver serialize` will be used to make a chunk suitable for
  caching and transmission over the network.  Drivers do not need to implement
  this if it is unnecessary.
* `rg-driver deserialize` will be used take a serialized chunk
  and transform it back into a block or a manifest for subsequent processing.
  Drivers do not need to implement this step if it is unnecessary.

Each `rg-driver` process loads the gateway owner's driver, configuration, and
secrets.  Depending on the mode of operation, only one of the driver methods
will be called:

| **Driver chassis mode** | **Driver method called** |
| ----------------------- | ------------------------ |
| `rg-driver read`        | `read_chunk(request, chunk_fd, driver_config, driver_secrets)`           |
| `rg-driver write`       | `write_chunk(request, chunk, driver_config, driver_secrets)`          |
| `rg-driver delete`      | `delete_chunk(request, driver_config, driver_secrets)`         |
| `rg-driver rename`      | `rename_file(request, old_path, new_path, driver_config, driver_secrets)`          |
| `rg-driver serialize`   | `serialize(request, chunk)`            |
| `rg-driver deserialize` | `deserialize(request, chunk)`          |

Each `rg-driver` chassis process is long-lived--it will run until the
`syndicate-rg` process terminates, or until the gateway owner changes the driver
code (in which case the `syndicate-rg` process stops them and re-starts them
with the new code).

## POST Format

Replica gateways receive chunks in resposne to signed HTTP POST requests.  The
HTTP POST request is multi-part, and must include a `data-plane` and a `control-plane` field.
The `control-plane` field contains a signed `SG_messages::Request` structure
from the user gateway, and the `data-plane` field contains the chunk itself.

The `SG_messages::Request` structure describes the operation the user gateway
wants (i.e. get chunk, put chunk, delete chunk, rename file, etc.).  The
`data-plane` field may be omitted if there is no associated chunk to upload
(e.g. on delete).

Once the replica gateway authenticates the `SG_messages::Request` structure
(i.e. it was signed by the user gateway who claims to have sent it, and the
chunk's hash matches the hash in the `SG_messages::Request` structure), it
proceeds to process the request.

## Receiving chunks

The steps to receive store a chunk are as follows:

* **Receive serialized chunk.** The `syndicate-rg` process receives and buffers the chunk in its entirety.
  Once received, the `syndicate-rg` process constructs a `SG_messages::DriverRequest` message
  for the serialized chunk.

* **Deserialize chunk.** The `syndicate-rg` process deserializes the chunk by sending it to a
  `rg-driver` worker running with the `deserialize` setting.  The `rg-driver`
   worker calls the driver code's `deserialize()` method with the chunk and the
  `SG_messages::DriverRequest`, and replies the deserialized chunk back to `syndicate-rg`.
   
   If there is no `rg-driver` running with this setting (i.e. the driver code does not implement
   any specialized deserialization logic), then this step is a no-op.

* **Store chunk.** The `syndicate-rg` process then forwards the deserialized chunk to an
  `rg-driver` worker running with the `write` setting.  The `rg-worker` receives
  the chunk and the `SG_messages::DriverRequest` structure, and calls the driver code's
  `write_chunk()` method to store its data.  The `rg-driver` worker replies the
  success/fail status of the `write_chunk()` method.

* **Reply status.** The `syndicate-rg` process replies to the HTTP POST request
  with HTTP 200 if the above process ran to completion, or HTTP 40x or 50x if there were 
  any errors (e.g. out-of-memory, invalid request, driver crash, etc.).  The
  HTTP 200 response will include a serialized `SG_messages::Reply` structure in
  its body, which contains the driver's success/faulure status.

The `SG_messages::DriverRequest` structure passed to the `write_chunk()` method
will indicate whether or not the chunk is a serialized manifest or block.  If it
is a block, the structure will indicate the logical byte offset and length in
the file that the block overwrites.

## Sending chunks

Replica gateways serve data to anyone who asks via HTTP GET requests.  GET
requests are not authenticated, since it is expected that untrusted
intermediaries like CDNs will be requesting chunks on behalf of user gateways.
This is okay, however, since a
user gateway requesting chunks will already have learned their expected chunk hashes
from the metadata service (and the MS only relays signed hashes uploaded by other
user gateways).

As an optimization, replica gateways maintain an on-disk cache of serialized chunks.
Serving a cached serialized chunk does not involve the replica gateway driver at all.

On cache miss, however, the replica gateway must invoke its driver to fetch the
chunk from back-end storage and serialize it.  This is handled as follows:

* **Forward request.** The `syndicate-rg` process creates a
  `SG_messages::DriverRequest` structure describing the block or manifest
   requested, and forwards it to a `rg-driver` worker running with the `read`
   setting.

* **Load chunk.** When the `rg-driver` receives the `SG_messages::DriverRequest`
  from its parent `syndicate-rg` process, it invokes the `read_chunk()` method
  in the driver code with the request structure to actually fetch the data.
  The `rg-driver` process then replies the chunk data to `syndicate-rg`.

* **Serialize chunk.** Once the `syndicate-rg` process has the chunk, it passes
  it to an `rg-driver` process running with the `serialize` setting.  If the
  driver itself does not implement `serialize()`, then this is a no-op and the
  `rg-driver` is not invoked.

* **Cache serialized chunk.** The `syndicate-rg` process stores the serialized chunk to
  disk, so it can be served again on cache hit.

* **Reply chunk.** The `syndicate-rg` process replies the serialized chunk.

If there are any driver errors, then the `syndicate-rg` process replies HTTP 40x
(on invalid request or missing chunk) or HTTP 50x (on driver error).

## Deleting chunks

Replica gateways delete chunks at the request of user gateways.  They do so in
response to a signed HTTP POST request containing a `SG_messages::Request`
structure in the `control-plane` POST field.

The steps to delete a chunk are:

* **Forward request.** The `syndicate-rg` process creates a
  `SG_messages::DriverRequest` structure describing the block or manifest to
   delete, and forwards it to a `rg-driver` worker running with the `delete`
   setting.

* **Delete chunk.** The `rg-driver delete` worker calls `delete_chunk()` in the
  driver code, which should remove the chunk data from the backend service.

* **Uncache data and reply** On successful driver invocation, the `syndicate-rg` process
  uncaches the deleted data and replies an empty HTTP 200 response.

If there are any driver-related errors, then the `syndicate-rg` process still replies HTTP 200,
but with a serialized `SG_messages::Reply` structure that describes the error
details.  If there were any `syndicate-rg`-related errors, such as a `rg-drver`
crash or an invalid request, then `syndicate-rg` replies with an HTTP 40x or 50x
error.

## Renaming files

Some back-end services like iRODS are file-oriented, and need to preserve a
1-to-1 correspondance between the files and directories a user gateway writes
and the data stored in the back-end.  In such systems, the driver translates
chunk puts and deletes back into logical reads and writes on files in a
directory heirarchy.

To support this mode of operation, the replica gateway needs to be told when the
user gateway renames a file, so future chunk puts and deletes can be routed
correctly.  This is handled by an `rg-driver` process running with the `rename`
setting.

When the user gateway renames a file, it will send an HTTP POST request to the
replica gateway containing a serialized `SG_messages::Request` structure
describing the rename.  The replica gateway handles the request as follows:

* **Forward request.** The `syndicate-rg` process creates a
  `SG_messages::DriverRequest` structure describing the rename, and forwards it
   to a `rg-driver` worker running in the `rename` setting.

* **Rename file.** The `rg-driver` process calls the `rename_file()` driver
  method.

* **Reply status.** On successful driver invocation, the `syndicate-rg` process
  replies an empty HTTP 200 response.  No chunks are uncached, since the name 
  and path are not stored in blocks or manifests.

If there are any driver-related errors, then the `syndicate-rg` process still replies HTTP 200,
but with a serialized `SG_messages::Reply` structure that describes the error
details.  If there were any `syndicate-rg`-related errors, such as a `rg-drver`
crash or an invalid request, then `syndicate-rg` replies with an HTTP 40x or 50x
error.

## Example Implementation 

Please see the `disk` driver [here](https://github.com/syndicate-storage/syndicate-core/tree/master/python/syndicate/rg/drivers/disk).
