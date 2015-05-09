---
layout: post
title: "Beginner's Guide: creating clean Python development environments"
comments: true
tags:
  - python
  - software development
---

## Introduction

Code interact with its environment.  For example you can only run a Python
script if you have Python installed on the system.  Furthermore, a Python
script will only run without raising ``ImportError`` exceptions if all the
required packages are installed.

It therefore becomes important for you as a developer / computational scientist
to understand and control the environment in which your code operates.

In this post I will illustrate a work flow for creating clean Python
development environments.


## Example: developing a Python package


In the
[previous post]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %})
I illustrated how you could use a static code generator (``cookiecutter``) to
create a basic template to develop a Python package.

Now suppose that we wanted to develop a Python package named "awesome".  Let us
use a GitHub hosted Cookiecutter template to create a basic project layout.

```
$ cookiecutter gh:tjelvar-olsson/cookiecutter-pypackage
Cloning into 'cookiecutter-pypackage'...
remote: Counting objects: 48, done.
remote: Compressing objects: 100% (37/37), done.
remote: Total 48 (delta 13), reused 37 (delta 8), pack-reused 0
Unpacking objects: 100% (48/48), done.
Checking connectivity... done.
repo_name (default is "mypackage")? awesome
version (default is "0.0.1")? 
authors (default is "Tjelvar Olsson")?
```

This creates the directory ``awesome``.

```
$ cd awesome/
```

With a number of files and directories in it.

```
$ tree
.
├── README.rst
├── awesome
│   └── __init__.py
├── docs
│   ├── Makefile
│   ├── make.bat
│   └── source
│       ├── README.rst
│       ├── conf.py
│       └── index.rst
├── setup.cfg
├── setup.py
└── tests
    ├── __init__.py
    └── tests.py

4 directories, 11 files
```

You may notice that there are some tests included by default. Let us try to run
them.

```
$ python tests/tests.py
EE
======================================================================
ERROR: test_can_import_package (__main__.UnitTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests/tests.py", line 15, in test_can_import_package
    import awesome
ImportError: No module named awesome

======================================================================
ERROR: test_package_has_version_string (__main__.UnitTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests/tests.py", line 18, in test_package_has_version_string
    import awesome
ImportError: No module named awesome

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (errors=2)
```

That's not very good! What is going on? It seems that we cannot import the
``awesome`` module.

Depending on your level of familiarity with Python the problem may be obvious
to you. However, when I started out with Python this caused me a lot of
confusion. I clearly could import the ``awesome`` module!

```
$ python -c "import awesome; print(awesome.__version__)"
0.0.1
```

One of the places where Python looks for modules is within the directory of the
calling script, which is why the command above works. However, when we run the
``tests/tests.py`` script there is no ``awesome`` package to be found within
the ``tests`` directory, illustrated below.

```
$ cd tests/
$ python -c "import awesome; print(awesome.__version__)"
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ImportError: No module named awesome
$ cd ../
```

At this point one could start manually configuring the ``PYTHONPATH``
environment variable. However, let us look at a more elegant solution.

## Making use of ``setuptools``

In the 
[previous post]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %})
we started building up a basic ``setup.py`` file, which made use of the
``setuptools`` module.

You are probably already familiar with ``setuptools`` from installing other Python
packages using the command ``python setup.py install``. This installs the
package of interest into your Python distribution's ``site-packages``
directory.

However, this is not what we want to do because the package would be copied
there and any changes that we made to our local development files would not take
effect until we reinstalled the package. We want to be able to edit our local
development files and see the effects take place immediately.

The solution to this problem is to use ``python setup.py develop`` which
creates an ``.egg-link`` to our local development directory in the
``site-packages`` directory.

```
$ sudo python setup.py develop
Password:
running develop
...
Processing dependencies for awesome==0.0.1
Finished processing dependencies for awesome==0.0.1
```

Let us re-run the tests now that ``site-packages`` contains an ``awesome.egg-link``.

