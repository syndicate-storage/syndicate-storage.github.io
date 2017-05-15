Acquisition Gateway Drivers
===========================

Acquisition gateway drivers are *file-oriented* and *read-only*.  They respond
to HTTP GET requests for file data, which they service by synchronously fetching
byte ranges from a back-end data set.  Unlike other drivers, these drivers also
proactively crawl their back-end dataset and instruct the gateway to publish or
remove files and directories.

Acquisition gateways are implemented as a single server process (`syndicate-ag`)
and one or more driver chassis subprocesses (`ag-driver`) that do the actual work.  Each
`ag-driver` instance has a single responsibility, outlined below:

* `ag-driver read` will be used to load chunks from the back-end dataset.

* `ag-driver crawl` will be used to proactively index the data set and feed the
  `syndicate-ag` process with a sequence of files and directories to publish,
  update, or delete.  In addition, it will take requests from `syndicate-ag` to
  re-crawl a particular file or directory.

* `ag-driver serialize` will be used to transform a chunk to make it suitable
  for network transmission (e.g. compress it, encrypt it, etc.).

Each `ag-driver` process loads the gateway owner's driver, configuration, and
secrets.  Depending on the mode of operation, only one of the driver methods
will be called:

| **Driver chassis mode** | **Driver method called** |
| ----------------------- | ------------------------ |
| `ag-driver read`        | `read(request, chunk_fd, driver_config, driver_secrets)`           |
| `ag-driver crawl`       | `next_dataset(driver_config, driver_secrets)`, |
|                         | `refresh(request, driver_config, driver_secrets)` |
| `ag-driver serialize`   | `serialize(request, chunk)`            |


Each `ag-driver` chassis process is long-lived--it will run until the
`syndicate-ag` process terminates, or until the gateway owner changes the driver
code (in which case the `syndicate-ag` process stops them and re-starts them
with the new code).

## Data Model

Acquisition gateways support loading blocks on-the-fly, without requiring any
pre-processing.  This is meant to support datasets where the contents of the
files will not be known until read time.

To handle this, the acquisition gateway generates manifests on-the-fly for
its files.  It does not necessarily know what the block hashes will be
in advance (i.e. this information may not be known until read).  Instead,
manifests from acquisition gateways take on a slightly different structure and
validation algorithm.  The gateway signs the blocks on-the-fly, and tags their
entries in their manifest to instruct the reader to authenticate blocks as they
are received using the signature.

## Deployment Model

A volume can have at most acquisition gateway, and only an "archive"-type volume
may have acquisition gateways.  This is so driver
developers do not need to worry about handling write-conflicts from multiple
gateways.

## Sending Chunks

Acquisition gateways serve data to anyone who asks via HTTP GET requests.  GET
requests are not authenticated, since it is expected that untrusted
intermediaries like CDNs will be requesting chunks on behalf of user gateways.
This is okay, however, since the acquisition gateway signs its manifests and
blocks, and the user gateway reading them already has the acquisition gateway's
public key.

When `syndicate-ag` receives an HTTP GET request for a manifest or block, it
will invoke one of the `ag-driver read` workers to fetch it, and use an
`ag-driver serialize` worker to make it safe for transmission (if the driver
implements the `serialize()` method).

A GET request can be for a manifest or a block.

### Sending Manifests

The `syndicate-ag` process generates manifests automatically.  When it receives
an HTTP GET request for a manifest, it will *generate a new manifest for the
file* and send it back (piping it through the `ag-driver serialize` worker on the
way).  The block addresses calculated from this manifest will be unique to this
GET request (instead of unique to a file write).  This is to accomodate the fact
that the file's data may silently change across multiple requests.  The
acquisition gateway pessimistically assumes that the backend data is always
changing.

### Sending Blocks

As an optimization, acquisition gateways maintain an on-disk cache of serialized chunks.
Serving a cached serialized chunk does not involve the acquisition gateway driver at all.

On cache miss, however, the acquisition gateway must invoke its driver to fetch the
chunk from the back-end data set and serialize it.  This is handled as follows:

* **Forward request.** The `syndicate-ag` process creates a
  `SG_messages::DriverRequest` structure describing the block
   requested, and forwards it to a `ag-driver` worker running with the `read`
   setting.

