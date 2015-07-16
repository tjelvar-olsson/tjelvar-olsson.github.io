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
course started off with a lecture by
[Dr Veronica Grieneisen](https://www.jic.ac.uk/directory/veronica-grieneisen/),
giving an introduction to the
[cellular Potts model](https://en.wikipedia.org/wiki/Cellular_Potts_model).

The talk started off with a statement that biophysics, or simply physics,
constrains and drives tissue development. And that (embryonic) tissues
share properties with fluids.

So why do clusters of cells form similar structures to froths and bubbles?
Basically some of the principles are the same: the tendency to minimise
area and conserve (topological) constraints give rise to a frustration which
generates characteristics configurations.

In 1964 Steinberg formulated the
[differential adhesion hypothesis](https://en.wikipedia.org/wiki/Differential_adhesion_hypothesis)
in which he made the comparison between cells and immiscible fluids. He used the idea
that cell types present different adhesive and cohesive interactions to postulate
that the final configurations are established by obtaining a minimal interface
free energy through successive changes in cell contacts.

This means that differential inter-cellular adhesion is one of the most
important factors in cell sorting.

It did however take some time before this could be modelled. People tried doing
it using cellular automata. However, this turned out to be (close to)
impossible.

Some time later people started experimenting with the cellular Potts model.
A significant difference between the cellular Potts model and cellular automata is
the representation of a biological cell. In cellular automata a cell is
represented by a single pixel, whereas in the cellular Potts model a biological
cell is represented by lots and lots of pixels. The latter is therefore able to
represent cell shape.

The
[cellular Potts model](https://en.wikipedia.org/wiki/Cellular_Potts_model)
is simply a Hamiltonian consisting of terms for adhesion, volume conservation
and cortical tension. The latter being a more recent addition.

The cellular Potts model is driven by Monte Carlo sampling where the edges of the
cells are allowed to change state into neighboring cells. The energy of each pixel
is then evaluated and if the energy is lowered the change is accepted. If a
change increases the energy the change can still be accepted. The likelihood of
accepting an energetically unfavourable change is evaluated using a probability
function that makes it more unlikely as the energy difference increases.
The reason for accepting some energetically unfavoured changes is so that the
system can be driven towards the global minimum over time.

As it turns out the cellular Potts model can capture both the stochastic nature
of cell dynamics and cell sorting behaviour.

The formalisms of the cellular Potts model were then described in some detail
before the talk was concluded by stating that the cellular Potts model is an
energy-based model that describes surface mechanics (adhesion, membrane
fluctuations, internal pressures, cortical tension). As a result one can use it
to talk about macroscopic phenomena such as cell shape, cell sorting, tissue
surface tension, cell movement, stresses and shape changes, as well as
stresses and strains through a tissue.

It is also possible to extend the cellular Potts to model specific biological problems
such as chemotaxis and cell differentiation. Furthermore it can be used in conjunction with
other models to look at sub-celluar details such as gene regulatory networks. **It therefore
serves as a great tool for cell-based modelling of morphogenesis.**

The lecture was followed by a practical session where the participants got
hands on experience of using the cellular Potts model for studying cell
sorting.

After lunch
[Dr Stan Maree](https://www.jic.ac.uk/directory/stan-maree/)
took over where the morning's lecture had ended by illustrating how
the cellular Potts model can be used to model cellular movement and
morphogenesis.

The first example illustrated how the cellular Potts model could be extended to
model movements of cells in lymph nodes. The movement of T-cells in the lymph
node can be described as random persistent motion. The reason for this type of
movement is that T-cells want to move past as many dendritic cells as possible
(and vice versa) in a short a time as possible.

The model was created from:

- T-cells set to be persistently moving
- Dendritic cells (including extensions)
- Reticular network (undeformable)
- Correct sizes, densities, and shapes of cells
- Fitting speed and motility

Where the persistent T-cell movement was created from:

- Continuously adjusting the target direction
- Continuously adjusting the directional persistence
- Adjustment according to the reticular network

The model created managed to reproduce: short term persistent motion,
long term random motion, and the experimentally observed "stop-and-go"
behaviour. The latter had not been incorporated into the model and was
previously thought to occur from a syncronised clock in the T-cells. These and
other simulations then promted further and longer time-lapse experiments from
the experimental biologists that disproved the internal clock hypothesis.

The second example illustrated how the cellular Potts model could be combined
with chemotaxis, cell differentiation and gene regulatory networks to model
complex developmental changes during
[gastrulation](https://en.wikipedia.org/wiki/Gastrulation).

Dr Maree's lecture was followed by a keynote talk by
[Professor Shigeru Kondo](http://www.fbs.osaka-u.ac.jp/labs/skondo/indexE.html).

Professor Kondo gave a fascinating talk describing his quest to find evidence of
reaction-diffusion system animals.

He did this by turning the traditional work flow of a molecular biologist on its head.
Usually a molecular biologist would:

1. Find mutants
2. Identify all the genes involved in the phenomenon
3. Identify the functions of all those genes
4. Clarify the whole interaction network
5. Do calculation to make sure the identified system can reproduce the interested phenomenon

However professor Kondo decided that if he was to find evidence for the reaction-diffusion
system he would need to use the theory before doing the experiments. As such he set out to:

1. Do many computer simulations
2. Extract important characteristics
3. Predict something unexpected
4. Show that it can happen!

He then illustrated how his group had applied this methodology of modelling
first and experimenting second to find extraordinary evidence of the
reaction-diffusion system in fish.

For example, one prediction made from the Turing reaction-diffusion system
was that fish stripes should be able to migrate, specifically stripes
that bifurcate. At the time Professor Kondo asked leading experts of
fish developmental biology if this had every been observed. However, they
replied that it had not. Undeterred Dr Kondo started looking
for evidence of this behaviour and was able to find it in the skin of
maring anglefish, see
[Kondo and Asai; Nature (1995)](http://www.fbs.osaka-u.ac.jp/labs/skondo/paper/kondo%20Nature%201995.pdf).

After several other striking examples of prediction followed by experimental
evidence Professor Kondo concluded by stating that Turing systems had proved an
effective tool for understanding patterning. However, he made the point clear
that the underlying mechanism is probably encoded in cell motility rules rather
than necessarily in an activator and inhibitor.
