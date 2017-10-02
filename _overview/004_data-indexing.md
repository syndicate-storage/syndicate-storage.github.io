---
layout: post
title: Data Indexing
---

Gateways announce file updates via a shared *metadata service* (MS).  The
*metadata service* is an cloud-hosted read/write index that stores the system's
directory hierarchy.

After a user gateway creates or updates a file and replicates its data, it will
upload new metadata to the MS.  This metadata tells other gateways where the
file's chunks can be found, and how to construct globally-unique URLs to each
of them.  When other user gateways go to read the file, they first query the MS for
the manifest metadata, and then proceed to fetch the manifest and blocks from
the appropriate gateway.

Since Syndicate is file-oriented, user gateways resolve data using paths.  The
MS maintains all of the system's volume and directory state and handles path resolution on
their behalf.
