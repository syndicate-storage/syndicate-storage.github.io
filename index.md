---
layout: post
title: Syndicate
---

**A Scalable Data Storage and Delivery Platform**

Syndicate is a general-purpose data delivery platform that harnesses a collection of existing storage components to provide a global, scalable, and secure storage service. These include public and private cloud storage (for data durability), network caches and content distribution networks (for scalable read bandwidth), and local disks (for local read/write performance). Syndicate's goal is to allow applications to access data independent of where it is stored, while both minimizing the operational overhead imposed on users and maximizing the use of commodity infrastructure.

There are multiple ways to use and contribute to Syndicate. This guide describes four:
 - **Users:** Mount and access existing Syndicate volumes.
 - **Administrators:** Create and manage Syndicate volumes.
 - **Developers:** Contribute drivers that connect Syndicate to external stores.
 - **Operators:** Deploy and run the Syndicate service on behalf of a user community.
 
These four roles are sometimes conflated (e.g., users can administer their own volumes, admins can also operate the service), but they represent four different stakeholders, and so this guide is organized on that basis.
