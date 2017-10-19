---
layout: post
title: Deployment
---

Each Syndicate user gets one or more user gateways in order to access volumes.
A volume owner may opt to deploy one or more replica gateways to store users'
writes.  In addition, a volume owner may deploy one or more acquisition gateways
to expose datasets to users in a read-only fashion.

A volume may have at most one acquisition gateway.  This is by design, so
acquisition gateway driver developers do not need to reason about
write-conflicts.  In addition, a volume can only have an acquisition gateway if
it is an "archive"-type volume.  This means that only the acquisition gateway
may be a writer in this volume.

The reasoning here is that each dataset will belong to its own volume, and that
user-written data will be kept separately from datasets.
