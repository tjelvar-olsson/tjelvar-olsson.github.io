---
layout: post
title: Documenting your coding projects
comments: true
tags:
  - scientific computing
  - programming
  - software development
---

*Do you want other people to make use of the project that you are working on?*

If you answered **yes** to the question above you need to write some form of
documentation outlining how to make use of it.

*Do you enjoy writing documentation?*

If you answered **no** to the question above please read on to find some tips
to make the experience more enjoyable.

## Start early and start small

A common scenario is to treat documentation as an afterthought. However, if a
project is nearing completion and one has no documentation, the thought of
writing it can be daunting. As a result one never starts working on the BIG
documentation task, but rather spends time on smaller and more satisfying tasks
such as adding nice-to-have features.

The solution is to start early and to start small. Before you write any code
create a ``README`` file and include a sentence stating what problem the project
solves.

Once you have some code that can be installed add some basic installation
instructions to the ``README`` file.


## Include documentation in your definition of done

Suppose that you have implemented a new feature. You are proud of it. You have
even written tests for it! Don't stop there! Complete the task by writing some
descriptive documentation outlining how to make use of the feature.
Furthermore, if you have release notes add a bullet point with a link to the
section that you have just written.

## Reap the benefits of explaining your code to someone else

Writing documentation is an act of trying to explain something to someone else.
What often happens when one tries to explain a solution to someone else is that
one finds that the solution is lacking or sub optimal.  I often find that the
act of documenting a feature results in me realising that the feature is not
actually fit for purpose in its current state - giving me the opportunity to fix
it before it is released.

Discovering improvements by writing documentation is similar to
[rubber duck debugging](https://en.wikipedia.org/wiki/Rubber_duck_debugging),
where one tries to discover the source of a bug by explaining code line by line
to a rubber duck.

## Store your documentation alongside your code in version control as plain text files

Documentation should be stored alongside your code in version control as plain
text files.

Storing your code and documentation in the same repository allows them to be kept
in line with each other.

The benefits of plain text files are outlined in
[The Pragmatic Programmer](https://pragprog.com/book/tpp/the-pragmatic-programmer).
In fact they have an entire chapter devoted to them.

*What is so special about plain text files?*

In short: they are portable, easy to use and there is no lock-in. For a more
extensive answer have a look at CM Smith's Lifehack post
[Why Geeks Love Plain Text (And Why You Should Too)](http://www.lifehack.org/articles/technology/why-geeks-love-plain-text-and-why-you-should-too.html).

## Make use of tools that can convert your plain text files to beautifully formatted documents

Although text files have many advantages they are not ideal for consuming
(reading) documentation. When reading documentation you want it to be pleasant
on the eye and easy to navigate.

I highly recommend using
[Sphinx](http://sphinx-doc.org)
it is a great tool for writing technical documentation. It can produce a range
of output formats including HTML and PDF. It has great support for
cross-referencing and the HTML output has built-in support for searching.
Furthermore, if you use Sphinx markup you can host your documentation on
[Read the Docs](https://readthedocs.org). I will explain how to use Sphinx
in my next post.

## Conclusion

Documentation is a sign that someone cared about a project. This makes it
easier for other people to care about it too.

I hope that you found this post useful. If nothing else I hope that it has
given you the motivation to add a ``README`` file to your current project with
a line explaining what problem the project solves.
