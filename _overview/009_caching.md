---
layout: post
title: Caching
---

As an optimization, each gateway maintains an on-disk cache of serialized
chunks.  Replica and acquisition gateways can service HTTP requests from this 
cache without invoking their drivers.  Similarly, user gateways can service read
requests from this cache without contacting replica or acquisition gateways.

On cache hit, these chunks will be passed through their gateway's driver's `deserialize()`
method before being processed (if the driver implements `deserialize()`).
