---
layout: post
title: "Day 9: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The ninth day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
course was started by
[Professor Enrico Coen](https://www.jic.ac.uk/directory/enrico-coen/)
who continued on the theme of polarity.

Professor Coen did not set out to study polarity. He was interested in how
tissues grew. However, after some time he came to the conclusion that to
understand tissue growth he would have to understand polarity.

One of the main players of polarity in plants is the hormone auxin.
In fact some of the main markers for polarity in plants are the PIN proteins,
which actively transports auxin across the plasma membrane. By coordinating
the location of PIN proteins to one side of all cells a tissue
can create a polarity field. Furthermore, auxin gradients have been shown
to regulate tissue polarity.

At the time there where two main models describing auxin regulated tissue cell
polarity:

- Cell-cell comparison, a.k.a. "up-the-gradient" hypothesis
- Flux or gradients at the interface, a.k.a. "with-the-flow" hypothesis

The former could explain PIN locations and the latter veins in leafs.
However, both models also had issues. How could a cell, in a cell-cell comparison
scenario, know the concentration of its neighbors? On the flip-side, in the
"with-the-flow" hypothesis, it was unclear how the cell would be able to
measure the flux.

Professor Coen then turned the attention to animals  models that
had been proposed to coordinate the hair orientation in Drosophila wings.
Again there were two major classes of models:

- Cell-cell comparison models
- Interface models, i.e. receptors intaracting at interfaces between cells

These were very similar to the plant models. The main difference being that
plant cells could not interact directly as they are separated by a cell wall.

However, all the plant and animal models had an assumption in common. Namely
that cells are unpolarised in the absence of an asymmetric ligand distribution
or polarised neighbours.

But we know of at least some systems where this assumption is not true.
For example budding yeast and migrating neutrophils.

Dr Coen then showed that by assuming that cells have an intrinsic polarity one
can create a model where the polarised cells arranges themselves along
a concentration gradient by working as a system to reduce the gradient in question.
This is a bit counterintuitive.
The patterning is working to remove the signal that is causing the pattern.

One way to use this principle to model how a plant organises polarity is to
assume high auxin efflux at one end of the tissue and no export of auxin at the
other end.  Using this model one gets a similar result to the cell-cell
comparison models.

However, one can use the same principle to create a different model.
A model with high auxin production at one end of the tissue and low
auxin degradation at the other end. In this case one ends up with
results similar to those from the "with-the-flow" hypothesis.

So in essence we have a principle that can produce two different phenomena.


Professor Coen's talk was followed by a presentation by
[Dr Alexandre J. Kabla](http://kalab.emma.cam.ac.uk/index.php)
on the mechanobiology of cell migration and cell rearrangements.

Dr Kabla started off by illustrating that there is a massive amount
of motion during development. In fact most shapes are created by
cell migration.

This motion arises from several different processes:

- Sheet bending/folding
- Convergence extension
- Collective migration

Dr Kabla then described a methodology for understanding some of these
processes, in particular the latter two.

Using microscopy one can record time laps movies of developing embryos.
These images can then be segmented into individual cells, which can be
tracked over time. By looking at the motions of individual cells one can
calculate velocities. All of the velocities can then be used to
create a velocity field. By differentiating the velocity field one
obtains a deformation field, which is a useful representation for
trying to understand tissue formation by motion during development.
The deformation field can, in fact, by used to identify separate
tissues from a blob of cells.

Dr Kabla then went on to describe how one could analyse cell interacalation
(convergence extension) in more detail using the deformation field
representation.

The talk was followed by lunch, which was followed by another talk by Dr Kabla
giving more detail on how the deformation field representation could be used to
study collective migration. After the talk the participants of the course were
invited to to try out some of these analysis using data simulated using
cellular Potts model programs used earlier in the course.
