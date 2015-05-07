---
layout: post
title: How to set up Python a development environment using setuptools and virtualenv
comments: true
tags:
  - python
  - software development
---

In the
[previous post]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %})
I illustrated how you could use a static code generator (``cookiecutter``) to
create a basic template to develop a Python package.

Now suppose that you wanted to develop an awesome Python package named
"awesome". You would then set up a basic structure for your project using you
Git hosted Cookiecutter template.

```bash
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

```bash
$ cd awesome/
```

With a number of files and directories in it.

```bash
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

```bash
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

That's not very good! What is going on? It seems that I cannot import the
``awesome`` module. Depending on your level of familiarity with Python the
problem may be obvious to you. However, when I started out with Python this
caused me a lot of confusion because I clearly could import the ``awesome``
module!

```bash
$ python -c "import awesome; print(awesome.__version__)"
0.0.1
```

When I run the command above one of the places Python looks for modules is the
current directory. However, when I run the ``tests/tests.py`` script Python
cannot find the ``awesome`` package in the ``tests`` directory. In other words
to get the same environment as experienced when running ``python tests/tests.py``
one would have to go into the ``tests`` directory first.

```bash
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
we started building up a basic ``setup.py`` file, which makes use of the
``setuptools`` module.

You are probably already familiar with this tool from installing other Python
packages using the command ``python setup.py install``. This installs the
package into you Python distributions ``site-packages`` directory. However,
this means that any changes that you make to the files in your development
directory will not take effect until you re-install the package.

The solution to this problem is to use ``python setup.py develop`` which
creates an ``.egg-link`` in your ``site-packages`` directory to your
development directory.

```bash
$ sudo python setup.py develop
Password:
running develop
Checking .pth file support in /Library/Python/2.7/site-packages/
/usr/bin/python -E -c pass
TEST PASSED: /Library/Python/2.7/site-packages/ appears to support .pth files
running egg_info
creating awesome.egg-info
writing awesome.egg-info/PKG-INFO
writing top-level names to awesome.egg-info/top_level.txt
writing dependency_links to awesome.egg-info/dependency_links.txt
writing manifest file 'awesome.egg-info/SOURCES.txt'
reading manifest file 'awesome.egg-info/SOURCES.txt'
writing manifest file 'awesome.egg-info/SOURCES.txt'
running build_ext
Creating /Library/Python/2.7/site-packages/awesome.egg-link (link to .)
Adding awesome 0.0.1 to easy-install.pth file

Installed /Users/olssont/junk/awesome
Processing dependencies for awesome==0.0.1
Finished processing dependencies for awesome==0.0.1
```

Now, because ``site-packages`` contains the ``awesome.egg-link``,
we can run the tests.

```bash
$ python tests/tests.py 
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

Suppose that we decide that we do not like this package and we would like to
remove it from our ``site-packages``? We can achieve this by running ``python
setup.py develop --uninstall``.

```bash
$ sudo python setup.py develop --uninstall
Password:
running develop
Checking .pth file support in /Library/Python/2.7/site-packages/
/usr/bin/python -E -c pass
TEST PASSED: /Library/Python/2.7/site-packages/ appears to support .pth files
Removing /Library/Python/2.7/site-packages/awesome.egg-link (link to .)
Removing awesome 0.0.1 from easy-install.pth file
```

So far so good, but there are two issues with what we are currently doing.
First of all we need to have root permissions to run ``python setup.py
develop`` and ``python setup.py develop --uninstall`` when using the system's
Python. Secondly, when using the system's Python are using a (potentially)
polluted environment.

Let me expand on the second issue. Suppose that you had the ``PyYAML`` package
installed in your system's Python. It is a very useful package, but it is not
part of Python's standard library. Because it is a very useful package and you
want to parse YMAL files you start using it in your code. You run your tests,
**of course you are writing test to test your code as you go along**, and they
all pass. You feel happy and send the package to a friend, who by the way has
not yet installed the ``PyYAML`` package. The first thing that your friend is
going to see is an ``ImportError``.

The correct way to deal with this situation would be to add ``pyyaml`` as a
requirement in your ``setup.py`` file. For more details see the "Specifying
Dependencies" section in Scott Torborg's
[How To Package Your Python Code](http://www.scotttorborg.com/python-packaging/dependencies.html).

However, the question is *how could we have detected this eariler?*

## Creating a virtual Python development environment

There is a way to avoid making use of the system's "polluted" Python, which
also lets us work without having root privileges. When I first heard about this
it sounded like magic.

The solution is to make use of
[virtualenv](https://virtualenv.pypa.io/en/latest/).
From the virtualenv website:

<blockquote><code>virtualenv</code> is a tool for creating isolated Python environments.</blockquote>

Let us install ``virtualenv`` using ``pip``.

```bash
pip install virtualenv
```

Now we can create a virtual environment for our project. However, before we do
that let me give you a suggestion: create a separate directory for storing all
your virtual environments and give each virtual environment a descriptive name.
If you are anything like me you will end up having at least one virtual
environment for each project you are working on.

Assuming that you are now in your newly created directory, run the command
below.

```bash
$ virtualenv awesome
New python executable in awesome/bin/python
Installing setuptools, pip...done.
```

Note that this creates a directory named ``awesome``. You could have named it
anything, often people use ``env``, but I like to use the same name as the
project for which I intend to use the virtual environment. The directory
contains the virtual environment.

To better get a feel for the effect of activating the virtual environment let
us use ``which`` to find out the path to ``python``.

```bash
$ which python
/usr/bin/python
```

Now let us activate the virtual environment we just created.

```bash
$ source ./awesome/bin/activate
(awesome)$ which python
/home/tjelvar/virtualenvs/awesome/bin/python
```

When we source the ``activate`` script above it basically alters the ``PATH``
and ``PS1`` (for the prompt) environment variables. It also defines a
``deactivate`` function that one can use to reset the environment variables to
their original state.

```bash
(awesome)$ deactivate
tjelvar@crunchbang:~/virtualenvs$ which python
/usr/bin/python
```

## Tying it all together

That was a lot of pre-amble to be able to show a simple and effective work flow
for setting up a clean Python development environment.

Create a virtual environment for the project.

```bash
$ cd ~/virtualenvs   # Direcotry where I keep my virtual environments.
$ virtualenv awesome
```

Generate a new Python project template.

```bash
$ cd ~/projects   # Directory where I keep my projects.
$ cookiecutter gh:tjelvar-olsson/cookiecutter-pypackage
...
repo_name (default is "mypackage")? awesome
...
$ cd awesome
```

Source the virtual environment and use ``setuptools`` to create a development
environment.

```bash
$ source ~/virtualenvs/awesome/bin/activate
(awesome)$ python setup.py develop
```

## Run the tests!

Tests are great, they let us know that things are working as intended. Let us
make sure that our setup is sound.

```bash
(awesome)$ python tests/tests.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

## Conclusion

In this post I have shown you how to use ``setuptools`` and ``virtualenv`` to
create reproducible, clean and isolated Python development environments.
However, the work flow is not limited to development environments. It is just
as applicable to production environments and it is extensively used in the
Python web development community. In fact, having the same work flow for
setting up your development and production environments is a great bonus as it
gives you more confidence in the end product.
