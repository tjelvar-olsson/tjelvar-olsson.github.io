---
layout: post
title: "How to generate beautiful technical documentation"
comments: true
tags:
  - scientific computing
  - programming
  - software development
---

In the
[previous post]({% post_url 2015-06-28-five-tips-to-help-you-document-your-coding-project %})
I gave some motivational tips to inspire you to document your coding
project. In this post I will illustrate how you can convert documentation
written as plain text files into beautiful HTML documentation using a
tool called
[Sphinx](http://sphinx-doc.org).

## Installing Sphinx

Sphinx is a documentation generation tool written in Python and it can be
installed using ``pip``. If you do not yet have ``pip`` installed on your
system please have a look at the
[pip installation notes](https://pip.pypa.io/en/stable/installing.html).

Let us install Sphinx.

```
$ sudo pip install -U Sphinx
```

## Generating boilerplate files for the documentation

Suppose that were at the early stages of our project. All we have is
a ``README`` file with the content below.

```
README
======

This project aims to inspire people to write more and better documentation.
```

However, we know that we want to store more extensive documentation in
a subdirectory named ``docs``. Let us create that directory and add
some Sphinx boilerplate files to it.

```
$ mkdir docs
$ cd docs
$ sphinx-quickstart
```

The last command will prompt you with a bunch of questions on how
you want to setup your documentation and what extensions you want
to enable. I tend to accept the defaults for everything except the
question on whether or not I want to separate the source and
build directories.

```
> Separate source and build directories (y/n) [n]: y
```

The input fields for project name, author name(s) and project version require
you to provide some information. Below are the answers that I gave to these
questions in this instance.

```
> Project name: Better documentation
> Author name(s): Tjelvar Olsson
> Project version: 0.0.1
```

Let's see what was generated.

```
$ tree
.
├── Makefile
├── build
├── make.bat
└── source
    ├── _static
    ├── _templates
    ├── conf.py
    └── index.rst

4 directories, 4 files
 ```

Let us go through the files one by one. The ``Makefile`` allows us to build the
documentation using ``make``. The ``make.bat`` file allows us to build the documentation
on Windows based systems. The ``source/conf.py`` file contains configurations for building
the documentation (we will edit this later). The ``index.rst`` file is the root file of
the documentation we are about to write. 

## Let's build some documentation

Before we do anything else let us see what we get when we build the documentation.

```
$ make html
```

This will create output in the directory ``build/html``, open the ``build/html/index.html`` file
in your browser of choice. You should see something along the lines of the below.

![Sphinx default look](/images/sphinx_default_look.jpg)

Now have a look at the content of the ``source/index.rst`` file.

```
.. Better documentation documentation master file, created by
   sphinx-quickstart on Mon Jun 29 11:00:21 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Better documentation's documentation!
================================================

Contents:

.. toctree::
   :maxdepth: 2



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

The first section is a comment (the section starting with ``..``).  This is
followed by a header (denoted by the ``=`` underline).  The ``.. tocktree::``
section is Sphinx way of denoting that a list of other files should be included
(at the moment we have none). Finally, in the ``Indices and tables`` section
there are links to index, module and search pages. If you are documenting a
Python package the module page will contain links to the modules in your
package.

## Adding some more content

Let us add some more content. Create the file ``source/intro.rst`` and copy and
paste the text below into it.

```
Introduction
============

The purpose of this project is to help scientists write better documentation.
```

Now add a link to it in ``source/index.rst``.

```
Contents:

.. toctree::
   :maxdepth: 2

   intro
```

Note that the name of the file to be included ``intro`` needs to be indented to
the same level as ``:maxdepth:`` (by default this is three spaces). This has
caught me out many times as I tend to indent four spaces.

If you rebuild the documentation using ``make html`` you will see the content
of the ``source/intro.rst`` file included in the documentation.


## reStructuredText markup

You may have noticed that we can create headers by underlining them with
special characters. Sphinx uses reStructuredText as a markup language.
For a quick introduction to the reStructuredText syntax have a look at
[A ReStructuredText Primer](http://docutils.sourceforge.net/docs/user/rst/quickstart.html)
followed by
[Quick reStructuredText](http://docutils.sourceforge.net/docs/user/rst/quickref.html).
Another good source is Sphinx's
[ReStructuredText Primer](http://sphinx-doc.org/rest.html).


## Including code snippets in the documentation

Sphinx has taken advantage of the fact that reStructuredText is extensible and
have added directives of its own. We have already seen one of these: the ``toctree``
directive.

Let us have a look at the ``code-block`` directive, which can be used to include
code snippets. Create the file ``source/code_example.rst`` and add the text
below to it.

```
Code example
============

Here is a Python function.

.. code-block:: python

    def greet(name):
        print("Hello {}".format(name))

Here is a C function.

.. code-block:: C

    int add(int a, int b) {
        return a + b;
    }
```

Remember to include the file into the ``toctree`` of ``source/index.rst``.

```
.. toctree::
   :maxdepth: 2

   intro
   code_example
```

Use ``make html`` to build the documentation and behold the beautifully
generated code snippets included in your documentation.

It is also possible to include whole files of source code in your
documentation. Copy and paste the text below into a file named
``source/example_script.py``.

```python
"""This is an example script."""
import sys

def greet(name):
    """Return greeting."""
    return "Hello {}!".format(name)

if __name__ == "__main__":
    name = sys.argv[1]
    print(greet(name))
```

Now we will use Sphinx's ``include`` directory to include the content of this script
into the "Code example" page. Add the lines below to the end of the
``source/code_example.rst`` file.

```
Below is the content of a Python sample script.

.. literalinclude:: example_script.py
   :language: python
```

Sphinx also has several options for styling the display of your code snippets.
For example you can add line numbers and emphasize particular lines.  For more
inspiration on how to include code snippets in your documentation have a look
at
[Showing code examples](http://sphinx-doc.org/markup/code.html#includes)
in the Sphinx documentation.

## Generating API documentaiton for Python projects

Sphinx has got particularly good support for documenting Python projects.

Let us create a module named ``chemistry`` for us to document at the root level
of the project .

```
$ cd ../
$ mkdir chemistry
$ ls
README
docs
chemistry
``` 

Create the file ``chemistry/__init__.py`` and add the code below to it.

```python
"""Basic chemistry module.

The :mod:`chemistry` module contains three classes:

- :class:`chemistry.Atom`
- :class:`chemistry.Bond`
- :class:`chemistry.Molecule`

One can use the :func:`chemistry.Molecule.add_atom` and
:func:`chemsitry.Molecule.add_bond` functions to build up a molecule.

Example illustrating how to create a methane molecule.

>>> from chemistry import Molecule
>>> mol = Molecule('Methane')
>>> carbon_index = mol.add_atom(atomic_number=6)
>>> hydrogen1_index = mol.add_atom(atomic_number=1)
>>> hydrogen2_index = mol.add_atom(atomic_number=1)
>>> hydrogen3_index = mol.add_atom(atomic_number=1)
>>> hydrogen4_index = mol.add_atom(atomic_number=1)
>>> bond1_index = mol.add_bond(carbon_index, hydrogen1_index)
>>> bond2_index = mol.add_bond(carbon_index, hydrogen2_index)
>>> bond3_index = mol.add_bond(carbon_index, hydrogen3_index)
>>> bond4_index = mol.add_bond(carbon_index, hydrogen4_index)
"""

class Atom(object):
    """Class representing an atom."""

    def __init__(self, atomic_number):
        self.atomic_number = atomic_number
        self.bonds = []

    def bond_to(self, other_atom):
        """Return the :class:`chemistry.Bond` formed between the two atoms.

        :param other_atom: :class:`chemistry.Atom` to form :class:`chemistry.Bond` to
        :returns: :class:`chemistry.Bond`
        """
        bond = Bond(self, other_atom)
        self.bonds.append(bond)
        other_atom.bonds.append(bond)
        return bond

class Bond(object):
    """Class representing a bond between two atoms."""
    
    def __init__(self, atom1, atom2):
        self.atoms = (atom1, atom2)

class Molecule(object):
    """Class representing a molecule consisting of atoms and bonds."""

    def __init__(self, identifier):
        self.identifier = identifier
        self.atoms = []
        self.bonds = []

    def add_atom(self, atomic_number):
        """Return the list index of the atom added to the molecule.

        :param atomic_number: atomic number of the atom to be added
        :returns: index of the atom in the molecule
        """
        atom = Atom(atomic_number)
        self.atoms.append(atom)
        return len(self.atoms) - 1

    def add_bond(self, atom1_index, atom2_index):
        """Return the list index of the bond added to the molecule.

        :param atom1_index: atom's index in molecule
        :param atom2_index: atom's index in molecule
        :returns: index of the bond in the molecule
        """
        atom1 = self.atoms[atom1_index]
        atom2 = self.atoms[atom2_index]
        bond = atom1.bond_to(atom2)
        self.bonds.append(bond)
        return len(self.bonds) - 1
```

We will now use Sphinx's ``autodoc`` functionality to generate API
documentation for this module. First of all we need to add the
``sphinx.ext.autodoc`` extension to the ``docs/source/conf.py`` file.

```python
# Add any Sphinx extension module names here, as strings. They can be
# extensions coming with Sphinx (named 'sphinx.ext.*') or your custom
# ones.
extensions = ['sphinx.ext.autodoc']
```

In the same file we also need to specify the path to the module that we want to
generate documentation for.

```python
# If extensions (or modules to document with autodoc) are in another directory,
# add these directories to sys.path here. If the directory is relative to the
# documentation root, use os.path.abspath to make it absolute, like shown here.
sys.path.insert(0, os.path.abspath('../../'))
```

Now create the file ``docs/source/api.rst`` and copy and paste the text below
into it.

```
API documentaiton
=================

.. automodule:: chemistry
   :members:
```

We also need to remember to include the ``api.rst`` file in the ``toctree``.
Edit the ``docs/source/index.rst`` file to match the below.

```
.. toctree::
   :maxdepth: 2

   intro
   code_example
   api
```

Finally, regenerate the documentation by running ``make html`` in the ``docs``
directory and behold the beautifully generated API documentation.

If you interact with the generated HTML documentation you will note that the
constructs below have been converted into hyperlinks.

```
:mod:`chemistry`
:class:`chemistry.Molecule`
:func:`chemistry.Molecule.add_atom`
```

These directives can be used anywhere in your documentation to link to the
relevant section in the API documentation. Having descriptive documentation
that contains links to the more technical API documentation is very
pleasant and these directives make it very easy to do so.

It is also worth commenting on the ``:param:`` and ``:returns:`` formatting
used in the docstrings. These are part of a larger set of description directives
that are formatted nicely by Sphinx. For more information have a look at the
[info field list section](http://sphinx-doc.org/domains.html#info-field-lists)
in the Sphinx documentation.

## What about the original README file?

Let us finish off by including the content of the original ``README`` file
into the generated HTML documenation.

Create the file ``docs/source/README.rst`` and copy and paste the text below into it.

```
.. include:: ../../README
```

This will include the content of the top level ``README`` file into the documentation.


## Styling the documentaiton

The default theme of Sphinx is currently
[Alabaster](https://github.com/bitprophet/alabaster). It is very beautiful. However,
personally I prefer the ReadTheDocs theme. In particular because of its left hand side
navigation bar. Let's check it out.

First of all we install the theme using ``pip``.

```
$ pip install sphinx_rtd_theme
```

Now we need to edit theme section in ``docs/source/conf.py`` to look like the
below.

```python
# The theme to use for HTML and HTML Help pages.  See the documentation for
# a list of builtin themes.
#html_theme = 'alabaster'

# on_rtd is whether we are on readthedocs.org
on_rtd = os.environ.get('READTHEDOCS', None) == 'True'

if not on_rtd:  # only import and set the theme if we're building docs locally
    import sphinx_rtd_theme
    html_theme = 'sphinx_rtd_theme'
    html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]

# otherwise, readthedocs.org uses their theme by default, so no need to specify it
```

Note that the code above includes some logic for handling the cases where one
hosts the documentation on [readthedocs](https://readthedocs.org).

Regenerate the documentation by running ``make html`` in the ``docs`` directory
and explore the look and feel of this new theme. Note in particular the
behaviour of the left hand side navigation bar and the clear "next" and
"previous" buttons at the bottom of each page.

![Sphinx rdt theme](/images/rtd_theme.jpg)

For more information on the ``readthedocs`` theme have a look
[here](https://read-the-docs.readthedocs.org/en/latest/theme.html).


## ReadTheDocs

Whilst on the subject it is worth mentioning the ability to host your documentation
on . Simply sign-up for an account, link your
GitHub/BitBucket account and then you can select the projects that you want to host
on [readthedocs](https://readthedocs.org). It is great!

It is worth noting that if your project documentation includes links to packages
such as ``numpy`` and ``scipy`` you will need to mock these out in the
``conf.py`` file. For more information have a look at this
[readthedocs faq](https://read-the-docs.readthedocs.org/en/latest/faq.html#i-get-import-errors-on-libraries-that-depend-on-c-modules).
For a real life example have a look at
[this conf.py](https://github.com/JIC-CSB/jicimagelib/blob/master/docs/source/conf.py).


## Further reading

I hope this post has inspired you to try out Sphinx. It is a wonderful tool for
generating beautiful documentation!

Below are a couple links to other resources on how to use Sphinx.

- [Official Sphinx documentaiton](http://sphinx-doc.org/index.html)
- [Andrew Carter's: Documenting Your Project Using Sphinx](https://pythonhosted.org/an_example_pypi_project/sphinx.html)


