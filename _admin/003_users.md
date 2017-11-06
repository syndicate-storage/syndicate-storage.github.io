---
layout: post
title: Managing Users
---

Syndicate users are managed with the `syndicate` tool.

## Creating users

##### Standard User

`syndicate create_user "$SYNDICATE_USER" auto`

You can also specify other options such as the max volumes or gateways

`syndicate create_user "$SYNDICATE_USER" auto max_volumes=20 max_gateways=21`

##### Admin User

`syndicate create_user "$SYNDICATE_USER" auto max_volumes=20 max_gateways=21 is_admin=True`

##### A user with private key

`syndicate create_user "$SYNDICATE_USER" "$SYNDICATE_USER.au.pkey"`

## Deleting Users

`syndicate delete_user "$SYNDICATE_USER"`

## List Users

`syndicate list_users`
