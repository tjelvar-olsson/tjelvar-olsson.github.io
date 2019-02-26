---
layout: post
title: "Data management for biologists"
comments: true
tags:
  - biology
  - bioinformatics
  - data management
---

## Introduction

Data management is a great challenge in the biological sciences and discussing
it is often difficult because it is a multi-faceted problem and the term "data
management" often means different things to different people.

Over the past couple of years I have become more and more involved in helping
biological research groups manage their data. The first step in this process is
typically a meeting that gathers all the members of the research group or project
with the aim of getting people onto the same page.

During these sessions the participants are often surprised to find out how
different their point of view are to other people in the group. There is
typically a split across two axis. One axis being project leaders vs group members.
The former being more concerned with the long term safety and
viability of the data produced by the group and the latter being more concerned
with the limitations of the tools available for them to do their day to day
work. The other axis across which people have different points of view is that
between experimental biologists vs bioinformaticians.  The former having pain
points around managing distributed versions of Word and Excel files and the
latter struggling with having enough storage quota on the computer
cluster to analyse the high-throughput sequencing data produced by the group.

Having mediated many such meeting it has become clear that there is not one
solution that fits all. Each research group and project has its own quirks and
the members need to find a solution that works for them. However, 
there are some general principles that can help guide a group
towards a more consistent and coherent way of managing their data.

In this post I'd like to share these guiding principles that I use to mediate
these types of group data management sessions.

## Principle 1: Make it clear who is responsible for what

In terms of data management responsibilities are often implicitly assumed to be
with someone else. Let's illustrate this with an example.

Ambitious Anna, is an established group leader who has started making more and
more use of next generation sequencing. Two of the people in Ambitious Anna's
group are Fastidious Fatima and Binary Beatrice. Fastidious Fatima, an
experimental biology post doc, prepares a large batch of samples and sends it
off for sequencing with Nebulous New Sequencing Ltd. After a month of waiting
Nebulous New Sequencing sends Fastidious Fatima an email with instructions for
how to download her 100GB of sequencing data. Fastidious Fatima is busy
preparing more samples and she asks Binary Beatrice, the group
bioinformatician, to download the data. Binary Beatrice is happy to help,
particularly as she needs to process the data anyway.

Ambitious Anna, the group leader, has neither touched the experimental sample
nor the raw data produced by Nebulous New Sequencing
Ltd.  So Ambitious Anna implicitly assumes that the people in her group are
managing the data.

Fastidious Fatima, the experimental biologist, has a record of her experimental
work and samples in her lab notebook. However, since she did not download or
process the sequencing data produced by Nebulous New Sequencing Ltd she assumes
that Binary Beatrice and Ambitious Anna are managing that data.

Binary Beatrice, the bioinformatician, is overworked. As well as analysing data
produced by Fastidious Fatima she also has another six experimental biologists
to support. On top of this she needs to find another post-doc as her contract
runs out in three months time.  Binary Beatrice therefore thinks that it is
Ambitious Anna's job, as the group leader, to ensure that the data is managed
properly.

In this contrived example all the actors implicitly assume that the
management of data is somebody else's responsibility.

Getting everyone into a room to discuss data management can help improve this
situation. By explicitly stating who is responsible for what data are less likely
to fall between the cracks.

One may consider using the template below for assigning responsibilities.

*Ultimately data management is the responsibility of the group leader. However,
in practise the group leader is unlikely to be working with data on a day to
day basis so he or she needs to delegate this responsibility to a data
champion. The data champion then becomes responsible for ensuring that the
existing and new members of the group are aware of the group data management
processes.*

## Principle 2: Keep raw data safe and separate from derived data

