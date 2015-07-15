---
layout: post
title: "Day 3: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The third day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
started off with a lecture by
[Dr Veronica Grieneisen](https://www.jic.ac.uk/directory/veronica-grieneisen/),
giving an introduction to the
[Cellular Potts Model](https://en.wikipedia.org/wiki/Cellular_Potts_model).

The talk started off with a statement that biophysics, or simply physics,
constrains and drives the development and (embryonic) tissues share
properties with fluids.

So why do clusters of cells form similar structures to forths and bubbles?
Basically some of the principles are the same: the tendency to minimise
area and conserve (topological) constraints give rise to a frustration which
generates characteristics configurations.

In 1964 Steinberg formulated the
[differential adhesion hypothesis](https://en.wikipedia.org/wiki/Differential_adhesion_hypothesis)
in which he made the comparison between cells and immiscible fluids. He used the idea
that cell types present different adhesive and cohesive interactions to derive
that the final configurations are established by obtaining a minimal interface
free energy through successive changes in cell content.

This means that differential inter-cellular adhesion is on of the most
important factors in cell sorting.

It did however take some time before this could be modelled. People tried doing
it using cellular automata. However, this turned out to be (close to)
impossible.

Some time later people started experimenting with the Cellular Potts Model.
A significant difference between the cellular Potts model and cellular automata is
the representation of a biological cell. In cellular automata a cell is
represented by a single pixel, whereas in the cellular Potts model a biological
cell is represented by lots and lots of pixels.

The
[Cellular Potts Model](https://en.wikipedia.org/wiki/Cellular_Potts_model)
is simply a Hamiltonian consisting of terms for adhesion, volume conservation
and cortical tension. The latter being a more recent addition.

The cellular Potts model is driven by Monte Carlo sampling where the edges of the
cells are allowed to change state into neighboring cells. The energy of each pixel
is then evaluated and if the energy is lowered the change is accepted. If the
energy of a change increases the energy the change can still be accepted. The
likelihood of this is evaluated using a probability function that makes it more
unlikely as the energy difference increases.

As it turns out the cellular Potts model can capture both the stochastic nature
of cell dynamics and cell sorting behaviour.

The formalisms of the cellular Potts model were then described in some detail
before the talk was concluded by stating that the cellular Potts model is an
energy-based model that describes surface mechanics (adhesion, membrane
fluctuations, internal pressures, cortical tension). As a result one can use it
to talk about macroscopic phenomena such as cell shape, cell sorting, tissue
surface tension, cell movement, stresses and shape changes, as well as
stresses and strains trough a tissue.

It is also possible to extend the cellular Potts to model specific biological problems
such as chemotaxis and cell differentiation. Furthermore it can be used in conjunction with
other models to look at sub-celluar details such as gene regulatory networks. **It therefore
serves as a great tool for cell-based modelling of morphogenesis.**
