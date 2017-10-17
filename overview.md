---
layout: post
title: Overview
---


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

<br>

[Gateway Types](https://syndicate-storage.github.io/overview/001_gateway-types/)<br>
[Data Model](https://syndicate-storage.github.io/overview/002_data-model/)<br>
[Data Transport](https://syndicate-storage.github.io/overview/003_data-transport/)<br>
[Data Indexing](https://syndicate-storage.github.io/overview/004_data-indexing/)<br>
[Data Security](https://syndicate-storage.github.io/overview/005_data-security/)<br>
[Deployment](https://syndicate-storage.github.io/overview/006_deployment/)<br>
[Drivers](https://syndicate-storage.github.io/overview/007_drivers/)<br>
[Data Serialization](https://syndicate-storage.github.io/overview/008_data-serialization/)<br>
[Caching](https://syndicate-storage.github.io/overview/009_caching/)<br>
