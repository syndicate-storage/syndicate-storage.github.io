---
layout: post
title: Public Datasets
---

Syndicate delivers scientific datasets efficiently. These datasets are easily 
accessed in the same way as accessing local files.

## Datasets

Large-scale DNA sequence datasets are publicly accessible.

| **Dataset** | **Syndicate Volume** | **Access** | **Description** |
| -------------| ----------- | ----------- | ----------- |
| `iMicrobe`   | `imicrobe`  | `imicrobe_anonymous` Anonymous User Gateway | Microbial datasets hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://imicrobe.us/](http://imicrobe.us/) |
| `iVirus`     | `ivirus`    | `ivirus_anonymous` Anonymous User Gateway | Viral datasets hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://ivirus.us/](http://ivirus.us/) |

### Accessing a datatset using `syndicatefs`

To mount the `iMicrobe` dataset in a local path `/mnt/imicrobe` using the 
`syndicatefs`,
```
syndicatefs -u ANONYMOUS -v imicrobe -g imicrobe_anonymous /mnt/imicrobe
```

### Accessing a dataset using a `Docker image`

**[Docker](https://www.docker.com)** allows rapid deployment of software packages.
It also can be used in deployment of datasets.

Our pre-baked `docker image` gives you a **ready-to-go** analysis environment 
where analysis software and a dataset are prepared.

| **Dataset** | **Docker Image**| **Installed Softwares** |
| -------------| ----------- | ----------- |
| `iMicrobe`   | `syndicatestorage/imicrobe-anaconda`  | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2 |
| `iVirus`     | `syndicatestorage/ivirus-anaconda`    | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2 |

To access, 
```
docker run -ti --cap-add SYS_ADMIN --device /dev/fuse --privileged syndicatestorage/imicrobe-anaconda
```
