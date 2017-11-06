---
layout: post
title: Syndicate Admin Tool
---

`syndicate` is an administration tool.  It is used to manage the Metadata Service (MS), users, volumes, and gateways.

It has the following options:

 - `setup`, which creates an administrative config directory structure
 - User management: `create_user`, `delete_user`, `list_users`
 - Volume management: `create_volume`, `delete_volume`, `list_volumes`
 - Gateway management: `create_gateway`, `update_gateway`, `delete_gateway`,
   `list_gateways`

## Prerequisites

You must first [Install Syndicate](/install).

You will also need an admin user certificate or a standard user certificate.  Please read about [Certificates](/certificates) for more information.

## Setup

Setting up the Syndicate Tool is a one-time task per a user and a machine.

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