```
$ python tests/tests.py 
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

Great, we have a working development environment!

Before continuing let us square the circle by removing the development package
we just installed.

```
$ sudo python setup.py develop --uninstall
Password:
running develop
...
Removing awesome 0.0.1 from easy-install.pth file
```

So far so good, but there are two issues with what we are currently doing.
First of all we need to have root permissions to run ``python setup.py
develop`` and ``python setup.py develop --uninstall`` when using the system's
Python. Secondly, when using the system's Python are using a (potentially)
polluted environment.

Let me expand on the second issue. Suppose that you had the ``PyYAML`` package
installed in your system's Python. It is a very useful package, but it is not
part of Python's standard library. Suppose further that your package needed to
be able to parse YAML files. You therefore start using ``PyYAML``. Some time
later you want to share your code with your friend Alice. You run your tests,
*of course you are writing tests as you go along*, and they all pass. You feel
happy and send the package Alice. However, Alice has not yet installed the
``PyYAML`` package and consequently her first experience of your code is an
``ImportError``.

This ``ImportError`` could have been avoided by adding ``pyyaml`` as a
requirement to our ``setup.py`` file. For more details see the "Specifying
Dependencies" section in Scott Torborg's [How To Package Your Python
Code](http://www.scotttorborg.com/python-packaging/dependencies.html).

However, the question is *how could we have detected this issue before sending
our code to Alice?*

## Creating a virtual Python development environment

There is a way to avoid making use of the system's "polluted" Python, which
also lets us work without requiring root privileges. When I first heard about
this it sounded like magic.

The solution is to make use of
[virtualenv](https://virtualenv.pypa.io/en/latest/).
From the virtualenv website:

<blockquote><code>virtualenv</code> is a tool for creating isolated Python environments.</blockquote>

Let us install ``virtualenv`` using ``pip``.

```
$ pip install virtualenv
```

Now we can create a virtual environment for our project. However, before we do
that let me give you a tip: create a separate directory for storing all
your virtual environments and give each virtual environment a descriptive name.

```
$ mkdir ~/virtualenvs
```

If you are anything like me you will end up having at least one virtual
environment for each project you are working on.

```
$ virtualenv ~/virtualenvs/awesome
New python executable in /home/tjelvar/virtualenvs/awesome/bin/python
Installing setuptools, pip...done.
```

Note that this creates a directory named ``awesome`` in the ``~/virtualenvs``
directory. You could have named it anything, but I like to use the same name as
the project for which I intend to use the virtual environment. The
``~/virtualenvs/awesome`` directory contains the virtual environment.

To make use of a virtual environment we need to "activate" it. This is done by
sourcing the ``activate`` script in the ``bin`` directory of the virtual
environment.

To get a feel for the effect of activating the virtual environment let
us use ``which`` to find the path to ``python`` before and after we
activate the virtual environment.

```
$ which python
/usr/bin/python
```

Now let us activate the virtual environment we just created.

```
$ source ~/virtualenvs/awesome/bin/activate
(awesome)$ which python
/home/tjelvar/virtualenvs/awesome/bin/python
```

When we source the ``activate`` script above it basically alters the ``PATH``
and ``PS1`` environment variables. It also defines a ``deactivate`` function
that one can use to reset the environment variables to their original state.

```
(awesome)$ deactivate
$ which python
/usr/bin/python
```

## Tying it all together

That was a lot of pre-amble to be able to show a simple and effective work flow
for setting up clean Python development environments.

Generate a new Python project template.

```
$ cookiecutter gh:tjelvar-olsson/cookiecutter-pypackage
...
repo_name (default is "mypackage")? awesome
...
$ cd awesome
```

Create a virtual environment for the project.

```
$ virtualenv ~/virtualenvs/awesome
```

Activate the virtual environment and use ``setuptools`` to create a development
environment.

```
$ source ~/virtualenvs/awesome/bin/activate
(awesome)$ python setup.py develop
```

## Run the tests!

Tests are great, they let us know that things are working as intended. Let us
make sure that our setup is sound.

```
(awesome)$ python tests/tests.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

## Discussion

In this post I have shown you how to use ``setuptools`` and ``virtualenv`` to
create reproducible, clean and isolated Python development environments.

However, the work flow is not limited to development environments. It is just
as applicable to production environments and it is extensively used in the
Python web development community. In fact, having the same work flow for
setting up your development and production environments is a great bonus as it
gives you more confidence in the end product.
