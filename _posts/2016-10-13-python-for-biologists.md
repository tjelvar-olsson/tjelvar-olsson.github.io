---
layout: post
title: "Python for biologists"
comments: true
tags:
  - biology
  - bioinformatics
  - computational biology
  - systems biology
---

Python is a high-level scripting language that is growing in popularity in the
scientific community. It uses a syntax that is relatively easy to get to grips
with and that encourages code readability.

This post aims to give you a flavour of what it feels like to work with Python.
We will use Python to calculate the 
[guanine-cytosine (GC) content](https://en.wikipedia.org/wiki/GC-content) of
a DNA sequence. In the process you will also learn about some key aspects of
programming namely variables, functions and loops.

## Getting a flavour of Python

The most traditional way of working with Python is to write your code in a script
and run it using the ``python`` command. For example, if you had your code
in a file named ``analysis.py`` you could run it using the command below.

```
$ python analysis.py
```

However, there are other ways of interacting with Python.

These days so called "notebooks" are becoming more and more popular.
They are used for creating and sharing documents that include explanations of
code as well as code blocks that can be run interactively. Check out the
[Jupyter](http://jupyter.org) project for more details.

Python can also be run interactively in your terminal
using its
[interactive mode](https://docs.python.org/2/tutorial/interpreter.html#interactive-mode]).

To start Python in its interactive mode simply type ``python`` into your terminal.

```
$ python
Python 2.7.10 (default, Jul 14 2015, 19:46:27)
[GCC 4.2.1 Compatible Apple LLVM 6.0 (clang-600.0.39)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

This prints out information about the version of Python that is being used
and how it was compiled before leaving you at the interactive prompt. In this instance
I am using Python version 2.7.10.

The three greater than signs (``>>>``) represent the primary prompt into which
commands can be entered.

```python
>>> 1 + 2
3
```

There is also a secondary prompt that is represented by three dots (``...``).
It is used as a continuation line.

```python
>>> line = ">myseq1"
>>> if line.startswith(">"):
...     print(line)
...
>myseq1
```

The rest of this post will use this "interactive" Python format. You can try to
follow along using Python running interactively in a terminal or using a
Python notebook.  You can access a Python notebook from [Try
Jupyter](https://try.jupyter.org/).


## Variables

A variable is a means of storing a piece of information using using a
descriptive name. The use of variables is encouraged as it allows us to
avoid having to repeat ourselves.

In Python variables are assigned using the equals sign.

```python
>>> pi = 3.14
```

When naming variables being explicit is more important than being succinct.
One reason for this is that you will spend more time reading your code than
you will writing it. Avoiding the mental overhead of trying to understand
what all the acronyms mean is a good thing. For example, suppose that we
wanted to create a variable for storing the radius of a circle. Please
avoid the temptation of naming the variable ``r``, and go for the longer
but more explicit name ``radius``.

```python
>>> radius = 1.5
```

## Determining the GC count of a sequence

One feature of interest when examining DNA is the
[guanine-cytosine (GC) content](https://en.wikipedia.org/wiki/GC-content).
DNA with high GC-content is more stable than DNA with low GC-content.

Suppose that we had a string representing a DNA sequence.

```python
>>> dna_string = "attagcgcaatctaactacactactgccgcgcggcatatatttaaatata"
>>> print(dna_string)
attagcgcaatctaactacactactgccgcgcggcatatatttaaatata
```
A string is a data type for representing text. As such it is not ideal for data
processing purposes. In this case the DNA sequence would be better represented
using a "list", with each item in the list representing a DNA letter. A list,
also known as an array, is a data structure representing a collection of elements
with a specific order. 

In Python we can convert a string into a list using the built-in ``list()``
function.

```python
>>> dna_list = list(dna_string)
>>> print(dna_list)
['a', 't', 't', 'a', 'g', 'c', 'g', 'c', 'a', 'a', 't', 'c',
 't', 'a', 'a', 'c', 't', 'a', 'c', 'a', 'c', 't', 'a', 'c',
 't', 'g', 'c', 'c', 'g', 'c', 'g', 'c', 'g', 'g', 'c', 'a',
 't', 'a', 't', 'a', 't', 't', 't', 'a', 'a', 'a', 't', 'a',
 't', 'a']
```

Python's list has got a method called ``count()`` that we can use to find out
the counts of particular elements in the list.

```python
>>> dna_list.count("a")
17
```

To find out the total number of items in a list one can use Python's built-in
``len()`` function, which returns the length of the list.

```python
>>> len(dna_list)
50
```

When using Python you need to be careful when dividing integers, because in
Python 2 the default is to use integer division, i.e. to discard the remainder.

```python
>>> 3 / 2
1
```

One can work around this by ensuring that at least one of the numbers is
represented using floating point.

```python
>>> 3 / 2.0
1.5
```

*Warning: In Python 3, the behaviour of the division operator has been
changed, and dividing two integers will result in normal division.*

One can convert an integer to a floating point number using Python's built-in
``float()`` function. 

```python
>>> float(2)
2.0
```

We now have all the information required to calculate the GC-content of the DNA
sequence.

```python
>>> gc_count = dna_list.count("g") + dna_list.count("c")
>>> gc_frac = float(gc_count) / len(dna_list)
>>> 100 * gc_frac
38.0
```

## Creating reusable functions

Suppose that we wanted to calculate the GC-content for several sequences. In
this case it would be very annoying, and error prone, to have to enter the
commands above into the Python shell manually for each sequence. Rather, it
would be advantageous to be able to create a piece of code that could be called
repeatedly to calculate the GC-content. We can achieve this using the concept of
functions. In other words functions are a means for programmers to avoid repeating
themselves.

Let us create a simple function that adds two items together.

```python
>>> def add(a, b):
...     return a + b
...
>>> add(2, 3)
5
```

In Python functions are defined using the ``def`` keyword. Note that the
``def`` keyword is followed by the name of the function. The name of the
function is followed by a parenthesized set of arguments, in this case the
function takes two arguments ``a`` and ``b``. The end of the function
definition is marked using a colon.

The body of the function, in this example the ``return`` statement, needs to be
indented. The standard in Python is to use four white spaces to indent code
blocks. In this case the function body only contains one line of code. However,
a function can include several indented lines of code.

*Warning: Whitespace really matters in Python! If your code is not correctly
aligned you will see ``IndentationError`` messages telling you
that everything is not as it should be. You will also run into
``IndentationError`` messages if you mix white spaces and tabs.*

Now we can create a function for calculating the GC-content of a sequence.
As with variables explicit trumps succinct in terms of naming.

```python
>>> def gc_content(sequence):
...     gc_count = sequence.count("g") + sequence.count("c")
...     gc_fraction = float(gc_count) / len(sequence)
...     return 100 * gc_fraction
...
>>> gc_content(dna_list)
38.0
```

## List slicing

Suppose that we wanted to look at local variability in GC-content. To achieve
this we would like to be able to select segments of our initial list. This is
known as "slicing", as in slicing up a salami.

In Python slicing uses a ``[start:end]`` syntax that is inclusive for the start
index and exclusive for the end index. To illustrate slicing let us first
create a list to work with.

```python
>>> zero_to_five = ["zero", "one", "two", "three", "four", "five"]
```

To get the first two elements we therefore use 0 for the start index, as
Python uses a zero-based indexing system, and 2 for the end index as the
element from the end index is excluded.

```python
>>> zero_to_five[0:2]
['zero', 'one']
```

Note that the start position for the slicing is 0 by default so we could just
as well have written.

```python
>>> zero_to_five[:2]
['zero', 'one']
```

To get the last three elements.

```python
>>> zero_to_five[3:]
['three', 'four', 'five']
```

We can use list slicing to calculate the local GC-content measurements of
our DNA.

```python
>>> gc_content(dna_list[:10])
40.0
>>> gc_content(dna_list[10:20])
30.0
>>> gc_content(dna_list[20:30])
70.0
>>> gc_content(dna_list[30:40])
50.0
>>> gc_content(dna_list[40:50])
0.0
```

## Loops

It can get a bit repetitive, tedious, and error prone specifying all the ranges
manually. A better way to do this is to make use of a loop construct. A loop
allows a program to cycle through the same set of operations a number of times.

In lower level languages ``while`` loops are common because they operate in a way
that closely mimic how the hardware works. The code below illustrates a typical
setup of a while loop.

```python
    >>> cycle = 0
    >>> while cycle < 5:
    ...     print(cycle)
    ...     cycle = cycle + 1
    ...
    0
    1
    2
    3
    4
```

In the code above Python moves through the commands in the while loop executing
them in order, i.e. printing the value of the ``cycle`` variable and then
incrementing it. The logic then moves back to the ``while`` statement and
the conditional (``cycle < 5``) is re-evaluated. If true the commands in the
while statment are executed in order again, and so forth until the conditional
is false. In this example the ``print(cycle)`` command was called five times,
i.e. until the ``cycle`` variable incremented to 5 and the ``cycle < 5``
conditional evaluated to false.

However, when working in Python it is much more common to make use of ``for``
loops. For loops are used to iterate over elements in data structures such as
lists.

```python
>>> for item in [0, 1, 2, 3, 4]:
...     print(item)
...
0
1
2
3
4
```

In the above we had to manually write out all the numbers that we wanted. However,
because iterating over a range of integers is such a common task Python has a
built-in function for generating such lists.

```python
>>> range(5)
[0, 1, 2, 3, 4]
```

So a typical for loop might look like the below.

```python
>>> for item in range(5):
...     print(item)
...
0
1
2
3
4
```

The ``range()`` function can also be told to start at a larger number. Say for
example that we wanted a list including the numbers 5, 6 and 7.

```python
>>> range(5, 8)
[5, 6, 7]
```

As with slicing the start value is included whereas the end value is excluded.

It is also possible to alter the step size. To do this we must specify the start
and end values explicitly before adding the step size.

```python
>>> range(0, 50, 10)
[0, 10, 20, 30, 40]
```

We are now in a position where we can create a naive loop for for calculating
the local GC-content of our DNA.

```python
>>> for start in range(0, 50, 10):
...     end = start + 10
...     print(gc_content(dna_list[start:end]))
...
40.0
30.0
70.0
50.0
0.0
```

Loops are really powerful. They provide a means to iterate over lots of items
and as such to automate repetitive tasks.

## Summary

I hope this post has given you a flavour of what it feels like to work with
Python.

The key take home messages were:

- You can explore Python's syntax using its interactive mode
- Variables and functions help us avoid having to repeat ourselves
- When naming variables and functions explicit trumps succinct
- Loops are really powerful, they form the basis of automating repetitive tasks

If you enjoyed this post please check out the book that I am working on
[The Biologist's Guide to Computing](http://biologistsguide2computing.com/)!
