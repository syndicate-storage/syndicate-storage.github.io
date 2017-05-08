---
layout: post
title: Setting up Syndicate PPA
---

Setting up Syndicate PPA
========================

`Syndicate` packages are offered via PPA. This guide shows you how to add 
Syndicate PPA to your system for further Syndicate installations.


Install some utilities.
```
apt-get install apt-transport-https ca-certificates
```

Set up a HTTPS certificate.
```
wget -O /usr/local/share/ca-certificates/butler.crt https://syndicate-storage.github.io/data/butler.crt
update-ca-certificates
```

Register a Syndicate PPA.
```
wget -qO - https://syndicate-storage.github.io/data/butler_opencloud_cs_arizona_edu_pub.gpg | apt-key add -
echo "deb https://butler.opencloud.cs.arizona.edu/repos/release/syndicate syndicate main" > /etc/apt/sources.list.d/butler.list
```

Update package caches.
```
apt-get update
```
