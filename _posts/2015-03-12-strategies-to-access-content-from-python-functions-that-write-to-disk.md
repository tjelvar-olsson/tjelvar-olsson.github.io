---
layout: post
title: Strategies to access content from Python functions that write to disk
comments: true
tags:
  - python
  - test driven development
---

Have you ever worked with an API that has some sort of "save to file" function
only to find yourself wanting a function that returns the content to a string?
For example the Python image module ``skimage.io`` has a function named
[``imsave``](http://scikit-image.org/docs/dev/api/skimage.io.html#imsave) that
takes ``fname`` and ``arr`` as arguments and writes an image to disk. However,
what I wanted was a function that returned the image as a byte string. In other
words I wanted the behaviour of the Python Image Library's
[``PIL.Image.tobytes``](http://pillow.readthedocs.org/en/latest/reference/Image.html#PIL.Image.Image.tobytes)
function. However, I could not find one in scikit-image.

## Strategy 1: make use of ``StringIO``

In these types of circumstances one can often make use of Python's built-in
[``StringIO``](https://docs.python.org/2/library/stringio.html) module.
Let's illustrate this using ``PIL``.

```python
>>> import numpy as np
>>> from PIL import Image
>>> from StringIO import StringIO
>>> ar = np.zeros((50,50), dtype=np.uint8)  # The array we want to get a png byte string for.
>>> img = Image.fromarray(ar)
>>> img = img.convert('RGB')  # Need to convert to RGB to save as PNG.
>>> output = StringIO()
>>> img.save(output, format="PNG")
>>> contents = output.getvalue()
>>> output.close()
>>> assert(isinstance(contents, bytes))

```

## Strategy 2: write, read, delete

However, one cannot use the approach above with ``skimage.io.imsave`` as it
does not provide a means to specify the format (the format seems to be
"automagically" determined from the file name). So we are forced to save the
image to disk and then read the contents of the file.

```python
>>> import os
>>> from skimage.io import imsave
>>> imsave('tmp.png', ar)
>>> contents = open('tmp.png', 'rb').read()
>>> os.unlink('tmp.png')
>>> assert(isinstance(contents, bytes))

```

## Strategy 3: create a context manager

The code above above is really ugly. What we want is something that can give us
a relatively safe temporary file path and delete it once we are done with it.
This is what Python's context managers are for. Context managers are what lets
you use the ``with`` statement for opening files etc. Jeff Preshing has
written a nice tutorial on context mangers [The Python "with" Statement by
Example](http://preshing.com/20110920/the-python-with-statement-by-example/).

Here I will use a test driven development (TDD) approach to illustrate how we
can implement a context manager to help us work more safely with temporary file
paths. So, before we start working on an implementation let us specify
the desired behaviour as a test. Add the code below to a file named
``tempfilepath.py``.

```python
if __name__ == "__main__":
    import os.path
    fpath = None
    with TemporaryFilePath() as tmp:
        assert(os.path.isfile(tmp.fpath))
        with open(tmp.fpath, 'w') as fh:
            fh.write('Testing opening and writing...')
        fpath = tmp.fpath
    assert(not os.path.isfile(fpath))
```

The code above will raise a ``NameError`` stating that the
``TemporaryFilePath`` is not defined. Great, now we can start adding an
implementation to make the tests pass. I will do this incrementally as it is a
useful illustration of some of the aspects of TDD.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

if __name__ == "__main__":
    import os.path
    fpath = None
    with TemporaryFilePath() as tmp:
        assert(os.path.isfile(tmp.fpath))
        with open(tmp.fpath, 'w') as fh:
            fh.write('Testing opening and writing...')
        fpath = tmp.fpath
    assert(not os.path.isfile(fpath))

```

We now get the error message below.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 7, in <module>
    with TemporaryFilePath() as tmp:
AttributeError: __exit__
```

In true TDD style let us add a minimal implementation to make the test pass.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __exit__(self, type, value, tb):
        pass

if __name__ == "__main__":
    import os.path
    fpath = None
    with TemporaryFilePath() as tmp:
        assert(os.path.isfile(tmp.fpath))
        with open(tmp.fpath, 'w') as fh:
            fh.write('Testing opening and writing...')
        fpath = tmp.fpath
    assert(not os.path.isfile(fpath))

```

The implementation now gives the error below.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 10, in <module>
    with TemporaryFilePath() as tmp:
AttributeError: __enter__
```

Let us add the minimal implementation to fix this.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __enter__(self):
        pass

    def __exit__(self, type, value, tb):
        pass

```

Which reveals the error below.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 14, in <module>
    assert(os.path.isfile(tmp.fpath))
AttributeError: 'NoneType' object has no attribute 'fpath'
```

Can you work out what we need to do to fix this? This is a bit subtle, and it
caught me out. The clue is that the ``tmp`` variable is ``NoneType``, whereas
it should have been ``TemporaryFilePath``. This is due to the ``__enter__``
function not returning anything. Let us fix it.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        pass

```

Now the context manager returns the object type we expect.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 14, in <module>
    assert(os.path.isfile(tmp.fpath))
AttributeError: 'TemporaryFilePath' object has no attribute 'fpath'
```

Time to add the ``fpath`` attribute.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self):
        self.fpath = 'tmp.txt'

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        pass
```

Now we are starting to get to the centre of the desired functionality.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 17, in <module>
    assert(os.path.isfile(tmp.fpath))
AssertionError
```

At this stage we just want to get the tests to pass so we add a "dumb" implementation.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self):
        self.fpath = 'tmp.txt'
        with open(self.fpath, 'w') as fh:
            pass

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        pass

```

Which gets us to the second assertion statement.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 23, in <module>
    assert(not os.path.isfile(fpath))
AssertionError
```

Basically, we need to add some clean up functionality to the ``__exit__`` function.

```python
import os

class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self):
        self.fpath = 'tmp.txt'
        with open(self.fpath, 'w') as fh:
            pass

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        os.unlink(self.fpath)

```

And that makes all the tests pass. However, this code still has the ugly
side-effect of hijacking the ``tmp.txt`` file. It is time to refactor the code
to make it less nasty. Let us make use of the
[``tempfile``](https://docs.python.org/2/library/tempfile.html) module.

```python
import os
import tempfile

class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self):
        with tempfile.NamedTemporaryFile(delete=False) as tmp:
            self.fpath = tmp.name

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        os.unlink(self.fpath)
```

Great, everything is working nicely. The only problem is that in order to be
able to save an image in png file format we need to be able to specify the
suffix of the file name.

At this stage it is very tempting to simply add the desired functionality and
it is where test driven development really requires discipline. Let us be good
practitioners of TDD and add a test specifying the desired behaviour first.

```python
if __name__ == "__main__":
    import os.path
    fpath = None
    with TemporaryFilePath() as tmp:
        assert(os.path.isfile(tmp.fpath))
        with open(tmp.fpath, 'w') as fh:
            fh.write('Testing opening and writing...')
        fpath = tmp.fpath
    assert(not os.path.isfile(fpath))
        
    with TemporaryFilePath(suffix='.png') as tmp:
        assert(tmp.fpath.endswith('.png'))
        
```

Great, we now have a failing test.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 27, in <module>
    with TemporaryFilePath(suffix='.png') as tmp:
TypeError: __init__() got an unexpected keyword argument 'suffix'
```

Let us continue to work incrementally and only fix the error reported.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self, suffix=None):
        with tempfile.NamedTemporaryFile(delete=False) as tmp:
            self.fpath = tmp.name
```

This moves us on to the actual assertion that we wanted to test.

```
$ python tempfilepath.py
Traceback (most recent call last):
  File "tempfilepath.py", line 28, in <module>
    assert(tmp.fpath.endswith('.png'))
AssertionError
```

Let us try to fix it.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self, suffix=None):
        with tempfile.NamedTemporaryFile(suffix=suffix, delete=False) as tmp:
            self.fpath = tmp.name

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        os.unlink(self.fpath)

```

However, this results in a horrible error message.

```
Traceback (most recent call last):
  File "tempfilepath.py", line 20, in <module>
    with TemporaryFilePath() as tmp:
  File "tempfilepath.py", line 8, in __init__
    with tempfile.NamedTemporaryFile(suffix=suffix, delete=False) as tmp:
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/tempfile.py", line 462, in NamedTemporaryFile
    (fd, name) = _mkstemp_inner(dir, prefix, suffix, flags)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/tempfile.py", line 237, in _mkstemp_inner
    file = _os.path.join(dir, pre + name + suf)
TypeError: cannot concatenate 'str' and 'NoneType' objects
```

The error message above is basically trying to tell us that the ``suffix``
argument should not be ``None`` by default. We can verify this by looking at
the
[tempfile.NamedTemporaryFile](https://docs.python.org/2/library/tempfile.html#tempfile.NamedTemporaryFile)
documentation, which states that it should be an empty string. Let us fix our code.

```python
class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self, suffix=''):
        with tempfile.NamedTemporaryFile(suffix=suffix, delete=False) as tmp:
            self.fpath = tmp.name

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        os.unlink(self.fpath)
```

And now all the tests pass. Below is the code in all its glory.

```python
import os
import tempfile

class TemporaryFilePath(object):
    """Context manager for handling temporary file paths."""

    def __init__(self, suffix=''):
        with tempfile.NamedTemporaryFile(suffix=suffix, delete=False) as tmp:
            self.fpath = tmp.name

    def __enter__(self):
        return self

    def __exit__(self, type, value, tb):
        os.unlink(self.fpath)

if __name__ == "__main__":
    import os.path
    fpath = None
    with TemporaryFilePath() as tmp:
        assert(os.path.isfile(tmp.fpath))
        with open(tmp.fpath, 'w') as fh:
            fh.write('Testing opening and writing...')
        fpath = tmp.fpath
    assert(not os.path.isfile(fpath))
        
    with TemporaryFilePath(suffix='.png') as tmp:
        assert(tmp.fpath.endswith('.png'))
```

We can now use this to get the content of our numpy array as an image in byte
string representation.

```python
>>> from tempfilepath import TemporaryFilePath
>>> with TemporaryFilePath(suffix='.png') as tmp:
... 	imsave(tmp.fpath, ar)
...     content = open(tmp.fpath, 'rb').read()
...
>>> assert(isinstance(content, bytes))

```

