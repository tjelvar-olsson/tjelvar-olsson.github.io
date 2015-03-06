---
layout: post
title: Three essential tips for improving your scientific code
comments: true
tags:
  - scientific computing
  - software development
---

Writing scientific code is not dissimilar to writing any other type of code.
What is different is that many people who end up coding during their PhD do
not have or get any formal training in software development best practices.

For me it was very much a case of trial and error and picking things up as I
went along. In the research group that I was in, my peers were doing lab work
culturing cells and characterising proteins, so there was no one to discuss
programming with. So I learnt by reading; reading blogs; reading magazines;
reading books; reading other people's code. However, it was a slow process
sorting the wheat from the chaff. Furthermore many of the things that I read
seemed a bit over the top for a one man band.

With hindsight I realise that another difficulty in learning software
development best practices is that sometimes the most fundamental aspects of
software development are not explicitly stated as they are taken for granted by
everyone that uses them.

Here are the three most valuable things that I have learnt both from my own
trial and error and by working with great software developers since finishing
my PhD. For anyone developing software professionally, none of this will be new.
However, if you are a scientist who has drifted into programming, these are the
three most important things that you can do to improve your productivity and
the quality of your code.


## Use version control

Using version control is one of the simplest ways of increasing your
productivity. The reason is it reduces your fear of changing existing
code as you can always roll back to a previously working state. One of the tell-tale
signs that you need to use version control is if your project directory
contains files named along the lines of the below (as you can tell I used to do
this before I saw the light).

