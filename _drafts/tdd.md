---
layout: post
title: "Test driven development"
comments: true
tags:
  - scientific computing
  - programming
  - software development
  - test driven development 
---

## Introduction

In
[Three essential tips for improving your scientific code]({% post_url 2015-02-28-three-essential-tips-for-improving-your-scientific-code %})
I talked about the importance of writing tests for your scientific code base.
Tests provides a means to verify that new code does what it is intended to do
and a means to alert you if you inadvertently break an existing piece of
functionality when modifying the code base.

Furthermore, if you have a well tested code base you feel less scared of making
changes to it. Have you ever worked on a code base and thought to yourself:

<blockquote>
I could really do with re-writing this to make it simpler, but I'm not sure what
else I would break....
</blockquote>

If your code base had better test coverage you would not feel this way.
Having tests give you the ability to make sweeping changes to the code base
whilst retaining confidence that you have not broken any vital piece of
functionality.

Tests also provide a type of living documentation of your code base, a
specification of how the code is intended to work.

In fact tests are so important that some people write them before they write
any code in a method known as test-driven development.

In this post we will make use of the skills we built up in
[Four tools for testing your Python code]({% post_url 2015-05-30-four-tools-for-testing-your-python-code %})
to explore test-driven development. We will use test-driven development to
create a Python FASTA parser package.

## What is test-driven development?

Test-driven development can be thought of as a three step process.

1. Write a test for the functionality that you have in mind and watch it fail
2. Write minimal code to make the test pass
3. Re-factor the code if required

Don't worry if the above does not make sense. The purpose of the rest of this
post is to illustrate how this works in practise.

## Spiking

