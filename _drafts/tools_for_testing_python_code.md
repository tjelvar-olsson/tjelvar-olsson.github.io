---
layout: post
title: "Four tools for testing your Python code"
comments: true
tags:
  - programming
  - software development
  - test driven development 
---

In a
[previous post]({% post_url 2015-05-09-begginers-guide-creating-clean-python-development-environments% })
we discussed how to set up clean Python development environment using
``virtualenv`` and
[cookicutter]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %}).

```
$ cookiecutter gh:tjelvar-olsson/cookiecutter-pypackage
...
repo_name (default is "mypackage")? awesome
...
$ cd awesome
$ virtualenv ~/virtualenvs/awesome
$ source ~/virtualenvs/awesome/bin/activate
(awesome)$ python setup.py develop
```

After having set up the development environment we ran the tests to check that
everything was working as expected.

```
(awesome)$ python tests/tests.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

This post will elaborate on the testing machinery running behind the scenes.


## 1. Unittest - a Python module for creating tests

Python comes with batteries included and built into the standard library is a
module named ``unittest``, which can be used to write tests.

As a side note: tests can be classified into many different types: unit tests,
integration tests, functional tests, acceptance tests. Mark Simpson has written
a nice concise overview of the different types of tests on
[stackoverflow](http://stackoverflow.com/a/4904533). As the post implies the
subject of classifying tests is rather subjective and you get different answers
depending on where you look. Personally, as I do not to work on massive code
bases, I simply use two broad categories: unit tests and functional test, where
the latter incorporates both acceptance and integration tests.

No matter how you classify your tests you can use Python's ``unittest`` module
to write them.

Below is a bare bones skeleton for writing a test using the ``unittest`` module.
To write a test we create a subclass of the ``unittest.TestCase`` base class.
Now any functions in our test class that start with ``test_`` will be tested
when we call the ``unittests.main()`` function. Copy and past the code below
into a file named ``basic_unittest.py``.

```python
import unittest

class MyTest(unittest.TestCase):
    def test_something(self):
        pass

if __name__ == "__main__":
    unittest.main()
```

Let us see what happens when run this code.

```
$ python basic_unittest.py
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

Okay, now let us have a look at the ``tests/tests.py`` file generated earlier
on by our cookiecutter template.

```python
import unittest
import os
import os.path
import shutil

HERE = os.path.dirname(__file__)
DATA_DIR = os.path.join(HERE, 'data')
TMP_DIR = os.path.join(HERE, 'tmp')


class UnitTests(unittest.TestCase):

    def test_can_import_package(self):
        # Raises import error if the package cannot be imported.
        import awsome

    def test_package_has_version_string(self):
        import awsome
        self.assertTrue(isinstance(awsome.__version__, str))

class FunctionalTests(unittest.TestCase):

    def setUp(self):
        if not os.path.isdir(TMP_DIR):
            os.mkdir(TMP_DIR)

    def tearDown(self):
        shutil.rmtree(TMP_DIR)


if __name__ == "__main__":
    unittest.main()
```

There are several things to note here.

