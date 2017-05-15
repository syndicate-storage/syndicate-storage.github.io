---
layout: post
title: Developers Guide
---

Learn how contribute to Syndicate, including writing drivers that connect Syndicate to new data sources, storage services, and visualization tools.

# Overview

The "secret sauce" of Syndicate is its ability to be adapted to handle any
application's storage semantics with minimal changes.  It does so by processing
reads and writes as a sequence of "pipelines" that run through a set of
network-distributed "gateway" processes.

A "gateway" is a Syndicate process that handles reads and writes from
applications and other gateways.  An application reads and writes data through a
co-located gateway.  That gateway talks to other gateways in order to carry out
the request according to the application's expected storage semantics.

For example, Alice may run a gateway locally that implements a FUSE filesystem,
and Bob may run a gateway remotely that can load and store data to and from
Amazon S3.  Alice's application writes a lot of redundant data, so she programs
her gateway to deduplicate writes before making them persistent.

When Alice's application writes data, her gateway checks to see if the data has
never been written before.  If so, it sends it to Bob's
gateway, which will then send them to S3 for storage.  Later, when Alice's
application reads data, her gateway will pull data from Bob's gateway (which in
turn fetches it from S3).

The deduplication logic in Alice's gateway and the S3 interfacing logic in Bob's
gateway are both examples of gateway *drivers*.  Gateways are undifferentiated
processes; their application-specific functionality comes from the drivers they
run.

## Gateway Types

Syndicate offers three types of gateway: a **user gateawy**, a **replica
gateway**, and an **acquisition gateway**.

User gateways interface with the application, and run as co-located processes
alongside it.  The gateway presents a well-defined storage API to co-located
applications, such as a POSIX filesystem or an HTTP RESTful service.  When the
application reads data, the user gateway contacts other gateways to pull in the
requested chunks of data.  When the application writes data, it forwards the
written chunks to other gateways for storage.

Replica gateways interface with back-end read/write storage systems, like Amazon
S3.  They take chunks of data from user gateways and make them persistent, and
later serve the chunks back.

Acquisition gateways crawl external datasets and make them available to user
gateways in a read-only fashion.

## Data Model

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

### Data Transport

Syndicate operates by having gateways send and receive chunks from one another over HTTP.
Each chunk has a globally-unique URL, so any intermediate caching proxies can
safely intercept and service GET requests on chunks without leading to
inconsistency.  Gateways upload chunks to one another via authenticated HTTP
POST requests.

On read, a user gateway will fetch the manifest and blocks for the file from
either an acquisition gateway or a replica gateway (depending on which gateway
sources the file data).  It will do so by issuing an HTTP GET request to the
origin gateway for the manifest, and then it will issue multiple HTTP GET
requests for the blocks it needs.

On write, a user gateway will serialize and replicate a new manifest and the new
blocks to one or more replica gateways.

### Data Indexing

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

### Data Security

Each gateway belongs to one user and one volume, and has its own public/private
key pair.  The volume owner assigns gateways to users and volumes by signing a
certificate that lists the gateway's user ID, its volume ID, and its
capabilities.  The gateway user signs a certificate that pairs it to the volume
owenr's signature, as well as its host, port, and driver code hash.
Each gateway signs the manifests and metadata it replicates using its private
key.

Syndicate shares the set of public keys and certificates with its gateways, such
that all gateways in the same volume have a causally-consistent view of the
other gateways.  This way, a reader gateway will be able to verify that the
MS-served metadata record and manifest came from another gateway in the same
volume and came from the expected user.

Part of each gateway's certificate includes the hash of its driver.  When a
gateway receives its own certificate, it will download its driver, verify its
authenticity, and instantiate one or more driver chassis processes to execute
the driver code.  The driver code itself is stored on the MS by the user.

## Deployment

Each Syndicate user gets one or more user gateways in order to access volumes.
A volume owner may opt to deploy one or more replica gateways to store users'
writes.  In addition, a volume owner may deploy one or more acquisition gateways
to expose datasets to users in a read-only fashion.

A volume may have at most one acquisition gateway.  This is by design, so
acquisition gateway driver developers do not need to reason about
write-conflicts.  In addition, a volume can only have an acquisition gateway if
it is an "archive"-tyep volume.  This means that only the acquisition gateway
may be a writer in this volume.

The reasoning here is that each dataset will belong to its own volume, and that
user-written data will be kept separately from datasets.

## Drivers

Gateways implement logic to interact with the MS, to process
manifests, blocks, and other gateway certificates, and to send and receive data
to and from other gateways.  However, they do not implement code to interact
with specific back-end data services or data sets, and do not implement code to
interact with intermediary CDNs.  This is instead handled by the driver code.

Each driver is comprised of three files:

* the code that implements the gateway's driver methods (the `driver` file).

* a publicly-readable configuration structure (the `config` file).

* a set of gateway secrets that will be encrypted with the
  gateway's public key and readable only to the recipient gateway (the `secrets`
file).

The `config` and `secrets` files contain JSON objects.  The `driver` file is a
Python source file that implements the requisite methods.

### Driver Programming

The driver programming model is multi-process, and is comprised of multiple
workers.  Each worker will call only one of the driver's methods, and workers
**do not share state** with one another.  This design was
chosen specifically to (1) isolate the gateway process from driver bugs,
and (2) allow gateways to start more workers to handle higher
load.

This is acceptable because consistency is handled by the gateways themselves.
Each chunk is immutable, and there is
no need for replica gateways to handle concurrent
requests in any particular order (so there does not need to be any shared state
between concurrent method invocations).

However, if shared state is needed for some reason, it is up to the driver code
to set up its own inter-process communication.  For example, the driver code running in
worker instances can communicate locally via shared files, shared named
pipes, etc.  This is considered to be the exception rather than the rule,
however, so you should think carefully about whether or not this is necessary in
your driver design.

### Data Serialization

All gateway drivers implement a `serialize` and `deserialize` method.
Chunks are serialized by a sender gateways' drivers before being sent over the
network, and are deserialized by receiver gateways' drivers before being consumed.
The serialization/deserialization steps are meant to give the gateways a chance
to make data suitable for transmission and caching (e.g. encrypting it, giving it a canonical
encoding, compressing it, etc.).

Explicitly handling serialization and deserialization is optional.

### Caching

As an optimization, each gateway maintains an on-disk cache of serialized
chunks.  Replica and acquisition gateways can service HTTP requests from this 
cache without invoking their drivers.  Similarly, user gateways can service read
requests from this cache without contacting replica or acquisition gateways.

On cache hit, these chunks will be passed through their gateway's driver's `deserialize()`
method before being processed (if the driver implements `deserialize()`).

### Common Driver Arguments

Most driver methods take a `request`, a `driver_config`, and a `driver_secrets`
argument.

* `request` is a `SG_messages::Request` instance.  Fields within it can be
  queried with methods in `syndicate.util.gateway`.

* `driver_config` is a `dict` that contains the publicly-visible driver
  configuration.  It is loaded from the driver's `config` file.

* `driver_secrets` is a `dict` that contains private driver configuration.  It
  is loaded from the driver's `secrets` file.

