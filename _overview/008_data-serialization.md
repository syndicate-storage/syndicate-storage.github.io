---
layout: post
title: Data Serialization
---

All gateway drivers implement a `serialize` and `deserialize` method.
Chunks are serialized by a sender gateways' drivers before being sent over the
network, and are deserialized by receiver gateways' drivers before being consumed.
The serialization/deserialization steps are meant to give the gateways a chance
to make data suitable for transmission and caching (e.g. encrypting it, giving it a canonical
encoding, compressing it, etc.).

Explicitly handling serialization and deserialization is optional.
