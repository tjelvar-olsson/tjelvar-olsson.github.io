---
layout: post
title: Saving RGB tiff files using PyLibTiff
tags:
  - python
  - image analysis
---

In a [previous
post]({% post_url saving-16bit-tiff-files-using-python %}) I showed
how to read and write tiff files in Python using
[PyLibTiff](https://code.google.com/p/pylibtiff/). Here I will illustrate how
to use PyLibTiff to create an RGB tiff file.

The PyLibTiff on-line documentation is kind of minimal, so I started off by
simply trying to save a list containing three ``numpy.arrays``. This was simply
a guess based upon how I would have liked the package to work.

```python
>>> import numpy as np
>>> from libtiff import TIFF
>>> r = np.ones((50,50), dtype=np.uint8) * 30
>>> g = np.ones((50,50), dtype=np.uint8) * 90
>>> b = np.ones((50,50), dtype=np.uint8) * 120
>>> tiff = TIFF.open('initial-test.tiff', 'w')
>>> tiff.write_image([r, g, b])
>>> tiff.close()
```

To my surprise this created a tiff file without complaining. However,
inspecting the tiff file using
[exiftool](http://www.sno.phy.queensu.ca/~phil/exiftool/) revealed that the
file only had one channel per sample. I had in fact produced a multi-page tiff
file.

```bash
$ exiftool initial-test.tiff 
...
Bits Per Sample                 : 8
Compression                     : Uncompressed
Photometric Interpretation      : BlackIsZero
...
```

After some head scratching I started digging around in PyLibTiff's built in
documentation using ``pydoc``. This documentation was very informative. It
revealed that the ``write_image()`` function has an argument named
``write_rgb``, which by default is set to ``None``.

```python
>>> tiff = TIFF.open('rgb-test.tiff', 'w')
>>> tiff.write_image([r, g, b], write_rgb=True)
>>> tiff.close()
```

Inspecting the new file revealed that it was indeed an RGB tiff file!

```bash
$ exiftool initial-test.tiff 
...
Bits Per Sample                 : 8 8 8
Compression                     : Uncompressed
Photometric Interpretation      : RGB
...
```

