---
layout: post
title: "How to continuously test your Python code on Windows using AppVeyor"
comments: true
tags:
  - scientific computing
  - programming
  - software development
  - test-driven development 
---

In the
[previous post]({% post_url 2015-08-30-five-steps-to-add-the-bling-factor-to-your-python-package %})
I illustrated how to setup continuous integration testing of your Python code
using [Travis CI](https://travis-ci.org). Travis CI is great when working on
Linux. However, what can you do if you wanted to setup automated continuous
integration testing on Windows?

*To me, a Linux enthusiast, this problem sounded almost insurmountable...*

## AppVeyor to the rescue

However, it turns out that
[AppVeyor](http://www.appveyor.com)
has provided a service for solving this problem.

One simply needs to create an ``appveyor.yml`` file to configure the running of
the test suite. The code below creates a testing matrix for running the test
suite on 32-bit Python 2.7, 3.3 and 3.4 using the ``nosetests`` test runner.

```yaml
build: false

environment:
  matrix:
    - PYTHON: "C:\\Python27"
      PYTHON_VERSION: "2.7.8"
      PYTHON_ARCH: "32"

    - PYTHON: "C:\\Python33"
      PYTHON_VERSION: "3.3.5"
      PYTHON_ARCH: "32"

    - PYTHON: "C:\\Python34"
      PYTHON_VERSION: "3.4.1"
      PYTHON_ARCH: "32"


init:
  - "ECHO %PYTHON% %PYTHON_VERSION% %PYTHON_ARCH%"

install:
  - "%PYTHON%/Scripts/pip.exe install nose"
  - "%PYTHON%/Scripts/pip.exe install coverage"

test_script:
  - "%PYTHON%/Scripts/nosetests"
```

Note that we use ``pip`` to install the ``nose`` and ``coverage`` packages
before we run the test suite.

Commit and push this file and login to AppVeyor using your GitHub account. Sync
your GitHub repositories and then select the project you want AppVeyor to run
continuous integration testing on.

*Job done!*

## Using Minconda to test projects that depend on the ``numpy``/``scipy`` stack

Again testing projects that depend on ``numpy`` and ``scipy`` present problems
in that these packages take too long to build from scratch. However, just like
in the
[previous post]({% post_url 2015-08-30-five-steps-to-add-the-bling-factor-to-your-python-package %})
we can make use of [Miniconda](http://conda.pydata.org/docs/index.html).

In fact the kind people at AppVeyor have already deployed Minicoda to their
build workers
([github.com/appveyor/ci/issues/359](https://github.com/appveyor/ci/issues/359)).

So to test a project that depends on ``numpy`` and ``scipy`` one can simply
use the ``appveyor.yml`` file below.

```yaml
build: false

environment:
  matrix:
    - PYTHON_VERSION: 2.7
      MINICONDA: C:\Miniconda
    - PYTHON_VERSION: 3.4
      MINICONDA: C:\Miniconda3

init:
  - "ECHO %PYTHON_VERSION% %MINICONDA%"

install:
  - "set PATH=%MINICONDA%;%MINICONDA%\\Scripts;%PATH%"
  - conda config --set always_yes yes --set changeps1 no
  - conda update -q conda
  - conda info -a
  - "conda create -q -n test-environment python=%PYTHON_VERSION% numpy scipy nose"
  - activate test-environment
  - pip install coverage

test_script:
  - nosetests
```

The script above installs ``numpy``, ``scipy`` and ``nose`` using the
Conda package manager. However, the Conda package manager does not contain
the ``coverage`` package. We therefore install that using ``pip`` instead after
the virtual environment has been activated.

The fact that Miniconda is included in the AppVeyor makes it trivial to test
Python code with scientific dependencies.

*Great stuff!*

## See also

- Oliver Grisel and Kyle Kaster's tutorial
  [Building Binary Wheels for Windows using Appveyor](https://packaging.python.org/en/latest/appveyor.html)
- Robert T. McGibbon's
  [Python Appveyor Conda example](https://github.com/rmcgibbo/python-appveyor-conda-example)
