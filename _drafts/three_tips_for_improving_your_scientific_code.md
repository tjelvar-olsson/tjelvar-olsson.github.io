---
layout: post
title: Three essential tips for improving your scientific code
comments: true
tags:
  - scientific computing
---

Writing scientific code is not that different to writing any other type of code.
What is different is that many people that end up coding during their PhD do
not have or get any formal training in software development best practises.

In my case it was very much a case of trail and error and picking things up as
I went along. In the research group that I was in my peers were doing lab work
culturing cells and characterising proteins, so there was no one to discuss
programming with. So I learnt by reading; reading blogs; reading magazines;
reading books; reading other people's code. However, it was a slow process
sorting the wheat from the chaff. Furthermore many of the things that I read
about seemed a bit over the top if one was not working for a software
development house.

With hindsight I realise that another difficulty in learning software
development best practises is that sometimes the most fundamental aspects of
software development are not explicitly stated as they are taken for granted by
everyone that uses them.

Here are the three most valuable things that I have learnt both from my own
trail and error and by working with great software developers since finishing
my PhD. For anyone developing software professionally non of this will be new.
However, if you are a scientist that has drifted into programming these are the
three most important things that you can do to improve your productivity and
the quality of your code.

## Use version control



## Write code so that it can be understood by someone less clever than yourself

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

So go on name your variable ``temperature_increase`` instead of ``temp_inc`` or
``ti``. You have to type a few more letters but you will gain so much more. By
the way does ``temp`` stand for ``temporary`` or ``temperature`` and does
``inc`` stand for ``increment`` or ``increase``? Also, if you find that typing
out long explicit names for variables, functions and classes is causing you
frustration then you should go on the hunt for a better text editor (I use vim)
or an integrated development environment.

In terms of commenting your code the key is to realise that you should document
the intent not the actual code. In other words, I can read your code so I don't
need it re-iterated using plain English. However, I cannot read your mind so
please tell me what the intention was. Let us illustrate this with an example.

Think of an example...

Describing the architecture of the system is just a fancy way of saying that
you should describe the how the components of your software interact with each
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
parser.py     - module for parsing parameter files
database.py   - module for storing results
simulation.py - module for running simulations
experiment.py - template for creating a new experiment

The ``experiment.py`` template uses the parser to read in the parameters for
the experiment. The parameters are then passed on to the simulation
(``simulation.py``).  Note that when you instantiate the
``simulation.Simulation`` class you need to provide it with a
``database.Database`` instance. The latter will be used to write the simulation
results to your database of choice.
```

Something about coding style...


## Write tests
