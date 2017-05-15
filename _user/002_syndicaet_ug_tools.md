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

| **Command** | **Corresponding UNIX command** | **Description** |
| -------------| ----------- | ----------- |
| `syndicate-cat` | `cat`  | Show file content |
| `syndicate-ls` | `ls`   | List directory content |
| `syndicate-rename` | `mv` | Rename a file/directory |
| `syndicate-touch` | `touch` | Create an empty file |
| `syndicate-trunc` | `truncate` | Truncate a file |
| `syndicate-unlink` | `rm` | Remove a file |
| `syndicate-mkdir` | `mkdir` | Make a directory |
| `syndicate-rmdir` | `rmdir` | Remove a directory |
| `syndictae-stat` | `stat` | Show status of a file/directory |
| `syndicate-listxattr` | `xattr` | List extended attributes of a file/directory |
| `syndicate-getxattr` | `xattr -p` | Show an extended attribute of a file/directory |
| `syndicate-setxattr` | `xattr -w` | Set an extended attribute to a file/directory |
| `syndicate-removexattr` | `xattr -d` | Remove an extended attribute of a file/directory |
| `syndictae-coord` | None | Show a coordinator Syndicate Gateway of a file/directory |
| `syndicate-read` | None | Read file content of arbitrary range |
| `syndicate-write` | None | Write file content |
| `syndicate-get` | None | Get a file and store to local |
| `syndicate-put` | None | Put a local file to a Syndicate Volume |
| `syndicate-refresh` | None | Refresh TTL of a file/directory |
| `syndicate-repl` | None | |
| `syndicate-vacuum` | None | |

## Run

An example command is:

```
syndicate-ls -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /subdir1
```

This displays content of `/subdir1` directory. 

## Common Options

* `-d$DEBUG_LEVEL`: set a debug level. For example, `-d3` will set the debug
level to 3 (very verbose).
* `-u $SYNDICATE_USER`: set a user. For `anonymous` access, use `ANONYMOUS`.
* `-v $SYNDICATE_VOLUME`: set a volume.
* `-g $SYNDICATE_GATEWAY`: set a gateway.
