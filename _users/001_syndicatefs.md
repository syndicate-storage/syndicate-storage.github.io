---
layout: post
title: Syndicatefs
---

Syndicatefs
===========

`syndicatefs` is a **Syndicate User Gateway** that implements a FUSE filesystem. 
It lets you mount a Syndicate volume in your filesystem hierarchy.


## Prerequisite
If `Syndicate PPA` is not installed at your system, follow [Syndicate PPA](../000_setup_syndicate_ppa/) guide 
first.
If you do not have a user certificate and a gateway certificate, follow [Create a user](#) and [Create a gateway](#) guide.


## Installation
Once `Syndicate PPA` is installed, type:
```
apt-get install syndicate-core syndicatefs
```


## Run

An example of `syndicatefs` command will be:
```
syndicatefs -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY -d3 $MOUNT_PATH
```

Once `syndicatefs` is successfully mounted, you will be able to access data of 
in the volume under the `$MOUNT_PATH`.


### Options

* `-f`: run in foreground
* `-d$DEBUG_LEVEL`: set a debug level. For example, `-d3` will set the debug
level to 3 (very verbose).
* `-u $SYNDICATE_USER`: set a user. For `anonymous` access, use `ANONYMOUS`.
* `-v $SYNDICATE_VOLUME`: set a volume.
* `-g $SYNDICATE_GATEWAY`: set a gateway.
