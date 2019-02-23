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
typically a split across two axis. One axis being project leaders vs post-docs
and PhD students. The former being more concerned with the long term safety and
viability of the data produced by the group and the latter being more concerned
with the limitations of the tools available for them to do their day to day
work. The other axis across which people have different points of view is that
between experimental biologists vs bioinformaticians.  The former having pain
points around managing distributed versions of Word and Excel files and the
latter struggling with having enough storage quota on the high-performance
cluster to analyse the high-throughput sequencing data produced by the group.

Having mediated many such meeting it has become clear that there is not a one
solution that fits all. Each research group and project has its own quirks and
the members need to find a solution that works for them. However, we have also
discovered that there are some general principles that can help guide a group
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

## Principle 2: Keep raw data separate, safe and findable 

## Principle 3: Standardise the location and structure of data

## Principle 4: Provide metadata

## Discussion

The first two of these principles can be implemented relatively
easily.
