---
layout: post
title: "Packaging data and metadata using dtool"
comments: true
tags:
  - biology
  - bioinformatics
  - data management
---

## Introduction

In the previous post I described four principles for effective data management.

1. Make it clear who is responsible for what
2. Keep raw data safe and separate from derived data
3. Standardise the location and structure of data
4. Provide metadata

Getting a research group together and discussing these can lead to a more
coherent strategy for managing data. However, the fourth principle presents a
challenge in that there is not a perfect solution for packaging metadata with
data.

*What is metadata anyway?*

Metadata is data about data. Take, for example, an experiment comparing the
expression profiles of different tissues in the plant *Arabidopisis thaliana*.
In this example the species, *A. thaliana*, is a key piece of metadata that needs
to be recorded and associated with the data.
Another key piece of metadata to make sense of the data in this example is the tissue.
In other words one would need to record the tissue associated each expression profile.
These types of metadata are called descriptive metadata. Without these
descriptive metadata it would be impossible to draw any conclusions from the
data.

When working with digital files one can also think of sizes and checksums
of the files themselves as metadata. This type of metadata is called structural
metadata. Structural metadata can be useful to ensure that files have not become corrupted.
For example, sequencing companies typically provide MD5 checksums alongside
the raw data files so that one can verify that the downloaded sequence files contain the
expected content.

There is also a third type of metadata called administrative metadata.
Administrative metadata is used to manage data as a resource. For example a
UniProt identifier is a piece of administrative metadata used manage a protein
in the UniProt database.

Although metadata is essential for making sense of data finding solutions for
managing metadata can be difficult. In some cases metadata resides
inside the head of individuals. Have you ever found yourself asking one of your
colleagues a question along the lines of:

*What buffer did you use in this experiment?*

One strategy for associating metadata with files is to include it in directory
structures and file names. This takes the form of file paths along the lines of
``replicate_1/chitin/col0_leaf_1.tif``. Here the file name tells us that the
image is from leaf sample one from the Colombia-0 ecotype of *A. thaliana*.
The directory structure encodes that this is replicate one and that the sample
has been treated with chitin.

Using file names and directory structures to store metadata is better than
keeping it in ones head. However, it is also fragile in that the metadata can
easily be lost if one moves or renames the file.

In this post I will describe our approach to overcoming this problem.


## Treating metadata and data as a unified whole

