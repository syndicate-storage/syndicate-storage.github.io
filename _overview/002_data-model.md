---
layout: post
title: Data Model
---

Data in Syndicate is file-oriented.  Each file has a *manifest* and one or more
*blocks*.  A *manifest* represents an immutable snapshot of a file.  It contains
the list of blocks and their hashes, the time at which it was written, and some
additional versioning information used to query blocks.  A *block* is an
unstructured fixed-size set of bytes, linked to a manifest.

Collectively, manifests and blocks are two types of *chunk*.  A *chunk* is a
globally-unique, globally-addressible blob of data.  Chunks are immutable;
Syndicate handles writes by creating new chunks (new manifests and blocks), and
garbage-collecting old chunks that are no longer referenced.

Files in Syndicate are organized into *volumes*.  A *volume* is a logical
collection of data with a designated root directory.  Each gateway belongs to one
volume, and only gateways in the same volume can interact with the data and with
one another.

Volumes have a designiated owner, which can add and remove gateways, and change
gateway's privileges within the volume.
