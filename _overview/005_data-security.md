---
layout: post
title: Data Security
---

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
