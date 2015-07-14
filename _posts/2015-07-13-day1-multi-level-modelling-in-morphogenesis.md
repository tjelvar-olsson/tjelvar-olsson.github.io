---
layout: post
title: "Day 1: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

Today was the first day of a two week course on
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/).

During the introductory lecture, given by 
[Dr Veronica Grieneisen](https://www.jic.ac.uk/directory/veronica-grieneisen/),
the goals of the course were outlined.

A goal of the course is to spread a broader understanding of
developmental biology and biological modelling. More specifically to:

- Define and understand generic principles guiding developmental biology
- Learn how to identify and unravel processes
- Understand and discuss at what level one should "model" a phenomena
- Get exposed to different biological paradigm systems, as well as modelling formalisms
- How to obtain models **with predictive value** and **explanatory power** that **create isomorphisms**

In this context isomorphisms are corresponding abstractions and conceptual
models that can be applied to different phenomenon.

At another level a goal of the course is to discuss practical aspects of
modelling. As scientists we need techniques to be able to express ourselves. As
such the course aims to open the black boxes that biologists often make use of.

At yet another level the course is about communication. In particular enabling
communication with a common language. The participants of the course are
intentionally a mix of experimental and computational biologists from many
different fields of biology. Bridging the gap between experimental and
computational biologists is a central theme of the course. As is learning how
to express oneself to people from different fields. 

The section describing the goals was followed by a brief introduction
to partial differential equations starting from the conservation equation (the
differentiation form of the
[continuity equation](https://en.wikipedia.org/wiki/Continuity_equation)),
leading into a discussion about
[flux](https://en.wikipedia.org/wiki/Flux), Fick's first law and
[it's relation to diffusion](https://en.wikipedia.org/wiki/Fick%27s_laws_of_diffusion).

The overview of partial differential equations was followed by a discussion on
[cellular automata](https://en.wikipedia.org/wiki/Cellular_automaton),
"to have an object or not to have an object". Three examples were described:

1. [Majority voting rule](http://demonstrations.wolfram.com/CellularAutomataWithMajorityRule/)
2. [Conway's game of life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)
3. [Margolous diffusion/alternation](http://cell-auto.com/neighbourhood/margolus/)

The participants were then immersed in a hands on workshop exploring majority
voting and Conway's game of life. Followed by an exploration of diffusion simulated by
partial differential equations and Margolous alternation. The latter was accomplished
by an exercise exploring
[diffusion-limited aggregation](https://en.wikipedia.org/wiki/Diffusion-limited_aggregation).
The purpose of these exercises was to make biologist more familiar with
thinking algorithmically and for everyone to think critically about the impact
of the choice of modelling technique.  What can be seen as a feature in one
instance can be an artifact in another. It all depends on the phenomena that
one is trying to model.

Then it was time for lunch and socialising.

After lunch
[Dr Stan Maree](https://www.jic.ac.uk/directory/stan-maree/)
introduced three seemingly different phenomenon: cellular slime mold chemotaxis, 
Belousov-Zhabotinsky reaction (chemistry) and action potentials in neurophysiology.

The
[Hodgkin-Huxley model](https://en.wikipedia.org/wiki/Hodgkin–Huxley_model)
was described in all its complexity. Followed by a statement that it can be
described as "unpleasantly complex" and a quote from FitzHugh that
"the usefulness of an equation to an experimental physiologist (...) depends
on his understanding of how it works". The
[FitzHugh-Nagumo model](https://en.wikipedia.org/wiki/FitzHugh–Nagumo_model)
was then briefly introduced. However, the details of it and the implications
of the model were not described as it was to be explored during the
afternoons practical session.

Instead the focus shifted to how one can gain an understanding of systems of
linear ordinary differential equations. Time plots were contrasted with
[phase plane plots](https://en.wikipedia.org/wiki/Phase_plane). And the importance
of visualising
[nullclines](https://en.wikipedia.org/wiki/Nullcline) as lines of zero change
for a particular parameter was highlighted. In particular the fact that
one can identify all equilibria from the intersections of nullclines in
a phase plane plot.

Stability of equilibria was then discussed and simple rules for quickly analysing
the stability of equilibria were derived from the fact that:

1. For an equilibria to be stable its eigenvalues need to be negative
2. Summing two eigenvalues results in the trace
3. Multiplying two eigenvalues results in the determinant 

So by plotting the trace vs the determinant we can get a plot illustrating
different types of equilibria,
[see also](https://en.wikipedia.org/wiki/Phase_plane#Eigenvectors_and_nodes).

![trace-determinant plot](https://upload.wikimedia.org/wikipedia/commons/3/35/Phase_plane_nodes.svg)

The
[Jacobian matrix](https://en.wikipedia.org/wiki/Jacobian_matrix_and_determinant)
was then introduced and the idea that the Jacobian can be approximated by
plotting the nullclines on the phase plane plot and making small
perturbations around the equilibria.

The participant where then invited to explore the temporal dynamics of the FitzHugh-Nagumo
model using the software
[grind](http://www-binf.bio.uu.nl/rdb/grind.html). This was followed by exercises looking
at the spatio-temporal dynamics of the same model using partial differential equations.
This led on to looking at spirals formed when introducing introducing a temporary barrier
and it was highlighted that these spirals could never have been identified if
one did not take the spatial regime into account.  Finally the link to the
other phenomenon outlined at the beginning of the afternoon session, slime mold
chemotaxis and
Belousov-Zhabotinsky reaction was pointed out and the isomorphic nature of these
phenomenon was highlighted.
