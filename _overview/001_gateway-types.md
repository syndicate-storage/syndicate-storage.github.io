---
layout: post
title: Gateway Types
---

Syndicate offers three types of gateways: a **user gateway**, a **replica
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
