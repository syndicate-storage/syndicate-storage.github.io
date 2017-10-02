---
layout: post
title: Data Transport
---

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
