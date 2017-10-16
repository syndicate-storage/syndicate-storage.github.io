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

* First download SDM from git.

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
