---
layout: post
title: Five exercises to help you get familiar with the Python debugger
comments: true
tags:
  - python
  - programming
---

## Introduction

When one starts programming it is common to find oneself inserting ``print``
statements all over the code when one finds that things are not working as
expected. This is fine and can often be a quick way of finding out what is
going on. However, it can become tedious whenever the problem in question is
non-trivial and one finds oneself repeatedly inserting yet another print
statement before running the script yet again only to find that one still does
not know what the problem is.

There is a more powerful way of finding out what a program is doing: using a
debugger. However, people often shy away from debuggers because of their arcane
interface. This post contains five exercises to help you master the Python
debugger.

## Exercise 1: stepping through a program

Let us start by stepping through a simple program. Copy and past the code
snippet below into a file named ``pdb_exercie_1.py``.

```python
name = 'alice'
greeting = 'hello ' + name
print(greeting)
```

Now invoke the script using the python debugger using the command below.

```
python -m pdb pdb_exercie_1.py
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
> pdb_exercise_1.py(2)<module>()
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

Up until this point we have not actually had any errors in our scripts to
correct. Let us change that. Copy and past the code below into a file named
``pdb_exercise_2.py``.

```python
import sys

def magic(x, y):
    return x + y * 2

x = sys.argv[1]
y = sys.argv[1]

answer = magic(x, y)
print('The answer is: {}'.format(answer))
```

Suppose that we run this script with the inputs 1 and 50 expecting the result 101.

```
python pdb_exercise_3.py 1 50
The answer is: 111
```

What is going on?

Now rather than insert ``print`` statements all over the code to work out what is going on let us examine the code in the debugger.

```
python -m pdb pdb_exercise_3.py 1 50
```

Let us get to the point where we have access to the variables ``x`` and ``y``.

```
> pdb_exercise_3.py(1)<module>()
-> import sys
(Pdb) n
> pdb_exercise_3.py(3)<module>()
-> def magic(x, y):
(Pdb) n
> pdb_exercise_3.py(6)<module>()
-> x = sys.argv[1]
(Pdb) n
> pdb_exercise_3.py(7)<module>()
-> y = sys.argv[1]
(Pdb) n
> pdb_exercise_3.py(9)<module>()
-> answer = magic(x, y)
(Pdb) 
```

First of all let us see what attributes are available in the scope of the program. We can do this using ``p`` for print.

```
(Pdb) p dir()
['__builtins__', '__file__', '__name__', '__package__', 'magic', 'sys', 'x', 'y']
```

There is also ``pp`` for pretty print.

```
(Pdb) pp dir()
['__builtins__',
 '__file__',
 '__name__',
 '__package__',
 'magic',
 'sys',
 'x',
 'y']
```

Okay, that is fine. So what is x?

```
(Pdb) p x
'1'
```

Hey, that looks suspiciously like a string. Note that we can use raw Python within the debugger. Let us find out type x is.

```
(Pdb) type(x)
<type 'str'>
```

The fact that we can execute Python within the debugger means that we can
change the input variables dynamically.

```
(Pdb) x = int(x)
(Pdb) y = int(y)
```

Let us just check the values before we run the program.

```
(Pdb) p x, y
(1, 1)
```

What ``y`` is 1 not 50?

Inspecting the code we find that I forgot to update the index when I copied the
input parsing line (note line 7 in the code listing below).

```
(Pdb) l
  4         return x + y * 2
  5     
  6     x = sys.argv[1]
  7     y = sys.argv[1]
  8     
  9  -> answer = magic(x, y)
 10     print('The answer is: {}'.format(answer))
[EOF]
(Pdb) 
```
Ok, let us just change the value of ``y`` to 50 in the debugger before checking
if the code works as expected by letting it run to completion.

```
(Pdb) y = 50
(Pdb) c
The answer is: 101
The program finished and will be restarted
> pdb_exercise_3.py(1)<module>()
-> import sys
(Pdb) 
```

Ok, so the example is a little bit naff. However, I hope it illustrates the
power of working with the debugger, particularly if you are working on a more
complicated code base.

## Exercise 4: using breakpoints

So far we have been stepping though a program from beginning to end. However,
when working on larger programs this is often not practical. To simulate such a
situation copy and paste the code below into a file named
``pdb_exercise_4.py``.

```python
import time

def slow_subtractor(a, b):
    """Return a minus b."""
    time.sleep(5)
    return a - b

some = slow_subtractor(12, 8)
crazy = slow_subtractor(12, 78)
scientific = slow_subtractor(56, 31)
experiment = slow_subtractor(101, 64)

total = some + crazy + scientific + experiment

experimental_fraction = experiment / total
```

When we run this code we get a ``ZeroDivisionError``.

```
tjelvar@crunchbang:~/projects/tjelvar-olsson.github.io/_drafts$ python pdb_exercise_4.py
Traceback (most recent call last):
  File "pdb_exercise_4.py", line 15, in <module>
    experimental_fraction = experiment / total
ZeroDivisionError: integer division or modulo by zero
```

Stepping through the code in the debugger would be annoying as you would have
to press ``n`` every time the ``slow_subtraction()`` function was called. Let
us instead insert a breakpoint before the line that generates the error. This
is achieved by importing the ``pdb`` module and using the ``pdb.set_trace()``
function.

```python
import time
import pdb

def slow_subtractor(a, b):
    """Return a minus b."""
    time.sleep(5)
    return a - b

some = slow_subtractor(12, 8)
crazy = slow_subtractor(12, 78)
scientific = slow_subtractor(56, 31)
experiment = slow_subtractor(101, 64)

total = some + crazy + scientific + experiment

pdb.set_trace()
experimental_fraction = experiment / total
```

If we run the code now we get dumped into a debugger session before the
offending line is executed.

```
python pdb_exercise_4.py
> pdb_exercise_4.py(17)<module>()
-> experimental_fraction = experiment / total
(Pdb) p total
0
(Pdb) p some, crazy, scientific, experiment
(4, -66, 25, 37)
```

Ok, so it looks like there is something funny going on with the ``crazy``
variable. Perhaps the input arguments were given the wrong way around.

The take home message is that setting breakpoints is a powerful way of getting
to the point of interest in your code when you want to examine what is going
on.


## Exercise 5: Moving around the stack


## Summary

## Further reading

- [Debugging in Python | Python Conquers The
  Universe](https://pythonconquerstheuniverse.wordpress.com/2009/09/10/debugging-in-python/)
- [Debugging Python Like a Boss - The Zapier Engineering
  Blog](https://zapier.com/engineering/debugging-python-boss/)
