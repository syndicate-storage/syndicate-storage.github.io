---
layout: post
title: Setup Syndicate Tool
---

{% include toc.html %}

`syndicate` is an administration tool.  It lets you control Syndicate users, 
volumes and gateways.

## Prerequisites

You must first [Install Syndicate](/install).

If you do not have an admin user certificate or a user certificate, ask to 
your Metadata Service operator for it.

## Setup

Setting up the Syndicate Tool is an one-time task per a user and a machine.

```
syndicate -d --trust_public_key setup $SYNDICATE_USER $USER_CERT_PATH $MS_HOST
```

Parameters:
- `$SYNDICATE_USER`: the user name on the MS
- `$USER_CERT_PATH`: certificate file path
- `$MS_HOST`: URL of the MS

e.g.

```
syndicate -d --trust_public_key setup iychoi@email.arizona.edu ./iychoi@email.arizona.edu.pkey http://syndicate-ms-datasets-prod.appspot.com
```