Most researchers are aware that they should keep their data safe by backing it
up. If possible it is also worth protecting raw data by making it read
only. This means that you cannot accidentally delete or modify it. More good
suggestion on this topic can be found in [Ten Simple Rules for Digital Data
Storage](https://doi.org/10.1371/journal.pcbi.1005097).

However, here I would like to emphasize another point, *the importance of
keeping **raw** data separate from **derived** data*.

Let's illustrate this with another story. Once upon a time Binary Beatrice was
making the transition from experimental biology to bioinformatics. She had got
her first sequencing data and was eager to analyse it.

Binary Beatrice wanted to run a tool called The Latest & Greatest Aligner,
which after she had spent three weeks installing it, was ready for her to use.
Half a year earlier, as preparation, Binary Beatrice had attended the
institute's cluster computing course and she had learnt how to write a
batch submission script to submit jobs to the cluster. She therefore wrote such
a batch submission script to run her Latest & Greatest Aligner. The Latest &
Greatest Aligner needed to know where the data was so she put the batch
submission script next to the raw data. That way Binary Beatrice did not have
to worry about file paths (the bane of scientific computing).

To Binary Beatrice's surprise The Latest & Greatest Aligner worked out of the
box and produced great results. It also produced lots and lots of files.
However, her analysis did not end there she also had to run The Latest &
Greatest Normaliser and The Greatest and Latest Plotter. These tools produced
even more files.

Then something terrible happened. Binary Beatrice hit her storage quota and
could not write any more files. At this point she had a directory filled with
millions of files. Some of them were raw data, some of them were batch
submission scripts, some of them were intermediate files and some of them were
figures that she wanted to use in her paper.

Because all the derived file names were based on the names of the raw data
files Binary Beatrice did not dare create an expression for deleting files in
bulk. She therefore spent two weeks cleaning up her data.

At this point Binary Beatrice made a promise to herself to always keep raw data
separate from derived data. In fact all her new projects have a structure with
four directories: ``raw_data``, ``scripts``, ``intermediate_data``, and
``final_data``. When she hits her quota it is now easy for her to remove the
files in the ``intermediate_data`` directory.

In the fictional example above Binary Beatrice learnt from her mistake
immediately. This is not always the case. In real life many people ask to get
their storage quota increased and don't learn the lesson of separating raw data
from derived data.  Eventually, when these people leave the group, no one can
work out what their raw/derived data is.


## Principle 3: Standardise the location and structure of data

It is natural, and common, for PhD students and post docs to think of the data
that they generate as their own. This tends to lead to a situation where the
data is organised per research group member. For example, Ambitious Anna might
have a shared folder for her group and at the top level are the folders with
names of the group members ``Fastidious-Fatima``, ``Binary-Beatrice``, etc.
Fastidious Fatima then organises her work and her data in the
``Fastidious-Fatima`` folder and Binary Beatrice organises her work and her
data in the ``Binary-Beatrice`` folder.

This is not necessarily a bad way to organise the group's data.
Group leaders sometimes find it easier to remember data based on who generated it.
However, it is
important to realise that (unless otherwise stated) the data generated when
working in a research group does not belong to the individual generating it.
The data belongs to the group leader. If this is not stated explicitly, and
made clear within the group, it is easy for each member of the group to invent
their own way of structuring the data within their own folder. When this
happens files and data often become incomprehensible once the person who
organised them leaves the group.

It is therefore highly recommended that the location and structure of data is
standardised, and ideally that this standard is recorded in a document that can
be read by everyone at the top level of the shared folder. If a data champion
has been nominated it is his/her responsibility to ensure that this document is
kept up to date and that the other members of the group know that they need to
follow the standards for organising data outlined in this document.

I also highly recommend having a separate folder at the top level of the shared
folder dedicated to storing raw data, see Principle 2 above. Below is an example
that still gives individuals their own working space.

```
Ambitious-Anna
├── GROUP-MEMBERS
│   ├── Binary-Beatrice
│   └── Fastidious-Fatima
├── RAW-DATA
└── README.txt
```

Below is an example that structures work based on projects rather than individuals.
This can be useful if more than one person is working on a project.

```
Ambitious-Anna
├── PROJECTS
│   ├── Cure-Cancer
│   └── Feed-The-World
├── RAW-DATA
└── README.txt
```

Obviously it is possible to mix and match according to need. However, it is
useful to document the rational of the structure and how it is intended to be
used. In the examples above this information is recorded in the ``README.txt`` file.


## Principle 4: Provide metadata

Metadata is a fancy term for information that put data into context. For
example, in a microscopy experiment the pixels captured are data and
information about the experiment such as the magnification and the X/Y scales
are metadata.  More formally, metadata is data about data. Without metadata
raw data is meaningless as it cannot be understood.

It is important to think about what metadata one needs to capture. Often this
is closely linked to the design of the experiment. For example, if one is
performing a time series study, it is important to associate the date/time with
each data point.

This type of metadata is called descriptive metadata. Descriptive metadata is
important as it allows data to be put into the context of a scientific
question. For example, if one performs a RNA sequencing experiment to compare
expression profiles in different tissues it is important to record which data
are associated with which tissues.

When thinking about how to organise data it is worth thinking about how
descriptive metadata should be recorded and associated with the data.
This is a non-trivial problem. It is not uncommon for metadata to be stored
in an individual's memory. This is not a safe strategy! Another common approach
is to store descriptive metadata in file names and directory structures. This is
better, but is also fragile as it is easy to loose metadata when moving and/or
renaming files.

Another type of metadata is structural metadata and includes things such as
sizes and checksums of files.  Structural metadata can be used to
verify that the raw data files have not become corrupted.  For example,
sequencing companies typically provide MD5 checksums along side the raw data files
so that one can verify that the downloaded files contain the expected
content.

This fourth principle, is more complicated than the previous ones. Although, it
is easy to understand that metadata is important there is currently not an easy
way to bundle arbitrary metadata with files on disk. The poor mans solution is
to capture this metadata using some sort of directory structure. However, this
is fragile and makes it difficult to add more meta data on an *ad-hoc* basis.

Furthermore, there is not really a neat solution for capturing structural
metadata such as sizes and checksums of files. It is therefore rarely done
within research groups. Ideally this is something that should be automated
as it is not a productive use of researchers' time to calculate and record
these types of file properties.


## Discussion

Using these principles to mediate discussions about working practises can result in
a much more coherent strategy to managing data.

The first three principles are relatively easy for a research group to get to grips with.
They can be implemented by discussing how the group think things should be done
and by coming to a mutual understanding and agreement on how data should be
structured and organised.

The fourth principle highlights the importance of recoding metadata. However,
having metadata separate from data, for example in directory structures and
file names, is fragile. The metadata can easily be lost when moving and
renaming files. In the next post I will describe our solution to this problem.
