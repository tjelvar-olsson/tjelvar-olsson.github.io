---
layout: post
title: Using Cookiecutter as a passive code generator
comments: true
tags:
  - python
  - software development
---

<figure>
  <img src="/images/cookiecutter.jpg" alt="Cookiecutters." />
  <figcaption>
  Using a tool to generate a templated result.
  </figcaption>
</figure>

In [The Pragmatic
Programmer](https://pragprog.com/book/tpp/the-pragmatic-programmer) Andrew Hunt
and David Thomas talk about the importance of code generators when faced with
the task of producing the same thing over and over. They further separate code
generators into two types: passive and active.

A passive code generator being one that saves on typing. It is run once, the
result is placed into version control and then the code is built upon by hand.

Whereas an active code generator is used to produce complete code by converting
a source of meta-data into language(s) of interest. Active code generators are
run frequently and as the resulting code is reproducible it is also disposable,
hence it does not need to be tracked in version control.

In this post I will show you how you can use a passive code generator to create
a basic layout for a Python package.


## Cookiecutter: a passive code generator

A classic example where passive code generators are useful is in setting up an
initial project structure. Let us take the example of creating a Python
package, in the simplest case you will want to create a ``setup.py`` file and a
directory with the desired package name containing an ``__init__.py`` file.
Scott Torborg has created a great tutorial on
[How To Package Your Python Code](http://www.scotttorborg.com/python-packaging/).

Several tools exist to deal with this type of scenario. However, I quite like
Audrey Roy's [Cookiecutter](https://github.com/audreyr/cookiecutter). Let us
illustrate it's use by creating a minimal template for a Python package.

Firs of all we install it using ``pip``.

```bash
$ sudo pip install cookiecutter
```

Now we will create a funny looking directory structure. It is funny looking because it uses the
[Jinja2](http://jinja.pocoo.org) templating syntax.

```bash
$ mkdir -p mypyproject/{{ "{{cookiecutter.repo_name"}}}}/{{ "{{cookiecutter.repo_name"}}}}
```

Now create the file ``myproject/cookicutter.json`` and add the code below to it.

```json
{
  "repo_name": "mypackage",
  "version": "0.0.1",
  "author": "Your Name"
}
```

Let us have a look at the directory structure we have created.

```bash
$ tree mypyproject/
mypyproject/
├── cookiecutter.json
└── {{cookiecutter.repo_name}}
    └── {{cookiecutter.repo_name}}

2 directories, 1 file
```

We now have enough boilerplate to run cookiecutter. Actually we have more
than enough, at this point we do not need the ``version`` and ``author``
variables.

Let us create an "awesome" Python package to see it in action.

```bash
$ cookiecutter mypyproject/
repo_name (default is "mypackage")? awesome
version (default is "0.0.1")? 
author (default is "Your Name")? Tjelvar Olsson
```

Note that that the prompts and default values are the key/value pairs specified
in the ``cookiecutter.json`` file.

Let us have a look at what was produced.

```bash
$ tree awesome/
awesome/
└── awesome

1 directory, 0 files
```

Ok, great - let us add an ``__init__.py`` file to the leaf
``myproject/{{ "{{cookiecutter.repo_name"}}}}/{{ "{{cookiecutter.repo_name"}}}}`` directory.

```bash
$ touch mypyproject/\{\{cookiecutter.repo_name\}\}/\{\{cookiecutter.repo_name\}\}/__init__.py
```

In the above we need to esacape the ``{`` and ``}`` characters when using bash.
If you are not already using tab completion when using bash this may be a good
point to try it out (just start typing the name of the file/directory of
interest and then press the tab key). 

Let's run ``cookiecutter`` again to see what we get now that we have added the
``__init__.py`` file.

```bash
$ cookiecutter mypyproject/
repo_name (default is "mypackage")? awesome
version (default is "0.0.1")? 
author (default is "Your Name")? Tjelvar Olsson
```

```bash
$ tree awesome/
awesome/
└── awesome
    └── __init__.py

1 directory, 1 file
```

Great we now automatically get an ``__init__.py`` file added to our project
when we create it. Now let us add a basic, but all the same templated,
``setup.py`` file to our project layout. Create the file
``mypyproject/{{ "{{cookiecutter.repo_name"}}}}/setup.py`` and copy and paste the code
below into it.

```python
from setuptools import setup

setup(name="{{ "{{ cookiecutter.repo_name "}}}}",
      version="{{ "{{ cookiecutter.version "}}}}",
      author="{{ "{{ cookiecutter.author "}}}}"
)
```

Let us try this out.

```
$ cookiecutter mypyproject/
repo_name (default is "mypackage")? awesome
version (default is "0.0.1")? 
author (default is "Your Name")? Tjelvar Olsson
```

```bash
$ tree awesome/
awesome/
├── awesome
│   └── __init__.py
└── setup.py

1 directory, 2 files
```

```bash
$ cat awesome/setup.py 
from setuptools import setup

setup(name="awesome",
      version="0.0.1",
      author="Tjelvar Olsson"
)
```

Great we now have a basic layout for building up a Python project!

Now that you know the principles you can use them to automate the generation of
your boilerplate code.


## Making use of GitHub

Once you start building up your template make sure that you save it on GitHub
or BitBucket. *You are already using version control, right?*

A nice feature of Cookiecutter is that it has built in functionality for making
use of templates stored in GitHub/Bitbucket. For example to make use of my
default Python package layout, which includes:

- setup.py
- test suite layout using nose and coverage
- sphinx docs layout using read the docs theme

You can simply use the command below.

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

Alternatively, for an even more extensive setup have a look at [Audrey Roy's
ultimate python package
tempalate](https://github.com/audreyr/cookiecutter-pypackage).

## Summary

When you find yourself repeatedly doing the same thing it may be time to start
thinking about using a code generator. In this post I have shown you how to
use ``cookiecutter`` to produce a basic Python package template.

However, it is not limited to Python package projects you could use
it to automate the setup of CMake / HTML / LaTeX files; the world is your
oyster.

Happy code generating!
