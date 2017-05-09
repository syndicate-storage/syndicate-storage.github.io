---
layout: post
title: Syndicatefs
---

{% include toc.html %}

`syndicatefs` is a **Syndicate User Gateway (UG)** that implements a FUSE
filesystem interface.  It lets you mount a Syndicate volume as a local
filesystem.

## Prerequisites

You must first [Install Syndicate](/install).

If you do not have a user certificate and a gateway certificate, follow [Create
a User](#) and [Create a gateway](#) guide.


## Installation

Once Syndicate is installed, type:
```
apt-get install syndicate-core syndicatefs
```

## Run

An example `syndicatefs` UG command:

```
syndicatefs -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY -d3 $MOUNT_PATH
```

Once the volume is successfully mounted, the volume's data will be available at `$MOUNT_PATH`.

## Syndicatefs Options

* `-f`: run in foreground
* `-d$DEBUG_LEVEL`: set a debug level. For example, `-d3` will set the debug
level to 3 (very verbose).
* `-u $SYNDICATE_USER`: set a user. For `anonymous` access, use `ANONYMOUS`.
* `-v $SYNDICATE_VOLUME`: set a volume.
* `-g $SYNDICATE_GATEWAY`: set a gateway.
