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
| `syndicate-stat` | `stat` | Show status of a file/directory |
| `syndicate-listxattr` | `xattr` | List extended attributes of a file/directory |
| `syndicate-getxattr` | `xattr -p` | Show an extended attribute of a file/directory |
| `syndicate-setxattr` | `xattr -w` | Set an extended attribute to a file/directory |
| `syndicate-removexattr` | `xattr -d` | Remove an extended attribute of a file/directory |
| `syndicate-coord` | None | Show a coordinator Syndicate Gateway of a file/directory |
| `syndicate-read` | None | Read file content of arbitrary range |
| `syndicate-write` | None | Write file content |
| `syndicate-get` | None | Get a file and store to local |
| `syndicate-put` | None | Put a local file to a Syndicate Volume |
| `syndicate-refresh` | None | Refresh TTL of a file/directory |
| `syndicate-repl` | None | |
| `syndicate-vacuum` | None | |

## Run

An example command displaying content of `/subdir1` directory is:

```
syndicate-ls -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /subdir1
```

### Options

Following options are common for all commands.

* `-d$DEBUG_LEVEL`: set a debug level. For example, `-d3` will set the debug
level to 3 (very verbose).
* `-u $SYNDICATE_USER`: set a user. For `anonymous` access, use `ANONYMOUS`.
* `-v $SYNDICATE_VOLUME`: set a volume.
* `-g $SYNDICATE_GATEWAY`: set a gateway.

### Command Examples

#### syndicate-cat 
`syndicate-cat` displays content of a file. The command takes a Syndicate file 
path as an argument.

Following command displays content of `/syndicate_file.txt` Syndicate file.
```
syndicate-cat -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /syndicate_file.txt
```

#### syndicate-ls
`syndicate-ls` lists content of a directory. The command takes a Syndicate directory 
path as an argument.

Following command displays content (sub-directories and files) under `/syndicate_dir`
Syndicate directory.
```
syndicate-ls -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /syndicate_dir
```

#### syndicate-rename
`syndicate-rename` renames a file/directory. Th command takes two Syndicate 
file/directory paths as arguments. A file/directory given to the first argument 
is renamed (or moved) to the second argument.

Following command renames a file `/syndicate_file1` to `/syndicate_file2`.
```
syndicate-rename -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /syndicate_file1 /syndicate_file2
```

Following command moves a file `/syndicate_file1` to a directory `/dir1`.
```
syndicate-rename -u $SYNDICATE_USER -v $SYNDICATE_VOLUME -g $SYNDICATE_GATEWAY /syndicate_file1 /dir1/
```

#### syndicate-touch

#### syndicate-trunc

#### syndicate-unlink

#### syndicate-mkdir

#### syndicate-rmdir

#### syndicate-stat

#### syndicate-listxattr

#### syndicate-getxattr

#### syndicate-setxattr

#### syndicate-removexattr

#### syndicate-coord

#### syndicate-read

#### syndicate-write

#### syndicate-get

#### syndicate-put

#### syndicate-refresh

#### syndicate-repl

#### syndicate-vacuum