It is not wrong to develop code without tests. However, if you are doing test
driven-development you should treat such exploratory code as "throw away" and
use it as a guide to write tests when doing things property. In this context
"properly" means writing the tests first. People who practise test
driven-development refer to such exploratory coding as a 
[spike](http://stackoverflow.com/questions/249969/why-are-tdd-spikes-called-spikes).
Here we will treat the exploration from the
[prevoius FASTA post](2015-03-22-object-oriented-programming-for-scientists) as
a spike.


## Creating a project template

We will start by creating a project template using
[cookiecutter]({% post_url 2015-05-04-using-cookiecutter-a-passive-code-generator %})

```
$ cookiecutter gh:tjelvar-olsson/cookiecutter-pypackage
...
repo_name (default is "mypackage")? tinyfasta
version (default is "0.0.1")? 
authors (default is "Tjelvar Olsson")? 
...
$ cd tinyfasta
```

And setting up a
[clean Python development environment]({% post_url 2015-05-09-begginers-guide-creating-clean-python-development-environments %}).

```
$ virtualenv ~/virtualenvs/tinyfasta
$ source ~/virtualenvs/tinyfasta/bin/activate
(tinyfasta)$ python setup.py develop
```

Note that you can view this project and its progression on
[GitHub](https://github.com/tjelvar-olsson/tinyfasta).

## Start with a functional test

When practising test driven development it is often useful to start with a
functional test. A functional test differs from a unit test in that it tests
a slice of functionality in the system as opposed to an individual unit.
The rational for starting with a functional test is that it allows us to take a
step back and think about the larger picture.

We can translate the learning from our spike into a functional test.

[ebcc524 ``tests/tests.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/ebcc524fed6f596cc749e9bbb439ab47f4398aeb/tests/tests.py)

```python
    def test_output_is_consistent_with_input(self):
        from tinyfasta import FastaParser
        input_fasta = os.path.join(DATA_DIR, "dummy.fasta")
        output_fasta = os.path.join(TMP_DIR, "tmp.fasta")
        with open(output_fasta, "w") as fh:
            for fasta_record in FastaParser(input_fasta):
                fh.write("{}\n".format(fasta_record))
        input_data = open(input_fasta, "r").read()
        output_data = open(output_fasta, "r").read()
        self.assertEqual(input_data, output_data)
```

And add a sample test file
[tests/data/dummy.fasta](https://github.com/tjelvar-olsson/tinyfasta/blob/ebcc524fed6f596cc749e9bbb439ab47f4398aeb/tests/data/dummy.fasta).


## Start building up functionality using unit tests

Another reason for starting with a functional test is that it can act as a
guide for what to implement. When we run the functional test we immediately
find out that we need a class named ``FastaParser``.

```
Traceback (most recent call last):
  File "/Users/olssont/junk/tinyfasta/tests/tests.py", line 31, in test_output_is_consistent_with_input
    from tinyfasta import FastaParser
ImportError: cannot import name FastaParser
```

At this point we add a unit test for initialising a ``FastaParser`` instance.

[a6d2253 ``tests/tests.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/a6d2253090fc56b7bd78b146ccf13dc00374fd03/tests/tests.py)

```python
    def test_FastaParser_initialisation(self):
        from tinyfasta import FastaParser
        fasta_parser = FastaParser('test.fasta')
        self.assertEqual(fasta_parser.fpath, 'test.fasta')
```

After having run the test and watched it fail we add minimal code to make the
unit test pass.

[a6d2253 ``tinyfasta/__init__.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/a6d2253090fc56b7bd78b146ccf13dc00374fd03/tinyfasta/__init__.py)

```python
class FastaParser(object):
    """Class for parsing FASTA files."""

    def __init__(self, fpath):
        """Initialise an instance of the FastaParser."""
        self.fpath = fpath
```

The implementation makes the unit test pass. So we continue by running the
functional test again.

```
Traceback (most recent call last):
  File "/Users/olssont/junk/tinyfasta/tests/tests.py", line 40, in test_output_is_consistent_with_input
    for fasta_record in FastaParser(input_fasta):
TypeError: 'FastaParser' object is not iterable
```

Okay, so we need a test to make sure that the class iterable.

[abfdeee ``tests/test.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/abfdeeea35fe5c5143a1a4831a1ce4d7523b3515/tests/tests.py)

```python
    def test_FastaParser_is_iterable(self):
        from tinyfasta import FastaParser
        fasta_parser = FastaParser('test.fasta')
        self.assertTrue(hasattr(fasta_parser, '__iter__'))
```

At this point it may be worth reflecting on how we should make this test pass.
In test driven development we want to add minimal implementation to get the
tests to pass. The code below is pretty minimal and it makes the test pass.


[abfdeee ``tinyfasta/__init__.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/abfdeeea35fe5c5143a1a4831a1ce4d7523b3515/tinyfasta/__init__.py)

```python
    def __iter__(self):
        """Yield FastaRecord instances."""
        yield None
```

As the docstring above suggests we want the ``FastaParser`` to yield
``FastaRecord`` instances. So at this point we can start building up the
``FastaRecord`` using small incremental steps of test and code. To get a
feel for this have a look at the commits:

- [cd34f8b Added FastaRecord class.](https://github.com/tjelvar-olsson/tinyfasta/commit/cd34f8b862974abbe1fad096a89ffc34b537c22b)
- [ef95643 Added sequence logic to FastaRecord.](https://github.com/tjelvar-olsson/tinyfasta/commit/ef95643fa53e60569a6f48b3275d57b56b5400a0)
- [873798b Added string representation of FastaRecord class.](https://github.com/tjelvar-olsson/tinyfasta/commit/873798b7c98606285ea809f9ea87f849bffd59e4)

At this point we have all the functionality we need to add a proper
implementation of the ``FastaParser.__iter__()`` method, which we hope will
make the functional test pass.

[75e3272 ``tinyfasta/__init__.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/75e32726b83a674b00bc2eee70d8f7ea3f9906c4/tinyfasta/__init__.py)

```python
    def __iter__(self):
        """Yield FastaRecord instances."""
        fasta_record = None
        with open(self.fpath, 'r') as fh:
            for line in fh:
                if line.startswith('>'):
                    if fasta_record:
                        yield fasta_record
                    fasta_record = FastaRecord(line)
                else:
                    fasta_record.add_sequence_line(line)
        yield fasta_record
```

Let us make sure that all the tests pass.

```
$ nosetests
........
Name        Stmts   Miss  Cover   Missing
-----------------------------------------
tinyfasta      26      0   100%
----------------------------------------------------------------------
Ran 8 tests in 0.027s

OK
```

Great we have a basic working implementation of our ``tinyfasta.py`` module.

## And iterate

Now that we have the basics implemented we want to add more functionality and
by now you know what that means: another test. As we are wanting to add new
functionality we start all over again with another functional test.

In the commit history of the ``tinyfasta`` project one can see how
functionality for searching the FASTA description line was added.

- [fb685cb Added functional test for FastaRecord.description_matches search.](https://github.com/tjelvar-olsson/tinyfasta/commit/fb685cb82aa947c8f1249120f093ecfb27cf3c50)
- [cd78c02 Added empty description_matches function.](https://github.com/tjelvar-olsson/tinyfasta/commit/cd78c020da21a0a07f06313eca891bac5418e6e6)
- [1b89336 Added description_matches implementation.](https://github.com/tjelvar-olsson/tinyfasta/commit/1b89336c960bc46e88593599bd24314c12801d1b)

Followed by functionality for searching the biological sequence.

- [5fe617d Added functional test for FastaRecord.sequence_matches function.](https://github.com/tjelvar-olsson/tinyfasta/commit/5fe617d0016af4ef5c6ecbca8f8670abe57b6f54)
- [e97e623 Added empty sequence_matches function.](https://github.com/tjelvar-olsson/tinyfasta/commit/e97e623f5d4749b95d794c18e192eca366c4636b)
- [4f27704 Added sequence_matches implementation.](https://github.com/tjelvar-olsson/tinyfasta/commit/4f27704d0ca7e85948ae901de5d9d0db37a2d871)


## Refactoring

Up until this point we have followed the work flow below

1. Write a test
2. Write minimal code to make the test pass

However, this is not the whole story it leaves out an important aspect of test
driven-development: refactoring. The real work flow should look more like the below.

![TDD workflow image.]()

Let us start with a simple example of factoring out code duplication. After
having added functionality for using either strings or compiled regular
expressions to search the description and sequence we notice that there is a
lot of code duplication.

[e748ac3 ``tinyfasta/__init__.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/e748ac3f3d50a43dcef23f81c8bddb49ed556917/tinyfasta/__init__.py)

```python
    def description_matches(self, search_term):
        """Return True if the search_term is in the description."""
        if hasattr(search_term, "search"):
            return search_term.search(self.description) is not None
        return self.description.find(search_term) != -1

    def sequence_matches(self, search_motif):
        """Return True if the motif is in the sequence.

        :param search_motif: string or compiled regex
        :returns: bool
        """
        if hasattr(search_motif, "search"):
            return search_motif.search(self.sequence) is not None
        return self.sequence.find(search_motif) != -1
```

As we have been using test-driven development we have tests for all the
functionality of interest. We can therefore refactor to look like the below.

[2b988b9 ``tinyfasta/__init__.py``](https://github.com/tjelvar-olsson/tinyfasta/blob/2b988b9d8b309ae4de6ae1a953078e834ead724c/tinyfasta/__init__.py)

```python
    @staticmethod
    def _match(string, search_term):
        """Return True if the search_term is in the string.
        :param string: string to be searched
        :param search_term: string or compiled regex
        :returns: bool
        """
        if hasattr(search_term, "search"):
            return search_term.search(string) is not None
        return string.find(search_term) != -1

    def description_matches(self, search_term):
        """Return True if the search_term is in the description.
        :param search_term: string or compiled regex
        :returns: bool
        """
        return FastaRecord._match(self.description, search_term)

    def sequence_matches(self, search_motif):
        """Return True if the motif is in the sequence.
        :param search_motif: string or compiled regex
        :returns: bool
        """
        return FastaRecord._match(self.sequence, search_motif)
```

And run the tests.

```
$ nosetests
......................
Name        Stmts   Miss  Cover   Missing
-----------------------------------------
tinyfasta      43      0   100%   
----------------------------------------------------------------------
Ran 22 tests in 0.032s

OK
```

As all the tests pass we can have some level of confidence that everything is
still working as intended.

## Improving the design of the code

At some point whilst documenting how to use the ``tinyfasta`` package I realised that
the function names ``description_matches`` and ``sequence_matches`` were a little bit
misleading and that the names ``description_contains`` and ``sequence_contains`` would
be more appropriate. This was a relatively simple change to make, see
[commit 0496373](https://github.com/tjelvar-olsson/tinyfasta/commit/0496373038942e31a7afb53549ee5d4e371c0312).

However, some time later I realised that it would be much nicer if the API of the
``tinyfasta`` package would allow code that looked like the below. Note that the
``description`` is no longer a function, but an instance of some sort which has
a ``contains`` function.

```python
>>> from tinyfasta import FastaParser
>>> for fasta_record in FastaParser("tests/data/dummy.fasta"):
...     if fasta_record.description.contains('seq1'):
...         print(fasta_record)
...
>seq1|contains 2x78 A's
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```

Although, the change to the feel of the API is minor (an underscore swapped for
a full stop), the change to the underlying behaviour of the ``tinyfasta``
package is major.

However, because of all the tests the change was not so hard to implement.
First I went into the tests and changed all the calls to the
``description_contains`` and ``sequence_contains`` to ``description.contains``
and ``sequence.contains``. Then I simply "listened to my tests" as they guided
me through all the changes that needed to be made for the package to become
functional again.  Have a look at
[commit 7fb248f](https://github.com/tjelvar-olsson/tinyfasta/commit/7fb248f7ce3029bd517abe887623ecbe5b68c23e)
to see the resulting changes to the code base.

## Summary


