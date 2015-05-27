---
layout: post
title: "Test-driven development part II: tools for testing Python code"
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


## Unittest: a Python module for creating tests

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


## Nose: a test runner for finding tests


## Coverage: measuring your code coverage


## Mock: faking data for unit tests
