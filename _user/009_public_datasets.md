---
layout: post
title: Mounting Public Datasets
---

## Using the Syndicate Dataset Manager (SDM) Tool


The SDM command-line tool allows users to view and mount from a list of available public datasets.  The list of datasets can be modified seperately from the default list provided.  Currently the following public datasets are available.

| Syndicate Volume | Dataset | Description                               |
| ---------------- | ------- | ----------------------------------------- |
| ivirus           | iVirus  | Metagenomic samples for microbial ecology hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://imicrobe.us/](http://imicrobe.us/) |
| eht2013          | Event Horizon Telescope | Telescopic data from 2013                 |
| uhslc            | University of Hawaii Sea Level Center | Ocean Tide Dataset |
| nanograv9y       | NANOGrav | Gravitational waves data |
| refseq           | NCBI-REFSEQ | NCBI Reference Sequence Database |
| imicrobe         | iMicrobe | Metagenomic samples for viral ecology hosted by **[Cyverse](http://www.cyverse.org)**. For more details, access [http://ivirus.us/](http://ivirus.us/) |

### How to install SDM

SDM requires that Syndicate command-line tools (i.e. syndicate-ug-tools and dependencies) are installed.  See the [installation guide](https://syndicate-storage.github.io/install/) for more information on how to install  Syndicate.

* First download SDM from github.

	`git clone https://github.com/syndicate-storage/syndicate-dataset-manager.git`

* As root, execute the following.

	`cd syndicate-dataset-manager`
	
	`./setup.py build`

	`./setup.py install`

### SDM Help Page

Typing `sdm help` in the terminal will print the following sdm help page.

```
command : sdm <COMMAND> [<COMMAND_SPECIFIC_ARGS> ...]

Available Commands
+----------------------------+-------------------------+
|          COMMAND           |       DESCRIPTION       |
+----------------------------+-------------------------+
| list_datasets | ls | list  | list available datasets |
| show_mounts | ps | status  |    show mount status    |
|        mount | mnt         |     mount a dataset     |
|       mmount | mmnt        |   mount multi-datasets  |
|  unmount | umount | umnt   |    unmount a dataset    |
| munmount | mumount | mumnt |  unmount multi-dataset  |
|           clean            |   clear broken mounts   |
|          help | h          |        show help        |
+----------------------------+-------------------------+
```

### How to list available datasets

To see the default list of available datasets, simply type `sdm list_datasets` or `sdm ls` or `sdm list` at the command prompt.

The list of datasets should look similar to the output shown below.

```
+------------+------------------------------------------------------------+
|  DATASET   |                        DESCRIPTION                         |
+------------+------------------------------------------------------------+
|   ivirus   |       iVirus - Metagenomic samples for viral ecology       |
|  eht2013   |     Event Horizon Telescope - telescopic data in 2013      |
|   uhslc    | University of Hawaii Sea Level Center - Ocean Tide Dataset |
| nanograv9y |            NANOGrav - Gravitational waves data             |
|   refseq   |       NCBI-REFSEQ - NCBI Reference Sequence Database       |
|  imicrobe  |    iMicrobe - Metagenomic samples for microbial ecology    |
+------------+------------------------------------------------------------+
```

### How to mount and unmount a dataset

Mounting or unmounting a dataset is similar to using the standard *_mount_* and *_umount_* commands, except in this case *_mount_* and *_umount_* are options to the *_sdm_* command.

###### Mounting a dataset

This example shows how to mount the *_iMicrobe_* dataset to a directory such as *_/tmp/imicrobe_*

```
>mkdir /tmp/imicrobe

>sdm mount imicrobe /tmp/imicrobe
Mounting a dataset imicrobe to /tmp/imicrobe
A dataset imicrobe is mounted to /tmp/imicrobe
```

###### Unmounting a dataset

```
>sdm umount /tmp/imicrobe
Unmounting a dataset imicrobe mounted at /tmp/imicrobe
Successfully unmounted a dataset imicrobe mounted at /tmp/imicrobe
```

<br>

## Alternative methods of accessing a dataset

### Accessing a dataset using `syndicatefs`

For the next two sample commands, be sure to have configured access to the imicrobe gateway as per the instructions in the [syndicatefs user guide](https://syndicate-storage.github.io/user/001_syndicatefs/)

Mount the `iMicrobe` dataset in a local path `/mnt/imicrobe` using
`syndicatefs`.

```
syndicatefs -u ANONYMOUS -v imicrobe -g imicrobe_anonymous /mnt/imicrobe
```

### Accessing a dataset using `Syndicate UG Tools`

List files in the `iMicrobe` dataset

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