* **Load chunk.** When the `ag-driver` receives the `SG_messages::DriverRequest`
  from its parent `syndicate-ag` process, it invokes the `read()` method
  in the driver code with the request structure to actually fetch the data.
  The `ag-driver` process then replies the chunk data to `syndicate-rg`.

* **Serialize chunk.** Once the `syndicate-ag` process has the chunk, it passes
  it to an `ag-driver` process running with the `serialize` setting.  If the
  driver itself does not implement `serialize()`, then this is a no-op and the
  `ag-driver` is not invoked.

* **Cache serialized chunk.** The `syndicate-ag` process stores the serialized chunk to
  disk, so it can be served again on cache hit.

* **Reply chunk.** The `syndicate-ag` process signs and replies the serialized chunk.

If there are any driver errors, then the `syndicate-ag` process replies HTTP 40x
(on invalid request or missing chunk) or HTTP 50x (on driver error).

## Crawling Datasets

Acquisition gateway drivers additionally help the gateway crawl its back-end
data set, so it can publish the files and directories it represents.  The driver
gets to choose the file and directory structure, so long as it can map requests
for files and directories to the appropriate data set records.

The `ag-driver` chassis runs a background thread that repeatedly calls
`next_dataset()`.  The `next_dataset()` is a "generator-like" method that emits
an infinite stream of commands to the acquisition gateway.  That is, the
acquisition gateway driver chassis repeatedly calls `next_dataset()` to get a command
to create, update, or delete a file or directory on the MS.
The `next_dataset()` implementation should block until it has a
command available.

The method must return `True` if there are more commands, and `False` if there
are not.  The method yields commands to the acquisition gateway by calling
`syndicate.util.gateway.crawl()` for each command (this method serves the same
purpose as `yield` does in a traditional Python generator).

For example, a driver that makes a directory tree on local disk available via an
acquisition gateway would simply walk the directory tree and emit a command to
publish each file or directory visited.  Each call to `next_dataset()` would
get the command to create the next file or directory.  Once
all files and directories have been visited, `next_dataset()` would block.  The driver
implementation may opt to watch the files and directories on disk that have been
published, so it can queue "update" and "delete" commands for `next_dataset()` to return in 
order to handle the cases where the on-disk directory tree is modified.

If the `next_dataset()` implementation decides that there are no more commands
for it to yield to the acquisition gateway, it may instruct the acquisition
gateway to stop with a "finish" command.

Driver implementations can use the
`syndicate.util.gateway.make_metadata_command()` method to generate commands for
`next_dataset()` to return.  The method requires:

* the command to execute on the MS ("create", "update", "delete", or "finish")

* the type of inode being affected ("file", "directory")

* the size of the data (only for "file", and only for "create" and "update")

* the POSIX permission bits (only read/execute flags will be honored)

* the path on the MS 

* optional: the TTL for the metadata (defaults to 5 seconds)

## Example

This snippit will cause the acquisition gateway to set up its volume with one
22-byte file at `/hello`.  It will also create the directory `/goodbye`, and
then delete it.

```Python
from syndicate.util.gateway import make_metadata_command, crawl

# root directory of the volume
cmd = make_metadata_command("create", "directory", 0555, None, "/")
crawl(cmd)

# add 11-byte file at /hello
cmd = make_metadata_command("create", "file", 0444, 11, "/hello")
crawl(cmd)

# add directory at /goodbye
cmd = make_metadata_command("create", "directory", 0555, None, "/goodbye")
crawl(cmd)

# expand /hello to 22 bytes
cmd = make_metadata_command("update", "file", 0444, 22, "/hello")
crawl(cmd)

# delete directory /goodbye
cmd = make_metadata_command("delete", "directory", None, None, "/goodbye")
crawl(cmd)

# no more commands from the driver
cmd = make_metadata_command("finish", None, None, None, None)
crawl(cmd)
```

A working example that causes the acquisition gateway to serve data out of a
directory on-disk can be found
[here](https://github.com/syndicate-storage/syndicate-core/tree/master/python/syndicate/ag/drivers/disk).
