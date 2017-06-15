---
layout: post
title: Public Datasets
---

Syndicate delivers scientific datasets efficiently. These datasets are easily 
accessed in the same way as accessing local files.

## Datasets

DNA sequence datasets are provided via Syndicate.

| **Dataset** | **Syndicate Volume** | **Access** | **Description** |
| -------------| ----------- | ----------- | ----------- |
| `iMicrobe`   | `imicrobe`  | `imicrobe_anonymous` Anonymous User Gateway | Metagenomic samples for microbial ecology hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://imicrobe.us/](http://imicrobe.us/) |
| `iVirus`     | `ivirus`    | `ivirus_anonymous` Anonymous User Gateway | Metagenomic samples for viral ecology hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://ivirus.us/](http://ivirus.us/) |

### Accessing a dataset using `syndicatefs`

To mount the `iMicrobe` dataset in a local path `/mnt/imicrobe` usiDng the 
`syndicatefs`,
```
syndicatefs -u ANONYMOUS -v imicrobe -g imicrobe_anonymous /mnt/imicrobe
```

### Accessing a dataset using `Syndicate UG Tools`

To list files in the `iMicrobe` dataset, 
```
syndicate-ls -u ANONYMOUS -v imicrobe -g imicrobe_anonymous /
```

Similarly, you can use `syndicate-cat`, `syndicate-get` and many more commands 
to access content of the dataset. For more information, check [Syndicate UG Tools](/user/002_syndicaet_ug_tools).

### Accessing a dataset using a `Docker image`

**[Docker](https://www.docker.com)** allows rapid deployment of software packages.
It also can be used in deployment of datasets.

Our pre-baked `docker image` gives you a **ready-to-go** research analysis environment 
where analysis software and a dataset are prepared.

| **Dataset** | **Docker Image**| **Installed Softwares** |
| -------------| ----------- | ----------- |
| `iMicrobe`   | `syndicatestorage/imicrobe-base`      | Ubuntu 14.04 |
| `iMicrobe`   | `syndicatestorage/imicrobe-anaconda`  | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2 |
| `iMicrobe`   | `syndicatestorage/imicrobe-jupyter`   | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2, Jupyter |
| `iMicrobe`   | `syndicatestorage/imicrobe-mash`      | Ubuntu 14.04, Mash |
| `iMicrobe`   | `syndicatestorage/imicrobe-anvio`     | Ubuntu 14.04, Anvio |
| `iVirus`     | `syndicatestorage/ivirus-base`        | Ubuntu 14.04 |
| `iVirus`     | `syndicatestorage/ivirus-anaconda`    | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2 |
| `iVirus`     | `syndicatestorage/ivirus-jupyter`     | Ubuntu 14.04, Anaconda 4.3.1, R 3.3.2, Jupyter |
| `iVirus`     | `syndicatestorage/ivirus-mash`        | Ubuntu 14.04, Mash |
| `iVirus`     | `syndicatestorage/ivirus-anvio`       | Ubuntu 14.04, Anvio |

To access, 
```
docker run -ti --privileged syndicatestorage/imicrobe-anaconda
```
