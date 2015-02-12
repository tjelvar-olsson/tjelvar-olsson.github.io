---
layout: post
title: Saving 16-bit tiff files using Python
tags:
  - python
  - image analysis
---

When dealing with microscopy data it is not uncommon to be dealing with image
files that have 16-bit channels. This presents a difficulty when
working with Python as many imaging libraries struggle to save ``numpy.uint16``
arrays.

To illustrate the problem let us create a white 50x50 pixel 16-bit image using
``numpy``.

```python
>>> import numpy as np
>>> ar = np.ones((50,50), dtype=np.uint16)
>>> ar = ar * np.iinfo(np.uint16).max

```

[PIL](http://www.pythonware.com/products/pil/)/[Pillow](https://pillow.readthedocs.org/)
simply, and helpfully, raises a ``TypeError``.

```python
>>> from PIL import Image
>>> img = Image.fromarray(ar)
Traceback (most recent call last):
...
TypeError: Cannot handle this data type

```

[SciPy](http://www.scipy.org/) does save the file, but it converts it to 8-bit.
Personally I do not like this behaviour as it has caused me confusion on
several occasions as subsequent steps of the analysis has read the file and
tried to extract meaningful information from it.

```python
>>> import scipy.misc
>>> scipy.misc.imsave('scipy.tiff', ar)
>>> ar2 = scipy.misc.imread('scipy.tiff')
>>> ar2.dtype
dtype('uint8')
>>> np.max(ar2)
0

```

### PyLibTiff to the rescue

[PyLibTiff](https://code.google.com/p/pylibtiff/) is a package that provides a
wrapper to the [libtiff](http://www.remotesensing.org/libtiff/) library. To use
it simply make sure that you have the libtiff library installed on your system
and then you can use ``pip`` to install PyLibTiff. On a Debian based system.

```bash
sudo apt-get install libtiff-dev
sudo pip install libtiff
```

Now let us look at how to save a file using PyLibTiff.

```python
>>> from libtiff import TIFF
>>> tiff = TIFF.open('libtiff.tiff', mode='w')
>>> tiff.write_image(ar)
>>> tiff.close()

```

To show that everything is working as expected let us open the tiff file and
read in the image from it.

```python
>>> tiff = TIFF.open('libtiff.tiff', mode='r')
>>> ar = tiff.read_image()
>>> tiff.close()
>>> ar.dtype
dtype('uint16')
>>> np.max(ar)
65535

```


### Other options

Another options for working with 16-bit tiff files is
[OpenCV-Python](http://docs.opencv.org/trunk/doc/py_tutorials/py_tutorials.html).
I also believe that
[tiffile.py](http://www.lfd.uci.edu/~gohlke/code/tifffile.py.html) can handle
them, although I have not tested this myself. The reason I prefer PyLibTiff
over these is that it can be installed into a virtual environment using pip. 
