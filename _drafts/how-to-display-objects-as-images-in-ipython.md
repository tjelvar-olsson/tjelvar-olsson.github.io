---
layout: post
title: How to display objects as images in IPython
comments: true
tags:
  - python
  - image analysis
---

IPython has some neat functionality for displaying objects in ways that can be
more informative than the standard ``__repr__`` representation. Both the
IPython notebook and qtconsole support the display of png, jpeg and svg images.
Furthermore, the IPython notebook can also display html, javascript, json and
latex.

If you simply want to display an image you can achieve this using the
``IPython.display.Image`` class.

```python
>>> from IPython.display import Image
>>> image = Image('tjelvar.png')
>>> image
```

![Tiny image of tjelvar](/images/tiny_tjelvar.png)

However, suppose that you wanted to create an image representation of your own
class. Let us illustrate this with the hypothetical example of an
``ImageFile`` class that simply stores the location of an image.

```python
class ImageFile(object):
    """Class for storing an image location."""

    def __init__(self, fpath):
        self.fpath = fpath

    def _repr_png_(self):
        return open(self.fpath, 'r').read()
```

The usage of the class above would be along the lines of the below.

```python
>>> im_file = ImageFile('tjelvar.png')
>>> im_file
```

![Thumbnail of tjelvar](/images/tjelvar.png)

The example above would fall over if the file was not in png format. Let us
make the code a little bit more robust by adding a naive check for the file
format.

```python
class ImageFile(object):
    """Class for storing an image location."""

    def __init__(self, fpath):
        self.fpath = fpath
        self.format = fpath.split('.')[-1]

    def _repr_png_(self):
        if self.format == 'png':
            return open(self.fpath, 'r').read()
```

Finally, let us extend the class to be able to deal with jpeg and svg images as
well.

```python
class ImageFile(object):
    """Class for storing an image location."""

    def __init__(self, fpath):
        self.fpath = fpath
        self.format = fpath.split('.')[-1]

    def _repr_png_(self):
        if self.format == 'png':
            return open(self.fpath, 'r').read()

    def _repr_jpeg_(self):
        if self.format == 'jpeg' or self.format == 'jpg':
            return open(self.fpath, 'r').read()

    def _repr_svg_(self):
        if self.format == 'svg':
            return open(self.fpath, 'r').read()
```

You now have a class that stores image file paths and has the capability to
interactively display the images using either IPython qtconsole or IPython
notebook.

## Useful links

- [Integrating your own objects with
  IPython](http://ipython.org/ipython-doc/dev/config/integrating.html)
- [IPython rich display
  system](http://nbviewer.ipython.org/github/ipython/ipython/blob/1.x/examples/notebooks/Part%205%20-%20Rich%20Display%20System.ipynb)
- [Using the IPython display protocol for you own
  objects](http://nbviewer.ipython.org/github/ipython/ipython/blob/3607712653c66d63e0d7f13f073bde8c0f209ba8/docs/examples/notebooks/display_protocol.ipynb)