One way to work around the problem of potentially loosing metadata is to
package the metadata with the data. This can be achieved by using a command
line utility called [dtool](https://dtool.readthedocs.io).  dtool centres
around the concept of packaging data and metadata into a unified whole, from
here on in referred to as a dataset. Datasets can be moved around and organised
without loosing metadata.

Let's see this in practise. First of all one needs to install the dtool
software.  This can be done using the Python package installer
[pip](https://pip.pypa.io/en/stable/installing/).

```
$ pip install dtool
```

Let us use dtool to look at the descriptive metadata of a dataset stored
in the cloud.

```
$ dtool readme show http://bit.ly/Ecoli-ref-genome
description: U00096.3 genome with Bowtie2 indices
organism: Escherichia coli str. K-12 substr. MG1655
accession_id: U00096.3
link: https://www.ebi.ac.uk/ena/data/view/U00096.3
index_builder: bowtie2-build version 2.3.3
index_build_cmd: bowtie2-build U00096.3.fasta reference
```

From this metadata one can discern that this dataset contains an *E. coli*
reference genome with Bowtie2 indices.

Using dtool it is possible to list the files in the dataset.

```
$ dtool ls http://bit.ly/Ecoli-ref-genome
dda8452b346d51b9cf60f0662ef3d6e3b6da2e74  reference.2.bt2
d21454a7338c53eabc8d8ed7c2f9c3ff4585c4cf  reference.3.bt2
41fb9ae5d4f6c37226ff324c701b84bc3110709e  reference.1.bt2
b445ff5a1e468ab48628a00a944cac2e007fb9bc  U00096.3.fasta
37e2d68bb38271036d96b6979d24666e0d4fd814  reference.rev.1.bt2
23ebd7cd21a905d5f255919ca1d0491901cb8718  reference.4.bt2
828ebf503926b7c1b8b07c1995b4ca818814b404  reference.rev.2.bt2
```

The output above lists identifers and the relative paths of all the files in
the dataset. In dtool terminolgy the files in a dataset are referred to as
items.

It is also possible to get administrative and structural metadata from a dataset.
This can be done using the ``dtool summary`` command.

```
$ dtool summary http://bit.ly/Ecoli-ref-genome
name: Escherichia-coli-ref-genome
uuid: 8ecd8e05-558a-48e2-b563-0c9ea273e71e
creator_username: olssont
number_of_items: 7
size: 18.8MiB
frozen_at: 2018-09-26
```

From this one can discern, amongst other things, that the data is 18.8MiB in
size and that it has been given the Universally Unique Identifier (UUID)
``8ecd8e05-558a-48e2-b563-0c9ea273e71e``.

Suppose that you had recieved some *E. coli* RNA sequencing data and you wanted
to align it using Bowtie2. In that case
this dataset would be useful as it contains both the reference genome
and Bowtie2 generated indices. Below we create a directory
for storing datasets, and use dtool to download the dataset.

```
$ mkdir datasets
$ dtool cp http://bit.ly/Ecoli-ref-genome datasets/
Generating manifest  [####################################]  100%  reference.rev.1.bt2
Dataset copied to:
file:///Users/olssont/datasets/Escherichia-coli-ref-genome
```

The command above achieved a lot. It downloaded all the data and metadata from
a dataset stored in the cloud, in an Amazon S3 bucket to be precise, and
reconstructed the dataset on local disk. Note that this involved working with
two different storage technologies, both S3 object storage and file system disk.

All the commands that we have been using on the dataset hosted in the cloud
work the same on the dataset stored on local file system.

```
$ dtool readme show datasets/Escherichia-coli-ref-genome
description: U00096.3 genome with Bowtie2 indices
organism: Escherichia coli str. K-12 substr. MG1655
accession_id: U00096.3
link: https://www.ebi.ac.uk/ena/data/view/U00096.3
index_builder: bowtie2-build version 2.3.3
index_build_cmd: bowtie2-build U00096.3.fasta reference
```

Let's look at the structure of a dataset on the local file system.

```
$ tree datasets/Escherichia-coli-ref-genome
datasets/Escherichia-coli-ref-genome
├── README.yml
└── data
    ├── U00096.3.fasta
    ├── reference.1.bt2
    ├── reference.2.bt2
    ├── reference.3.bt2
    ├── reference.4.bt2
    ├── reference.rev.1.bt2
    └── reference.rev.2.bt2

1 directory, 8 files
```

From the above we can see that the data files are stored in a subdirectory
named ``data``.  The descriptive metadata is stored in the ``README.yml`` file.

```
$ cat datasets/Escherichia-coli-ref-genome/README.yml
description: U00096.3 genome with Bowtie2 indices
organism: Escherichia coli str. K-12 substr. MG1655
accession_id: U00096.3
link: https://www.ebi.ac.uk/ena/data/view/U00096.3
index_builder: bowtie2-build version 2.3.3
index_build_cmd: bowtie2-build U00096.3.fasta reference
```

On file system the data and metadata are stored in files. Furthermore, the
metadata files are plain text and make use of open standards. This makes it
possible to read and understand them without the need for specialised tools.

In summary, dtool treats data and metadata as a unified whole. The dtool
command  line interface (CLI) can be used to inspect a dataset's metadata allowing
one to understand the content of the dataset.


In this section three important features of dtool have been highlighted:

1. The dtool command line interface can be used to inspect a dataset's metadata
   allowing one to understand the content of the dataset.

2. When copying a dataset with dtool both the data and the metadata are copied
   across. This means that it is possible to copy datasets, for example to
   long-term storage systems, without fear of loosing metadata.

3. dtool supports several storage systems including both file system and Amazon
   S3 object storage. This make it possible to copy datasets between different
   storage systems without having to learn the specifics (and quirks) of the
   storage systems.

## Creating a dataset from scratch

SHOW THAT IT IS PAINLESS!

## Validating the integrity of a dataset

BONUS MATERIAL!

## DISCUSSION

LINK TO PAPER AND DOCUMENTATION!
