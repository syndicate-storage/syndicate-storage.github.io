---
layout: post
title: CDNs
---

## Content Delivery Networks

A Content Delivery Network (CDN) is a system of distributed servers that deliver Web content to a user primarily based on geographic location in order to improve data transfer time.  Because of Syndicate's unique caching design, Syndicate is able to operate and efficiently manage cache needs within the [OpenCloud](http://www.opencloud.us) CDN (also see [Internet2](https://www.internet2.edu)).  In this context, Syndicate can provide services that are unique only to Syndicate, such as replication and partial file transfers/caching.  For example, if data exists on a CDN node in Hawaii and a user on the East coast accesses the data via Syndicate, the data would be cached in multiple locations (closer and local to the user) while it is being transferred.  The next time the same data is accessed, Syndicate can provide the data from one of the various locations where it was replicated/cached, thus drastically improving transfer time.  In a real-life test, a file that originally took 80+ seconds to transfer from Hawaii was later redownloaded and took less than 0.4 seconds.  In this case, Syndicate recognized that the data was already stored in local cache.  After clearing the local cache, and downloading the file again, the same file took about 4 seconds to transfer.  This time the file was cached at a nearby CDN node.  Furthermore, if a user were to change a small portion of the same file, Syndicate knows to only re-transfer and update the chunk(s) of data that were modified rather than transferring the entire file.
