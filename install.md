---
layout: post
title: Install Syndicate
---

{% include toc.html %}

Precompiled `Syndicate` packages are offered via an APT repository for Ubuntu
14.04, and can be installed using the following steps.

Install prerequisites for APT over HTTPS:
```
sudo apt-get install apt-transport-https ca-certificates
```

Add the syndicate APT key and repo to the system's APT source list:

```
wget -qO - https://butler.opencloud.cs.arizona.edu/butler_opencloud_cs_arizona_edu_pub.gpg | sudo apt-key add -
echo "deb https://butler.opencloud.cs.arizona.edu/repos/release/syndicate syndicate main" | sudo tee /etc/apt/sources.list.d/butler.list
```

Update your APT package lists, and install syndicate packages:

```
sudo apt-get update
sudo apt-get install syndicate-core
```

Packages available:

 - `syndicate-core` - Core syndicate libraries
 - `syndicate-ms` - Metadata Service
 - `syndicate-ag` - Acquisition Gateway
 - `syndicate-rg` - Replica Gateway
 - `syndicate-ug-tools` - User Gateway Tools
 - `syndicate-automount` - Automount (certificate management) Service
 - `syndicatefs` - Syndicate FUSE driver
