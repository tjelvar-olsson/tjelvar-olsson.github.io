---
layout: post
title: "Five steps to show off the quality of your Python package"
comments: true
tags:
  - scientific computing
  - programming
  - software development
  - test-driven development 
---

## Introduction

In previous posts I have shown how to create a Python package.

We started by 
[using Cookicutter to generate a template for our package]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %}).
We then looked at
[how to setup and use clean development environments]({% post_url 2015-05-09-begginers-guide-creating-clean-python-development-environments %}).
This was followed by an
[outline of Python tools for testing]({% post_url 2015-05-30-four-tools-for-testing-your-python-code %})
and the implementation of the Python package using
[test-driven development]({% post_url 2015-06-13-test-driven-develpment-for-scientists %}).
Finally we looked at
[how to generate beautiful technical documentation]({% post_url 2015-07-11-how-to-generate-beautiful-technical-documentation %}).

Now it is time to show off our hard work. In this post I will show you how to
make use of cloud services to host your documentation, run continuous
integration tests and distribute your package. Furthermore, we will add
neat looking badges to the README file.

## Step 1: Host the documentation on readthedocs

You have spent hour documenting your package using Sphinx. It is time to share
it with the world.  Register with [readthedocs](https://readthedocs.org) and
sync your GitHub account with it. Then you can simply select the project that
you readthedocs to host documentation for.

If you are using Sphinx's [http://sphinx-doc.org/ext/autodoc.html](autodoc) and
your package depends on numpy/scipy/matplotlib you may run into trouble as the
readthedoc server will not compile the C extensions. The first thing to try to
work around this is to go into the advanced settings section of your project in
the readthedocs web interface and make sure that the project is set to install
into a virutalenvironment using ``setup.py install`` and that the checkbox to
"Give the virtual environment access to the global site-packages dir" is
selected. The system packages now seem to include numpy, scipy, and matplotlib
so this should go a long way. However if you are still running into trouble you
may need to
[mock out the dependencies](https://read-the-docs.readthedocs.org/en/latest/faq.html#i-get-import-errors-on-libraries-that-depend-on-c-modules).

## Step 2: Set up continuous integration testing on Travis Ci

You have spent hours using test-driven development to create a solid Python
package. It is time to automate the running of the test suite and to get
automatic testing of the code on different versions of Python.

Sign into [Travis CI](https://travis-ci.org) using your GitHub account. Select
the project that you want to test and add a ``.travis.yml`` file to the root
of your code repository.

Below is a simple setup for testing a Python package with no dependencies on
Python versions 2.7, 3.2, and 3.4 using the ``nose`` test runner.

```yaml
language: python
python:
  - "2.7"
  - "3.2"
  - "3.3"
  - "3.4"
script: nosetests
```

If your code includes dependencies on ``numpy`` and ``scipy`` things get a
little bit trickier as Travis can time out trying to install these from source.
The solution is to make use of [Miniconda](http://conda.pydata.org/docs/index.html).

The ``.travis.yml`` file below is based on the template from the 
[conda documentation](http://conda.pydata.org/docs/travis.html#using-conda-with-travis-ci)
and Dan Blanchard's post
[Quicker Travis builds that rely on numpy and scipy using Miniconda](https://gist.github.com/dan-blanchard/7045057).
It installs Miniconda with ``numpy``, ``scipy`` and ``nose`` and runs the test
suite on Python 2.7, 3.3. and 3.4.

```yaml
python:
  # We don't actually use the Travis Python, but this keeps it organized.
  - "2.7"
  - "3.3"
  - "3.4"
install:
  - sudo apt-get update
  # We do this conditionally because it saves us some downloading if the
  # version is the same.
  - if [[ "$TRAVIS_PYTHON_VERSION" == "2.7" ]]; then
      wget https://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh -O miniconda.sh;
    else
      wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh;
    fi
  - bash miniconda.sh -b -p $HOME/miniconda
  - export PATH="$HOME/miniconda/bin:$PATH"
  - hash -r
  - conda config --set always_yes yes --set changeps1 no
  - conda update -q conda
  # Useful for debugging any issues with conda
  - conda info -a
  - conda create -q -n test-environment python=$TRAVIS_PYTHON_VERSION numpy scipy nose
  - source activate test-environment
  - python setup.py install
# command to run tests
script: nosetests
```

## Step 3: Calculate your code coverage using Codecov

As you have developed your code using test-driven development you have a high
coverage. It is time to integrate the code coverage calculation into the Travis CI testing.
We will use [Codecov.io](https://codecov.io) to do this.

Sign in using your GitHub account, sync your repos and add the one that you want to test.
Then edit the ``.travis.yml`` file to look like the below.

```yaml
language: python
python:
  - "2.7"
  - "3.2"
  - "3.3"
  - "3.4"
script: nosetests
before_install:
  pip install codecov
after_success:
  codecov
```

## Step 4: Upload your Package to PyPi

You have developed a great Python package it is time to share it with the world.
This is done most effectively by uploading it to [PyPi](https://pypi.python.org/pypi).

Peter Down's has written a great post explaining
[How to submit a package to PyPi](http://peterdowns.com/posts/first-time-with-pypi.html).

Hosting your package on PyPi makes it easy for people to install using ``pip``.


## Step 5: Add badges to your README file

Now finally the part that we have all been waiting for: badges!

Readthedocs, TravisCI and Codecov.io all provide badges as part of their service. For the PyPi
package we will make use of [Version Badge](http://badge.fury.io).

Below is part of the reStructuredText markup I use for my ``tinyfasta`` package.

```
.. image:: https://badge.fury.io/py/tinyfasta.svg
   :target: http://badge.fury.io/py/tinyfasta
   :alt: PyPI package

.. image:: https://travis-ci.org/tjelvar-olsson/tinyfasta.svg?branch=master
   :target: https://travis-ci.org/tjelvar-olsson/tinyfasta
   :alt: Travis CI build status (Linux)

.. image:: https://codecov.io/github/tjelvar-olsson/tinyfasta/coverage.svg?branch=master
   :target: https://codecov.io/github/tjelvar-olsson/tinyfasta?branch=master
   :alt: Code Coverage

.. image:: https://readthedocs.org/projects/tinyfasta/badge/?version=latest
   :target: https://readthedocs.org/projects/tinyfasta/?badge=latest
   :alt: Documentation Status
```

On GitHub the [README.rst](https://github.com/tjelvar-olsson/tinyfasta/blob/master/README.rst)
file renders itself into a neat looking header with the badges below.

![PyPI package](http://badge.fury.io/py/tinyfasta.svg) ![Travis CI build status (Linux)](https://travis-ci.org/tjelvar-olsson/tinyfasta.svg?branch=master)
![Code Coverage](https://codecov.io/github/tjelvar-olsson/tinyfasta/coverage.svg?branch=master)
![Documentation Status](https://readthedocs.org/projects/tinyfasta/badge/?version=latest)


## Conclusion

You should now have a Python package that looks loved and cared for. It is easy
to install using ``pip``. The package has online documentation. Furthermore it
is tested every time code is pushed to GitHub and the test coverage is
measured.
