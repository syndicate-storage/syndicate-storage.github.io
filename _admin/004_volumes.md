---
layout: post
title: Managing Volumes
---

{% include toc.html %}

## Volumes

All data storage in syndicate happens in a data volume.

## Permissions

Syndicate's volume model and use of a CDN to improve performance means that
it's permissions model works slightly differently than a conventional
filesystem.

Every Syndicate volume is managed by a Metadata Service (MS), and each volume
has an owner that is registered on that MS. There isn't a concept of groups on
the MS, other than whether the user has an account or not.  Anyone without an
account may be able to access volumes that allow anonymous access. 

This is most evident when FUSE mounting a Syndicate filesystem - the
traditional POSIX user/group/other permissions model maps as follows:

| POSIX | Syndicate                                         |
|-------|---------------------------------------------------|
| User  | Volume owner user account on the MS               |
| Group | All other user accounts on the same MS            |
| Other | Anonymous access to anyone who can contact the MS |

Every syndicate gateway runs using the account of a specific Syndicate user,
and all changes to the volume will be sent to the MS as that user.

There is no mapping between local users to Syndicate users - if you were to
mount a Syndicate volume on a multi-user system, and more than one local (to
that system) user were to write or make changes to data in the volume, all of
those actions would appear to the MS as having been performed by the Syndicate
user account which is running the gateway.

