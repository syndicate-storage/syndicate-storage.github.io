---
layout: post
title: Drivers
---

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

#### Multi-process Drivers

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

#### Common Driver Arguments

Most driver methods take a `request`, a `driver_config`, and a `driver_secrets`
argument.

* `request` is a `SG_messages::Request` instance.  Fields within it can be
  queried with methods in `syndicate.util.gateway`.

* `driver_config` is a `dict` that contains the publicly-visible driver
  configuration.  It is loaded from the driver's `config` file.

* `driver_secrets` is a `dict` that contains private driver configuration.  It
  is loaded from the driver's `secrets` file.
