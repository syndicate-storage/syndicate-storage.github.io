---
layout: post
title: Data Security
---

Each gateway belongs to one user and one volume, and uses public/private
key pair certificates for authentication.  The volume owner assigns gateways to users and volumes by signing a certificate that lists the gateway's user ID, volume ID, and its capabilities.  The gateway user signs a certificate that pairs it to the volume owner's signature, as well as its host, port, and driver code hash. Each gateway signs the manifests (*i.e. lists of blocks and hashes associated with a file*) and metadata it replicates using its private key.

Syndicate shares the set of public keys and certificates with its gateways, such that all gateways in the same volume have a causally-consistent view of the other gateways.  This way, a reader gateway will be able to verify that the MS-served metadata record and manifest came from another gateway in the same volume and came from the expected user.

Part of each gateway's certificate includes the hash of its driver.  When a
gateway receives its own certificate, it will download its driver, verify its
authenticity, and instantiate one or more driver chassis processes to execute
the driver code.  The driver code itself is stored on the MS by the user.

Syndicate uses certificates to authenticate users, volumes and gateways. At creation of a user, a gateway or a gateway, Syndicate issues a new certificate. Users should keep certificates securely.
Syndicate certificates shall be copied to other machines or other users when the same user on other machines or different users need to access volumes or gateways.

### Creating a User Private Key

Generating a private key for a new user is quite simple.

```
openssl genrsa 4096 > ${USERNAME}.au.pkey
```

The resulting file can be used when creating a new user that has an existing private key.  See [Managing Users](https://syndicate-storage.github.io/admin/003_users/) in the Admin guide section.


### Copying Certificates

Copying certificates is done by "importing" and "exporting" certificates.
##### Importing certificates: Import a certificate file to a machine.

* syndicate import\_user \<user\_cert\_path> force
* syndicate import\_volume \<volume\_cert\_path> force
* syndicate import\_gateway \<gateway\_cert\_path> force

##### Exporting certificates: Export a certificate on a machine as a file.

* syndicate export\_user \<user\_name> \<output\_dir>
* syndicate export\_volume \<volume\_name> \<output\_dir>
* syndicate export\_gateway \<gateway\_name> \<output\_dir>

#### Example

When you want to access a volume "Vol1" via a gateway "Gateway1", do the following at the machine where the gateway and the volume was created:

Create the `~/Vol1` certificate file

```
syndicate export_volume Vol1 ~/
```

Create the "~Gateway1" certificate file

```
syndicate export_gateway Gateway1
```

Now copy the two certificate files to the machine where you want to access the volume, then import the certificates.

```
syndicate import_volume Vol1 force
syndicate import_gateway Gateway1 force
```