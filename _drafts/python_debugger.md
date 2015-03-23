---
layout: post
title: Seven exercises to get familiar with the Python debugger
comments: true
tags:
  - python
  - programming
---

## Introduction

When one starts programming it is common to find oneself inserting ``print``
statements all over the code when one finds that things are not working as
expected. This is fine and can often be a quick way of finding out what is
going on.

There is a more powerful way of finding out what a program is doing: using a
debugger. However, people often shy away from debuggers because of their arcane
interface. This post contains seven exercises to help you master the Python
debugger.

## Exercise 1: stepping through a program

Let us start by stepping through a simple program. Copy and past the code
snippet below into a file named ``pdb_exercies_1.py``.

```python
name = 'alice'
greeting = 'hello ' + name
print(greeting)
```

Now invoke the script using the python debugger using the command below.

```
python -m pdb pdb_exercies_1.py
```

In the above ``pdb`` is three letter acronym for Python DeBugger. You should be greeted by the prompt below.

```
> pdb_exercise_1.py(1)<module>()
-> name = 'alice'
(Pdb) 
```

The debugger shows the next line to be executed (``-> name = 'alice'``) as well
the prompt for interacting with the debugger (``(Pdb)``).

Type in ``n`` (as in next) to execute the line displayed. You should now see the lines below.

```
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_1.py(2)<module>()
-> greeting = 'hello' + name
```

Let us check the value of the newly assigned ``name`` variable. Type in ``p
name`` (p as in print). It should tell you that the name is "alice".

Type in ''n'' again to execute the next command. The ``greeting`` variable
should now have been assigned the string "hello alice".

When debugging it is quite easy to lose the frame of reference as to where one
is in the code. To put things into context type in ``l`` as in list (the source
code for the current file). You should see output below.

```
(Pdb) l
  1     name = 'alice'
  2     greeting = 'hello ' + name
  3  -> print(greeting)
[EOF]
```

Okay, so we are almost at the end. Type in ``n`` again to execute the last command.

```
(Pdb) n
hello alice
```

Finally, type in ``q`` to quite the debugger.

Well done! You have just used the Python debugger to step through a program.


## Exercise 2: stepping into functions

Let us create a script with a function. Copy and past the code snippet below
into a file named ``pdb_exercise_2.py``.

```python
def greet(name):
    greeting = 'hello ' + name
    return greeting

greeting = greet('alice')
print(greeting)
```
