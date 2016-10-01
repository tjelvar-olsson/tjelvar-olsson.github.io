---
layout: post
title: "Biologist's Guide to Python string manipulation"
comments: true
tags:
  - biology
  - bioinformatics
  - computational biology
  - systems biology
---

Because information about DNA and proteins are often stored in plain text files
many aspects of biological data processing involves manipulating text. In
computing text is often referred to as strings of characters. String
manipulation is is therefore a common task both for processing biological
sequences and for interpreting sequence identifiers.

This post provides a quick summary of how Python can be used for such string
manipulation, using the [FASTA description
line](https://en.wikipedia.org/wiki/FASTA_format#Description_line) as an
example.

The Python string object
------------------------

When reading in strings from a text file one often has to deal with
lines that have leading and/or trailing white spaces. Commonly one wants
to get rid of them. This can be achieved using the `strip()` method
built into the Python string object.

```python
>>> "  text with leading/trailing spaces ".strip()
'text with leading/trailing spaces'
```

Another common use case is to replace a word in a line. For example,
when we strip out the leading and trailing white spaces one might want
to update the word "with" to "without" to make the resulting string
reflect its current state. This can be achieved using the `replace()`
method.

```python
>>> "  text with leading/trailing spaces ".strip().replace("with", "without")
'text without leading/trailing spaces'
```

In the example above we chain the `strip()` and `replace()` methods together.
In practise this means that the `replace()` method acts on the return value of
the `strip()` method.

Python's string object also comes with a `startswith()` method. This can,
for example, be used to identify FASTA description lines.

```python
>>> ">MySeq1|description line".startswith(">")
True
```

The `endswith()` method complements the `startswith()` method and is
often used to examine file extensions.

```python
>>> "/home/olsson/images/profile.png".endswith("png")
True
```

The example above only works if the file extension is in lower case.

```python
>>> "/home/olsson/images/profile.PNG".endswith("png")
False
```

However, we can overcome this issue by adding a call to the `lower()`
method, which converts the string to lower case.

```python
>>> "/home/olsson/images/profile.PNG".lower().endswith("png")
True
```

Another common use case is to want to search for a particular string
within another string. For example one might want to find out if the
UniProt identifier "Q6GZX4" is present in a FASTA description line. To
achieve this one can use the `find()` method, which returns the index
position (zero-based) where the search term was first identified.

```python
>>> ">sp|Q6GZX4|001R_FRG3G".find("Q6GZX4")
4
```

If the search term is not identified `find()` returns -1.

```python
>>> ">sp|P31946|1433B_HUMAN".find("Q6GZX4")
-1
```

When iterating over lines in a file one often wants to split the line
based on a delimiter. This can be achieved using the `split()` method.
By default this splits on white space characters and returns a list of
strings.

```python
>>> "text without leading/trailing spaces".split()
['text', 'without', 'leading/trailing', 'spaces']
```

A different delimiter can be used by providing it as an argument to the
`split()` method.

```python
>>> ">sp|Q6GZX4|001R_FRG3G".split("|")
['>sp', 'Q6GZX4', '001R_FRG3G']
```

There are many variations on the string operators described above. It is
useful to familiarise yourself with the [Python documentation on
strings](https://docs.python.org/2/library/string.html).


Regular expressions
-------------------

Regular expressions can be defined as a series of characters that define
a search pattern.

Regular expressions can be very powerful. However, they can be difficult
to build up. Often it is a process of trial and error. This means that
once they have been created, and the trial and error process has been
forgotten, it can be extremely difficult to understand what the regular
expression does and why it is constructed the way it is.

*Warning: only use regular expression as a last resort!*

A good rule of thumb is to always try to use regular string operations to
implement the desired functionality and only switch to regular expressions when
the code implemented using regular string operations becomes more difficult to
understand than the equivalent regular expression.

To use regular expressions in Python we need to import the `re` module.
The `re` module is part of Python's standard library. Importing modules in
Python is achieved using the `import` keyword.

```python
>>> import re
```

Let us store a FASTA description line in a variable.

```python
>>> fasta_desc = ">sp|Q6GZX4|001R_FRG3G"
```

Now, let us search for the UniProt identifier `Q6GZX4` within the line.

```python
>>> re.search(r"Q6GZX4", fasta_desc)  # doctest: +ELLIPSIS
<_sre.SRE_Match object at 0x...>
```

There are two things to note here:

1.  We use a raw string to represent our regular expression, i.e. the
    string prefixed with an `r`
2.  The regular expression `search()` method returns a match object (or
    None if no match is found)

*What is a "raw string"?* In Python "raw" strings differ from regular strings
in that the bashslash `\` character is interpreted literally. For example the
regular string equivalent of `r"\n"` would be `"\\n"` where the first backslash
is used to escape the effect of the second (remember that `\n` represents a
newline). Raw strings were introduced in Python to make it easier to create
regular expressions that rely heavily on the use of literal backslashes.

The index of the first matched character can be accessed using the match
object's `start()` method. The match object also has an `end()` method
that returns the index of the last character + 1.

```python
>>> match = re.search(r"Q6GZX4", fasta_desc)
>>> if match:
...     print(fasta_desc[match.start():match.end()])
...
Q6GZX4
```

In the above we make use of the fact that Python strings support
slicing. Slicing is a means to access a subsection of a sequence. The
`[start:end]` syntax is inclusive for the start index and exclusive for
the end index.

```python
>>> "012345"[2:4]
'23'
```

To see the merit of regular expressions we need to create one that
matches more than one thing. For example a regular expression that could
match all the patterns `id0`, `id1`, ..., `id9`.

Now suppose that we had a list containing FASTA description lines with
these types of identifiers. Note that the list below also contains a sequence
line that we never want to match.

```python
>>> fasta_desc_list = [">id0 match this",
...                    ">id9 and this",
...                    ">id100 but not this (initially)",
...                    "AATCG"]
...
```

Let us loop over the items in this list and print out the lines that
match our identifier regular expression.

```python
>>> for line in fasta_desc_list:
...     if re.search(r">id[0-9]\s", line):
...         print(line)
...
>id0 match this
>id9 and this
```

There are several things to note in the above. First of all we are using
the concept of a `for` loop to iterate over all the items in the
`fasta_desc_list`. Secondly, there are two noteworthy aspects of the
regular expression. The `[0-9]` syntax means match any digit. The `\s`
regular expression meta character means match any white space character.

If one wanted to create a regular expression to match an identifier with
an arbitrary number of digits one can make use of the `*` meta
character, which causes the regular expression to match the preceding
expression 0 or more times.

```python
>>> for line in fasta_desc_list:
...     if re.search(r">id[0-9]*\s", line):
...         print(line)
...
>id0 match this
>id9 and this
>id100 but not this (initially)
```

It is possible to extract specific pieces of information from a line
using regular expressions. This uses a concept known as "groups", which
are indicated using parenthesis. Let us try to extract the UniProt
identifier from a FASTA description line.

```python
>>> print(fasta_desc)
>sp|Q6GZX4|001R_FRG3G
>>> match = re.search(r">sp\|([A-Z,0-9]*)\|", fasta_desc)
```

*Note how horrible and incomprehensible the regular expression is!*

It took me a couple of attempts to get this regular expression right as
I forgot that `|` is a regular expression meta character that needs to
be escaped using a backslash `\`.

The regular expression representing the UniProt idendifier `[A-Z,0-9]*` means
match capital letter (``A-Z``) and digits (``0-9``) zero or more times (``*``).
The UniProt regular expression is enclosed in parenthesis. The parenthesis
denote that the UniProt identifier is a group that we would like access to. In
other words, the purpose of a group is to give the user access to a section of
interest within the regular expression.

```python
>>> match.groups()
('Q6GZX4',)
>>> match.group(0)  # Everything matched by the regular expression.
'>sp|Q6GZX4|'
>>> match.group(1)
'Q6GZX4'
```

Note that there is a difference between the `groups()` and the `group()`
methods. The former returns a tuple containing all the groups defined in the
regular expression. The latter takes an integer as input and returns a specific
group. However, confusingly `group(0)` returns everything matched by the
regular expression and `group(1)` returns the first group making the `group()`
method appear as if it used a one-based indexing scheme.

Finally, let us have a look at a common pitfall when using regular
expressions in Python: the difference between the methods `search()` and
`match()`.

```python
>>> print(re.search(r"cat", "my cat has a hat"))  # doctest: +ELLIPSIS
<_sre.SRE_Match object at 0x...>
>>> print(re.match(r"cat", "my cat has a hat"))  # doctest: +ELLIPSIS
None
```

Basically `match()` only looks for a match at the beginning of the
string to be searched. For more information see the [search() vs
match()](https://docs.python.org/2/library/re.html#search-vs-match)
section in the Python documentation.

There is a lot more to regular expressions in particular all the meta
characters. For more information have a look at the [regular expressions
operations](https://docs.python.org/2/library/re.html) section in the
Python documentation.

This blog post was adapted from a section in the book that I am working on:
[The Biologist's Guide to Computing](http://biologistsguide2computing.com/).
Please check it out if you found this post useful!