```
new_simulation.py
older_simulation.py
old_simulation.py
simulation.py
simumlation_100606.py
test_simulation.py
```
When I started using version control
[Subversion](https://subversion.apache.org/) was the best open source tool
available. However, it was difficult to set up and I was never sure I got it
right. These days you have a choice of two largely equivalent systems
[Git](http://git-scm.com/) or [Mercurial](http://mercurial.selenic.com/). These
are very easy to set up and use.

Here I will illustrate how to use Git from the command line. To start a new project.

```
git init simulation
cd simulation
```

This creates a directory named ``simulation``, the files in this directory can
now be managed by git. Suppose that we create a ``README`` file in this
directory and want to add it to version control.

```
git add README
```

When a file is added it is staged to be committed. Let's commit it as a
snapshot to version control.

```
git commit -m "Added README file."
```

That's it. You can keep using ``git add`` and ``git commit`` to add incremental
changes to your code base until you find that you need to use some more
powerful features of Git, at which point you can learn more about it.

If you already have a project that you want to start tracking using Git you can
use the commands below.

```
cd my_existing_project
git init
git add "*"
git commit -m "Initial file import."
```

Note that the command above will put all files in the project directory under
version control so do not use it if your project directory contains machine
generated files, for example output files from your program.

Once you have got a little bit of familiarity with Git or Mercurial I would
strongly recommend that you set up an account with
[BitBucket](https://bitbucket.org/) or [GitHub](https://github.com/) and host
your code there. This has several advantages: you can stop worrying about your
computer crashing and losing all your work, you can access your code from any
machine with an internet connection, and you can collaborate with other people on
your code.


## Write code so that it can be understood by someone else

One thing that is different when developing code in an academic environment is
that it is not unusual to start a project from scratch. This is quite uncommon
when working for a company that develops software. In the latter case one of
the first tasks on the job is to get familiar with the company's code base.
This means reading code, which is when one learns to appreciate:

- Explicit names of variables, functions and classes
- Comments that explain the intent of code whenever it is not immediately intuitive
- Documentation describing the overall architecture of the system 
- Consistent coding style

However, when one starts coding by oneself on a new project none of the above
matters as the logic behind every decision and poorly named variable is
immediately obvious to oneself. Anyway, one tells oneself, "I will deal with
those niceties once the program is working".

However, once the program is working those things which were immediately
obvious are now obscure and anyway the program is working so one can use it to
generate some results, which is much more interesting than cleaning up code.
Then one realises that the results are not quite as expected and something is
not quite right about the logic of the program. However, the logic of the
program is not immediately clear...

So go on, name your variable ``temperature_increase`` instead of ``temp_inc`` or
``ti``. You have to type a few more letters but you will gain so much more. By
the way, does ``temp`` stand for ``temporary`` or ``temperature`` and does
``inc`` stand for ``increment`` or ``increase``? Also, if you find that typing
out long explicit names for variables, functions and classes is causing you
frustration then you should go on the hunt for a better text editor (I use vim)
or an integrated development environment, that understands code and offers to
complete names for you.

In terms of commenting your code, the key is to realise that you should document
the intent not the actual code. In other words, I can read your code so I don't
need it re-iterated using plain English. However, I cannot read your mind so
please tell me what the intention was.

Describing the architecture of the system is just a fancy way of saying that
you should describe how the components of your software interact with each
other. Suppose for example that you were faced with a relatively simple code
base that contained the files:

- ``parser.py``
- ``database.py``
- ``simulation.py``
- ``experiment.py``

Can you describe how these Python modules interact with and depend on each
other? What is the difference between a simulation and an experiment?

Now suppose that the author of this hypothetical Python package  had been kind
and spent five minutes including the lines below in the README file, would it
enable you to answer the questions above?

```
README
======

parser.py     - module for parsing parameter files
database.py   - module for storing results
simulation.py - module for running simulations
experiment.py - template for creating a new experiment

The ``experiment.py`` template uses the parser to read
in the parameters for the experiment. The parameters
are then passed on to the simulation (``simulation.py``).
Note that when you instantiate the ``simulation.Simulation``
class you need to provide it with a ``database.Database``
instance. The latter will be used to write the simulation
results to your database of choice.
```

I won't dwell too long on coding style. Basically be consistent and try to
use the standard one for your language; i.e. if you code in Python use
[PEP8](https://www.python.org/dev/peps/pep-0008/), if you write C code use
[K&R](http://en.wikipedia.org/wiki/The_C_Programming_Language) style, and so
forth.  If coding style interests you, please read [Style is
Substance](http://www.artima.com/weblogs/viewpost.jsp?thread=74230) by Ken
Arnold.


## Write tests

This is the most difficult of the tips outlined in this post. Writing good
tests is hard, and continuing to write tests as your code base grows requires
discipline. Furthermore, many scientific algorithms have a stochastic nature to
them, which further compounds the situation.

First of all, before you start writing tests, make sure that you find a suitable
testing framework so that you do not re-invente the wheel. For example if
you are coding in Python you could use
[Unittest](https://docs.python.org/2/library/unittest.html).

If you already have code that is working but have no tests, start by adding
some integration tests. In other words treat your software as a black box that,
given a set of inputs, produces a known set of outputs. Write an automated test that
checks that this is true.

Now once you go in and work on a particular unit of your code, make sure that
you write a test for that particular unit first, then make the change that you
wanted to make.

When the unit that you are testing is so isolated that it does not depend on
any other code or systems (e.g. a database running in the background) then the
test is referred to as a unit test.

There are two advantages to unit tests over integration tests. They make it
easier to identify which part of your code is broken when they fail. Secondly they
run quicker than integration tests so you can have more of them.

Why does the speed of the tests matter? Speed matters because once you have
automated tests in place you need to run them often, at a minimum before every
commit to version control.

At this point I recommend that you get a copy of Martin Fowlers' book
[Refactoring: Improving the Design of Existing
Code](http://martinfowler.com/books/refactoring.html). As the title suggests it
is about refactoring rather than testing. However, refactoring requires tests
and the book gives loads of practical advice on how to improve existing code by
writing tests and refactoring.

If you are starting out with a clean slate (i.e. no existing code), I highly
recommend that you start writing tests from the start. You could even go to the
extreme and use [Test Driven
Development](http://en.wikipedia.org/wiki/Test-driven_development), where you
write a test before you write any code. Initially the test will fail and then
you implement the code to make the test pass. 

Test driven development is a bit more complicated than what I outlined above,
notably it includes a step of refactoring. However I will not go into more
detail here.  If test driven development sounds interesting and you are
interested in web development as well I highly recommend Harry Percival's book
[Test-Driven Development with
Python](http://chimera.labs.oreilly.com/books/1234000000754).

This all sounds like a lot of hard work, why do I need tests anyway? I won't
dwell on this too much. However, if you don't have tests how can you have any
confidence that your code is doing what it is supposed to do? Okay, so you have
done manual testing and the results are as expected. Fine, now suppose that you
want to add another feature how can you be sure that you will not introduce a
bug somewhere else? Do you want to do all that manual testing again? If you do
not have tests you will get to the stage where you are afraid to touch the code
for fear of breaking it.

## Summary

This post turned out a bit longer than I initially thought. However the take
home message is simple:

- Use version control
- Write code so that it can be understood by someone else
- Write tests

Using version control is easy: do it!

Another person that is likely to need to get familiar with your code is *you in
six months time* so be kind and make your code easy to understand.

Writing good tests is initially hard, and the only way to learn is by practise (I'm still
learning). However, do write them otherwise your code will hold you to ransom.

If you already do all of the above, great, I'm preaching to the converted,
please forward this post to someone less experienced than yourself.

## Acknowledgements 

I'd like to thank Clare Macrae
([@ClareMacraeUK](https://twitter.com/ClareMacraeUK)) for helpful discussions
and feedback.
