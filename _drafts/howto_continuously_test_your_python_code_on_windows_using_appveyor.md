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

First of all one needs a script for installing Python. Below is a
PowerShell script for achieving this. It is based on the sample script provided
by Oliver Grisel and Kyle Kaster, which can be found
[here](https://raw.githubusercontent.com/pypa/python-packaging-user-guide/master/source/code/install.ps1).
Copy and paste the code below into the file ``appveyor/install.ps1``.

```powershell
# Sample script to install Python and pip under Windows.
# Based on the sample script provided by Olivier Grisel and Kyle Kastner.
# https://raw.githubusercontent.com/pypa/python-packaging-user-guide/master/source/code/install.ps1
# License: CC0 1.0 Universal: http://creativecommons.org/publicdomain/zero/1.0/

$BASE_URL = "https://www.python.org/ftp/python/"
$GET_PIP_URL = "https://bootstrap.pypa.io/get-pip.py"
$GET_PIP_PATH = "C:\get-pip.py"


function DownloadPython ($python_version, $platform_suffix) {
    $webclient = New-Object System.Net.WebClient
    $filename = "python-" + $python_version + $platform_suffix + ".msi"
    $url = $BASE_URL + $python_version + "/" + $filename

    $basedir = $pwd.Path + "\"
    $filepath = $basedir + $filename
    if (Test-Path $filename) {
        Write-Host "Reusing" $filepath
        return $filepath
    }

    # Download and retry up to 5 times in case of network transient errors.
    Write-Host "Downloading" $filename "from" $url
    $retry_attempts = 3
    for($i=0; $i -lt $retry_attempts; $i++){
        try {
            $webclient.DownloadFile($url, $filepath)
            break
        }
        Catch [Exception]{
            Start-Sleep 1
        }
   }
   Write-Host "File saved at" $filepath
   return $filepath
}


function InstallPython ($python_version, $architecture, $python_home) {
    Write-Host "Installing Python" $python_version "for" $architecture "bit architecture to" $python_home
    if (Test-Path $python_home) {
        Write-Host $python_home "already exists, skipping."
        return $false
    }
    if ($architecture -eq "32") {
        $platform_suffix = ""
    } else {
        $platform_suffix = ".amd64"
    }
    $filepath = DownloadPython $python_version $platform_suffix
    Write-Host "Installing" $filepath "to" $python_home
    $args = "/qn /i $filepath TARGETDIR=$python_home"
    Write-Host "msiexec.exe" $args
    Start-Process -FilePath "msiexec.exe" -ArgumentList $args -Wait -Passthru
    Write-Host "Python $python_version ($architecture) installation complete"
    return $true
}


function InstallPip ($python_home) {
    $pip_path = $python_home + "/Scripts/pip.exe"
    $python_path = $python_home + "/python.exe"
    if (-not(Test-Path $pip_path)) {
        Write-Host "Installing pip..."
        $webclient = New-Object System.Net.WebClient
        $webclient.DownloadFile($GET_PIP_URL, $GET_PIP_PATH)
        Write-Host "Executing:" $python_path $GET_PIP_PATH
        Start-Process -FilePath "$python_path" -ArgumentList "$GET_PIP_PATH" -Wait -Passthru
    } else {
        Write-Host "pip already installed."
    }
}

function InstallPackage ($python_home, $pkg) {
    $pip_path = $python_home + "/Scripts/pip.exe"
    & $pip_path install $pkg
}

function main () {
    InstallPython $env:PYTHON_VERSION $env:PYTHON_ARCH $env:PYTHON
    InstallPip $env:PYTHON
    InstallPackage $env:PYTHON nose
    InstallPackage $env:PYTHON coverage
}

main
```

The code above downloads and installs Python and ``pip``. It then uses ``pip``
to install the ``nose`` and ``coverage`` packages.

Now we need an ``appveyor.yml`` file to run the test suite. The code below
creates a testing matrix for running the test suite on 32-bit Python 2.7, 3.3
and 3.4 using the ``nose`` test runner.

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
  - "powershell appveyor/install.ps1"

test_script:
  - "%PYTHON%/Scripts/nosetests"
```

Commit and push these files to GitHub. Now login to AppVeyor using your GitHub
account. Sync your GitHub repositories and then select the project you want
AppVeyor to run continuous integration testing on.

*Job done!*

## Using Minconda to test projects that depend on the ``numpy``/``scipy`` stack

Again testing projects that depend on ``numpy`` and ``scipy`` present problems
in that these packages take too long to build from scratch. However, just like
in the
[previous post]({% post_url 2015-08-30-five-steps-to-add-the-bling-factor-to-your-python-package %})
we can make use of [Miniconda](http://conda.pydata.org/docs/index.html).

In fact the kind people at AppVeyor have already provide Minicoda on their
build workers
([github.com/appveyor/ci/issues/359](https://github.com/appveyor/ci/issues/359)).

So to test a project that depends on ``numpy`` and ``scipy`` one could simply
add the ``appveyor.yml`` file below.

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
the ``coverage`` package so we install that using ``pip`` instead once the
virtual environment has been activated.

The fact that Miniconda is included in the AppVeyor build workers makes it
trivial to test Python code with scientific dependencies.

*Great stuff!*

## See also

- Oliver Grisel and Kyle Kaster's tutorial
  [Building Binary Wheels for Windows using Appveyor](https://packaging.python.org/en/latest/appveyor.html)
- Robert T. McGibbon's
  [Python Appveyor Conda example](https://github.com/rmcgibbo/python-appveyor-conda-example).