Let us start by looking at the
``test_package_has_version_string`` function. It makes use of
``unittest.TestCase.assertTrue`` to check that the version number of
the ``awesome`` package we are developing is a string (using Python's
built in [isinstance](https://docs.python.org/2/library/functions.html#isinstance)
function). There are many other useful "assert" functions built into
the ``unittest.TestCase`` base class, note in particular
[unittest.TestCase.assertEqual](https://docs.python.org/2/library/unittest.html#unittest.TestCase.assertEqual).

At the top of the file we import several additional modules: ``os``,
``os.path``, ``shutil``. The ``os.path`` module is used to define some global
variables defining where we can expect to find input files for our functional
tests (``tests/data``) and where to write transient output from our functional
tests (``tests/tmp``).

The ``unittest.TestCase.setUp`` and ``unittest.TestCase.tearDown`` functions
provide a way to ensure test isolation. They are run before and after each
individual test in a test class.  The ``os`` module is used to create the
``tests/tmp`` directory if it does not already exist during the ``setUp`` of
the functional test and similarly the ``shutil`` module is used to remove the
``tests/tmp`` directory when a test is finished.

Hopefully this quick overview has provided a enough detail for you to get
started with writing your own tests. For more information have a look at the
[unittest documentation](https://docs.python.org/2/library/unittest.html).


## 2. Nose - a test runner for finding tests

As you build up more and more tests you want to have a way of running them all
automatically. One way to do this is to use
[nose](https://nose.readthedocs.org/en/latest/).

Let us install it using ``pip``.

```
(awesome)$ pip install nose
```

Now we can run the test suite using the ``nosetest`` command.

```
(awesome)$ nosetests
nose.plugins.cover: ERROR: Coverage not available: unable to import coverage module
..
----------------------------------------------------------------------
Ran 2 tests in 0.004s

OK

```

There are two things to note above. First of all, the ``nosetest`` command
automatically found and ran our tests in the ``tests`` directory. Yay!

Secondly, it complained about not being able to import the ``coverage`` module.
There are two reasons for this:

1. We have not installed the ``coverage`` module yet
2. The setup.cnf file specifies that it should be used

```conf
[nosetests]
detailed-errors=1
with-coverage=1
cover-package=awesome
cover-erase=1
verbosity=1
```

*What is the coverage all about anyway?*


## 3. Coverage - measuring your code coverage

The ``coverge`` module measures code coverage. Code coverage is a measure of
how many lines of ones code are being exercised by your tests. It is
particularly useful for identifying areas of the code-base that need more
tests.

Let us install it.

```
(awesome)$ pip install coverage
```

Now let us run the tests again.

```
(awesome)$ nosetests
..
Name     Stmts   Miss  Cover   Missing
--------------------------------------
awsome       1      0   100%   
----------------------------------------------------------------------
Ran 2 tests in 0.009s

OK
```

Great we have 100% test coverage! Let us add some more functionality to see what
happens when we have code that is not tested. Add the ``fpaths_in_dir()`` function
to the ``awesome/__init__.py`` file.

```
"""awesome package."""
import os

__version__ = "0.0.1"

def fpaths_in_dir(directory):
    """Return the paths to the files in the directory."""
    fpaths = []
    for fname in os.listdir(directory):
        fpaths.append(os.path.join(directory, fname))
    return fpaths
```

If we run the tests again we find out that lines 8-11 have not been convered
by the tests.

```
(awseome)$ nosetests
..
Name      Stmts   Miss  Cover   Missing
---------------------------------------
awesome       7      4    43%   8-11
----------------------------------------------------------------------
Ran 2 tests in 0.010s

OK
```

Let's add a test for them! But wait... Errh...

*How do we add a reliable test for something that wants to read information
from the file system?*


## 4. Mock - faking objects for unit tests

We can make use of mock objects to solve these types of problems. Mock objects
are mimic the behaviour of real objects in controllable ways. For more background
have a look at the
[Mock object wikipedia page](http://en.wikipedia.org/wiki/Mock_object).

As of Python 3.3 ``mock`` is part of the standard library. However, users of older
versions of Python can install it using ``pip``.

```
(awseome)$ pip install mock
```

Now we can write a test for our function. Add the function below to the
``UnitTests`` class in the ``awesome/tests/tests.py`` file.

```
    def test_fpaths_in_dir(self):
        from mock import MagicMock
        from awesome import fpaths_in_dir
        os.listdir = MagicMock(return_value=['test1.txt', 'test2.txt'])
        fpaths = fpaths_in_dir('some/dir')
        expected = ['some/dir/test1.txt', 'some/dir/test2.txt']
        self.assertEqual(fpaths, expected)
```

Let us run the tests again.

```
(awseome)$ nosetests
...
Name      Stmts   Miss  Cover   Missing
---------------------------------------
awesome       7      0   100%
----------------------------------------------------------------------
Ran 3 tests in 0.043s

OK
```

Great all the tests are passing! We can now relax again.

The ``mock`` module can do much more than what I have shown above. Have a look
at the [mock documentation](https://pypi.python.org/pypi/mock) for some more
inspiration.


## Conclusion

Python comes with lots of useful tools for helping you test your code base. In
this post I have described some of the most established ones. However there are
others around. Experiment and find out what works for you.

In the next post I will continue in on the theme of testing and try to illustrate
some aspects of test-driven development.
