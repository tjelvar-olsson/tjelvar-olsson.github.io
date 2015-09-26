---
layout: post
title: How to build a basic image viewer using FreeImage and SDL2
comments: true
tags:
  - C
  - image analysis
---

In this blog post we will use FreeImage and SDL2 to create a basic image viewer
in C. [FreeImage](http://freeimage.sourceforge.net/) is an open source library
project with support for working with image files. It supports over 30 file
formats, gives access to meta-data and provides basic image manipulation
routines.  [SDL](https://www.libsdl.org/) (Simple DirectMedia Layer) is a
cross-platform library which provides low level access to things like the
keyboard and mouse as well as graphics hardware using OpenGL and Direct3D. SDL
provides official supports for Windows, Mac, Linux, iOS and Android.

The post also serves as an introduction to working with C.

By the end of this post we will have created a C program named ``see`` that can
be used to view RGB and grayscale image from the command line.

```
$ ./see image.png
``` 


## Argument parsing

Let us start by adding some basic argument parsing. Add the code below to a
file named ``see.c``.

```c
#include <stdlib.h>
#include <stdio.h>

char *parse_args_get_filename(int argc, char *argv[])
{
    if (argc != 2) {
        fprintf(stderr, "Usage: %s FILENAME\n", argv[0]);
        exit(2);
    }
    char *filename = argv[1];
    printf("Input file: %s\n", filename);

    return filename;
}

int main(int argc, char *argv[]) {

  char *filename = parse_args_get_filename(argc, argv);

  return 0;
}
```

There are a few things going on about. We include the ``<stdlib.h>`` and
``<stdio.h>`` header files. The former provides the ``exit()`` function and the
latter the ``fprintf()`` and ``printf()`` functions. Note that ``printf(...)``
is a shorthand for ``fprintf(stdout, ...)``. We also define the function
``parse_args_get_filename()`` that returns a pointer to a ``char`` array, the
``char`` array that will hold our input file name.


## Compiling and linking

Let us compile the code.

```
$ gcc -c see.c
```

This creates the object file ``see.o`` which contains machine code as well as
information that allows a linker to find out which symbols (global objects,
functions, etc) it requires in order to work.

Let us link our object file.

```
$ gcc -o see see.o
```

This produces the executable file ``see``, which we can test using the command
below.

```
$  ./see image.png
Input file: image.png
```
