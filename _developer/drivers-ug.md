---
layout: post
title: User Gateway Drivers
---

User Gateway Drivers
====================

{% include toc.html %}

User gateways implement HTTP clients.
When an application reads data, the user gateway issues an HTTP GET to one or
more gateways to fetch the relevant chunks.  When an application writes data,
the user gateway issues an HTTP POST to one or more gateways to save the data
(as new chunks).  User gateways interact with other gateways via control-plane
messages, but these are not processed by the driver.

Besides the standard `serialize` and
`deserialize` methods, the only method a user gateway driver implements is a
method to translate a gateway-generated HTTP URL into a driver-specific HTTP URL.
This is meant to give the user
gateway a chance to decide which gateways or intermediate services (like
CDNs) to use for fetching a chunk.  While Syndicate gateways use a standard URL
format for addressing chunks of data, the intermediate
networks that serve these chunks may require a specific URL format in order to
be used.  For example, a driver that fetches chunks from the [Coral
CDN](http://www.coralcdn.org) would need to append `.nyud.net` to the URL's
host.

User gateway drivers are implemented within stand-alone chassis processes.  When
a user gateway starts, it instantiates one or more `ug-driver` subprocesses to
execute a signle driver method:

* `ug-driver cdn_url` will accept a standard data-plane URL
from the running gateway, and return a URL to the data using the driver's
`cdn_url()` method.
* `ug-driver serialize` will accept a chunk from the running gateway, and return
a serialized chunk using the driver's `serialize()` method.
* `ug-driver deserialize` will accept a serialized chunk from the running gateway,
and return a deserialized (application-readable) chunk using the `deserialize()`
method in the driver.

Each `ug-driver` process loads the gateway owner's driver, configuration, and
secrets.  Depending on the mode of operation, only one of the driver methods
will be called:

| **Driver chassis mode** | **Driver method called**              |
| ----------------------- | --------------------------------------|
| `ug-driver cdn_url`     | `cdn_url(request, syndicate_url)`     |     
| `ug-driver serialize`   | `serialize(request, chunk)`           |
| `ug-driver deserialize` | `deserialize(request, chunk)`         |

Each `ug-driver` chassis process is long-lived--it will run until the
user gateway process terminates, or until the gateway owner changes the
driver code (in which case the user gateway process stops them and re-starts
them with the new code).

## URL Format

User gateways address manifests and blocks as follows.

A manifest URL has the format:

```
    http://${gateway_host}:${gateway_port}/DATA/${volume_id}/${fs_path}.${file_id_hex}.${version}/manifest.${timestamp_sec}.${timestamp_nsec}
```

Where:
   * `${gateway_host}` is the hostname of the gateway
   * `${gateway_port}` is the port number of the gateway
   * `${volume_id}` is the volume ID (a base-10 unsigned 64-bit number)
   * `${fs_path}` is the absolute path in the filesystem to the file.
   * `${file_id_hex}` is the file ID (a base-16 unsigned 64-bit number)
   * `${version}` is the file version (a base-10 signed 64-bit number)
   * `${timestamp_sec}` is the "seconds" field of the manifest creation date (a
     base-10 signed 64-bit number)
   * `${timestamp_nsec}` is the "nanoseconds" field of the manifest creation date (
     base-10 unsigned 32-bit number)

A block URL has the format:

```
    http://${gateway_host}:${gateway_port}/DATA/${volume_id}/${fs_path}.${file_id_hex}.${version}/${block_id}.${block_version}
```

Where:
   * `${block_id}` is the ID of the block in the file (a base-10 unsigned 64-bit
     number)
   * `${block_version}` is the version number of the block (a base-10 signed
     64-bit number).


### Interpreting the URL fields

The fields `${timestamp_sec}` and `${timestamp_nsec}` in the manifest URL
increment monotonically each time a file is written and published.  They are set
by the user gateway executing the write, and are discovered and used by user
gateways to read the file.  The MS stores the current manifest timestamp.

The `${version}` field increments monotonically every time the file is truncated
or renamed, and whenever the file's coordinator gateway changes.

The `${block_version}` field is randomly-generated each time the block contents
change.  It is derived from the manifest itself for the file.

The fields `${volume_id}` and `${file_id_hex}` are consistent throughout the
file's lifetime.

The field `${fs_path}` will change only when the file is renamed.

## Generating URLs

The user gateway code will send either a manifest URL or a block URL to the
`ug-driver` process just before it issues an HTTP GET request with it.  One of
the `ug-driver cdn_url` driver chassis processes will be selected, and its
`cdn_url()` method will be called.

The `cdn_url()` method takes a `SG_messages::DriverRequest` object and the URL
as its arguments.  The driver request object contains the parsed fields of the
URL, which the driver code can access via the methods in the
`syndicate.util.gateway` package.

The method must return a URL on success, or `None` on error.  If the
method returns `None`, then the user gateway's read operation fails.


