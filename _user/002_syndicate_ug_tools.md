---
layout: post
title: Syndicate UG Tools
---

{% include toc.html %}

`Syndicate UG Tools` is a collection of commands that are used to access or 
manipulate Syndicate data. Each command is an **Syndicate User Gateway (UG)** 
and very similar to Unix utilities. 

## Prerequisites

You must first [Install Syndicate](/install).

If you do not have a user certificate and a gateway certificate, follow [Create
a User](#) and [Create a gateway](#) guide.


## Installation

Once Syndicate is installed, type:
```
apt-get install syndicate-ug-tools
```

## Available Commands

`Syndicate UG Tools` offers following commands:

| **Command**<br>*(click to view Man Pages)* | **Corresponding UNIX command** | **Description** |
| -------------| ----------- | ----------- |
| [syndicate-cat](https://butler.opencloud.cs.arizona.edu/docs/syndicate-cat.html) | `cat`  | Show file content |
| [syndicate-ls](https://butler.opencloud.cs.arizona.edu/docs/syndicate-ls.html) | `ls`   | List directory content |
| [syndicate-rename](https://butler.opencloud.cs.arizona.edu/docs/syndicate-rename.html) | `mv` | Rename a file/directory |
| [syndicate-touch](https://butler.opencloud.cs.arizona.edu/docs/syndicate-touch.html) | `touch` | Create an empty file |
| [syndicate-trunc](https://butler.opencloud.cs.arizona.edu/docs/syndicate-trunc.html) | `truncate` | Truncate a file |
| [syndicate-unlink](https://butler.opencloud.cs.arizona.edu/docs/syndicate-unlink.html) | `rm` | Remove a file |
| [syndicate-mkdir](https://butler.opencloud.cs.arizona.edu/docs/syndicate-mkdir.html) | `mkdir` | Make a directory |
| [syndicate-rmdir](https://butler.opencloud.cs.arizona.edu/docs/syndicate-rmdir.html) | `rmdir` | Remove a directory |
| [syndicate-stat](https://butler.opencloud.cs.arizona.edu/docs/syndicate-stat.html) | `stat` | Show status of a file/directory |
| [syndicate-listxattr](https://butler.opencloud.cs.arizona.edu/docs/syndicate-listxattr.html) | `xattr` | List extended attributes of a file/directory |
| [syndicate-getxattr](https://butler.opencloud.cs.arizona.edu/docs/syndicate-getxattr.html) | `xattr -p` | Show an extended attribute of a file/directory |
| [syndicate-setxattr](https://butler.opencloud.cs.arizona.edu/docs/syndicate-setxattr.html) | `xattr -w` | Set an extended attribute to a file/directory |
| [syndicate-removexattr](https://butler.opencloud.cs.arizona.edu/docs/syndicate-removexattr.html) | `xattr -d` | Remove an extended attribute of a file/directory |
| [syndicate-coord](https://butler.opencloud.cs.arizona.edu/docs/syndicate-coord.html) | None | Show a coordinator Syndicate Gateway of a file/directory |
| [syndicate-read](https://butler.opencloud.cs.arizona.edu/docs/syndicate-read.html) | None | Read file content of arbitrary range |
| [syndicate-write](https://butler.opencloud.cs.arizona.edu/docs/syndicate-write.html) | None | Write file content |
| [syndicate-get](https://butler.opencloud.cs.arizona.edu/docs/syndicate-get.html) | None | Get a file and store to local (similar to *_ftp_* `get`) |
| [syndicate-put](https://butler.opencloud.cs.arizona.edu/docs/syndicate-put.html) | None | Put a local file to a Syndicate Volume (similar to *_ftp_* `put`) |
| [syndicate-refresh](https://butler.opencloud.cs.arizona.edu/docs/syndicate-refresh.html) | None | Refresh TTL of a file/directory |
| [syndicate-repl](https://butler.opencloud.cs.arizona.edu/docs/syndicate-repl.html) | None | Replay a series of commands from a syndicate script |
| [syndicate-vacuum](https://butler.opencloud.cs.arizona.edu/docs/syndicate-vacuum.html) | None | Remove unlinked blocks and manifests |

## Run

An example command displaying content of `/subdir1` directory is:

```
syndicate-ls -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /subdir1
```

More examples are available in the [man pages](https://butler.opencloud.cs.arizona.edu/docs/pages.html).

### Options

Following options are common for all commands.

* `-d$DEBUG_LEVEL`: set a debug level. For example, `-d3` will set the debug
level to 3 (very verbose).
* `-u $SYNDICATE_USER`: set a user. For `anonymous` access, use `ANONYMOUS`.
* `-v $SYNDICATE_VOLUME`: set a volume.
* `-g $SYNDICATE_GATEWAY`: set a gateway.

