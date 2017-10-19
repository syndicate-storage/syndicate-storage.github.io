---
layout: post
title: Managing Syndicate Services
---

{% include toc.html %}

As previously described in the Overview section, Syndicate can be composed of four primary parts:

 - **Metadata Service** - manages volumes and users, runs in a central location

 - **Gateway** - multiple types that are run as a user and interacting with
   data in volumes

 - **Content Delivery Network** - caches data in volumes

 - **Administrative tools** - interacts with the MS to creating volumes, manage users, etc.


## Metadata Service

The metadata service is designed to run on Google's AppEngine, and can be run either on that cloud infrastructure, or locally on the GAE development environment.

In many cases, you may not need to run a Metadata Service, and may be able to use one provided by [OpenCloud](http://opencloud.us). 

## Gateways

Syndicate uses 3 kinds of gateways to get data into and out of volumes:

 - **User Gateways (UGs)** - The most common, which perform read and write operations on behalf of a user.

 - **Replica Gateways (RGs)** - Work with a backend storage system to store data

 - **Aquisition Gateways (AGs)** - Acquires data from another data management system

## Content Delivery Network

Used to accelerate data access to volumes, in the OpenCloud deployment scenario.  By placing a CDN node on your network (as a part of the OpenCloud project), data is kept closer to where it is used, which improves data access latency and overall bandwidth.

## Administrative tools

### `syndicate` command

The primary administrative tool is named `syndicate`, and is used to manage the Metadata Service (MS), users, volumes, and gateways.



