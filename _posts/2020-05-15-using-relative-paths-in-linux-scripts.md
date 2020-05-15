---
layout: post
title: "Using relative paths in Linux scripts"
comments: true
tags:
  - linux
  - data management
---

In the [preivous post]({% post_url 2020-05-05-relative-and-absolute-paths-in-linux %})
I discussed the difference between absolute and relative paths.

*So what is better absolute or relative paths? Which one should be used when
one needs to refer to a file in a script?*

Let me prefix my answer with the caveat that all paths are a pain. However,
absolute paths are more of a pain than relative paths. This is because absolute
paths make it difficult to restructure the way that directories are organised
on your computer. They also make it difficult for you to share your scripts
with collaborators because they would need their computer to be structured in
exactly the same way as yours. It is possible to get around some of these
issues by using relative paths.

This post will show you how create scripts that have a clear separation between
raw and derived data using relative paths. As a bonus the scripts will also be
more portable and less fragile with respect to reorganisations of your
directory structure.

This post makes use of some more advanced Linux skills including the use of
environment variables, the creation of a Bash script and adding execute
permissions to the script. You don't need too worry too much about these details
if they are new to you. An environment variable is a means to store a piece of
information for use later, a Bash script is a text file with commands to run,
and execute permissions allows the script to be run by referencing its path. If
you would like to learn more about these topics please let me know.

<iframe width="560" height="315" src="https://www.youtube.com/embed/YsyPTvIq57s" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

First of all let us recap to get setup to the where the previous post ended.
We need two subdirectories ``raw_data`` and ``scripts``. The ``mkdir`` command
below will create these if they do not already exist (the ``-p`` flag means
that no errors are generated if the directories already exist).

```
$ mkdir -p raw_data scripts
```


We also need a file with raw data. The command below creates this file if
it does not exist and **overwrites** it if it already exists.

```
$ echo "Raw data isn't baked data" > raw_data/raw_data.txt
```

To illustrate the use of relative paths in scripts create a file named
``analysis.sh`` in your ``scripts`` directory, i.e. with the relative path
``./scripts/analysis.sh``, and copy and paste the code below into it.

```bash
#!/bin/bash

# Save the current working directory in an environment variable.
INITIAL_WORKING_DIRECTORY=$(pwd)

# This line changes to current working directory to where
# the analysis.sh file is.
cd "$(dirname "$0")"

# Create an environment variable with the relative path to the
# derived data directory.
DERIVED_DATA_DIRECTORY=../derived_data

# Create the derived data  directory if it does not already exist.
mkdir -p $DERIVED_DATA_DIRECTORY

# This code streams the content of the raw data file into the sed
# stream editor. The sed stream editor is used to edit the content
# of the stream. Finally, the output of sed is redirected to a
# derived_data.txt file in the derived data directory.
cat ../raw_data/raw_data.txt  \
        | sed -e "s/Raw/Fudged/"  \
        | sed -e "s/isn't/is/"  \
        > $DERIVED_DATA_DIRECTORY/derived_data.txt

# Go back to where we were before changing into the
# scripts directory.
cd $INITIAL_WORKING_DIRECTORY
```

The code above works with relative paths. The paths are relative to the
``scripts`` directory. That means that the outcome of the script will be
independent of the directory one is in when running the script, i.e.  no nasty
side effects of input files not being found or output files being written to
the wrong directory.

To achieve this the script first makes a note of the directory you are
currently in and stores it in the ``INITIAL_WORKING_DIRECTORY`` environment
variable. The script then changes the working directory to be that of the
``analysis.sh`` script. At this point the script can start working with paths
relative to the ``scripts`` directory.

The details of the analysis in this script do not really matter. It creates a
directory for derived data (``../derived_data``) if it does not already exist.
It then takes as input the raw data, transforms it before writing it to a file
in the derived data directory.

Finally, and importantly, the script changes the working directory back to
whatever it was before the script was invoked.

To test the script script ensure that you are not in the ``scripts`` directory.
In the command below I change into my home directory.

```
cd /home/olssont
```

In the above I'm using an absolute path to make it clear which directory I am
referring to. Depending on your setup this path may be different. For clarity,
I am referring to the directory in which you created the ``raw_data`` and
``scripts`` directories.

Before we run the script we need to use the ``chmod`` command to make it
executable.

```
chmod +x ./scripts/analysis.sh
```

We can then run the script by calling its path.

```
./scripts/analysis.sh
```

This will have created a directory called ``dervied_data`` at the same level
as the ``scripts`` directory.

```
$ ls
derived_data  raw_data  scripts
```

Let us also use the ``cat`` command to look at the content of the
``./derived_data/derived_data.txt`` file.


```
$ cat ./derived_data/derived_data.txt
Fudged data is baked data
```


In a [previous post]({% post_url 2019-02-26-data-management-for-biologists %}])
about data management I talked about the need to keep raw data separate from
derived data. In this post I have given you some tips on how you can accomplish
this. Setting up scripts in the fashion outlined above also has the benefit
that it is easier to rename and reorganise directories without your scripts
breaking. Furthermore, it will make it easier for you to share your scripts
with collaborators.

