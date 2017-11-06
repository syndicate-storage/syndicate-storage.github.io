---
layout: post
title: Managing Gateways
---

Syndicate gateways are also managed with the `syndicate` tool.

## Gateways

As previously stated, Syndicate uses 3 kinds of gateways to get data into and out of volumes:

 - **User Gateways (UGs)** - The most common, which perform read and write operations on behalf of a user.

 - **Replica Gateways (RGs)** - Work with a backend storage system to store data

 - **Aquisition Gateways (AGs)** - Acquires data from another data management system

### Creating a Gateway

Use the `syndicate` tool with the create_gateway option.

syndicate create\_gateway name=$GATEWAY_NAME type=$TYPE volume=$VOLUME_NAME email=$SYNDICATE\_ADMIN\_EMAIL private\_key=auto port=$PORTNUM

##### Options
* name=$GATEWAY_NAME : The name of the gateway
* type=$TYPE : The gateway type; *AG, UG, RG*
* volume=$VOLUME_NAME : Name of the volume
* email=$SYNDICATE\_ADMIN\_EMAIL : Email address
* private\_key=auto : Automatically handle the key
* port=\$PORTNUM : Port the gateway should run on, i.e. 31112 

