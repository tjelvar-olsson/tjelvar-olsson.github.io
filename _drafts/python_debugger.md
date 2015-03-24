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

Before we start stepping through this script let us have a look at a new
command. Start the debugger.

```
python -m pdb pdb_exercise_2.py
```

This time rather than stepping through the program press ``c``, which stands
for "continue execution". You should see the output below.

```
(Pdb) c
hello alice
The program finished and will be restarted
> pdb_exercise_2.py(1)<module>()
-> def greet(name):
(Pdb) 
```

This time use ``n`` to walk through the script. Note that you only need three
clicks to get to the end of the program and that the debugger does not step
into the ``greet()`` function. You should see the output below.

```
> pdb_exercise_2.py(1)<module>()
-> def greet(name):
(Pdb) n
> pdb_exercise_2.py(5)<module>()
-> greeting = greet('alice')
(Pdb) n
> pdb_exercise_2.py(6)<module>()
-> print(greeting)
(Pdb) n
hello alice
```

Press ``c`` to  restart the program, then press ``n`` once to get to the line
where the greet function is about to be called.

```
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(1)<module>()
-> def greet(name):
(Pdb) n
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(5)<module>()
```

This time we will use ``s`` to "step" into the ``greet()`` function, then
continue walking through the program using ``n``. Note the difference now that
you have stepped into the ``greet()`` function.

```
(Pdb) s
--Call--
> pdb_exercise_2.py(1)greet()
-> def greet(name):
(Pdb) n
> pdb_exercise_2.py(2)greet()
-> greeting = 'hello ' + name
(Pdb) n
> pdb_exercise_2.py(3)greet()
-> return greeting
(Pdb) n
--Return--
> pdb_exercise_2.py(3)greet()->'hello alice'
-> return greeting
(Pdb) n
> pdb_exercise_2.py(6)<module>()
-> print(greeting)
(Pdb) n
hello alice
--Return--
> pdb_exercise_2.py(6)<module>()->None
-> print(greeting)
(Pdb) 
```

Finally let us have a look at the ``r`` command, which stands for "return".
This is similar to the ``c`` command, but rather than continuing to the end of
the program ``r`` runs to the end of the function.

Let us try it out, start off by entering ``c`` to restart the program then
enter ``n`` and ``s``. You should now be in the ``greet()`` function.

```
(Pdb) c
The program finished and will be restarted
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(1)<module>()
-> def greet(name):
(Pdb) n
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(5)<module>()
-> greeting = greet('alice')
(Pdb) s
--Call--
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(1)greet()
-> def greet(name):
(Pdb)
```

As a sanity check enter ``l`` to list where you are in the code. You should see the below.

```
(Pdb) l
  1  -> def greet(name):
  2         greeting = 'hello ' + name
  3         return greeting
  4     
  5     greeting = greet('alice')
  6     print(greeting)
[EOF]
(Pdb) 
```

Now press ``r`` as in "return".

```
(Pdb) r
--Return--
> /home/tjelvar/projects/tjelvar-olsson.github.io/_drafts/pdb_exercise_2.py(3)greet()->'hello alice'
-> return greeting
(Pdb) 
```

Finally, as a recap let us have a look at the "help" descriptions of the
commands that we have been using so far.

```
(Pdb) help n
n(ext)
Continue execution until the next line in the current function
is reached or it returns.
(Pdb) help s
s(tep)
Execute the current line, stop at the first possible occasion
(either in a function that is called or in the current function).
(Pdb) help c
c(ont(inue))
Continue execution, only stop when a breakpoint is encountered.
(Pdb) help r
r(eturn)
Continue execution until the current function returns.
(Pdb) help l
l(ist) [first [,last]]
List source code for the current file.
Without arguments, list 11 lines around the current line
or continue the previous listing.
With one argument, list 11 lines starting at that line.
With two arguments, list the given range;
if the second argument is less than the first, it is a count.
(Pdb) help help
h(elp)
Without argument, print the list of available commands.
With a command name as argument, print help about that command
"help pdb" pipes the full documentation file to the $PAGER
"help exec" gives help on the ! command
(Pdb) 
```

## Exercise 3: interacting with the program under inspection

- use a script that generates an error
- p
- pp
- changing the value of a variable


## Exercise 4: using breakpoints

