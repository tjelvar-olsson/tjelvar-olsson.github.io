---
layout: post
title: "Day 8: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The fifth day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
course was started by
[Dr Yogi Jaeger](http://www.crg.eu/en/johannes_jaeger)
giving an introductory lecture to
parameter estimation using reverse-engineering.
The talk was illustrated using a case study of
[segmentation](https://en.wikipedia.org/wiki/Segmentation_(biology))
during
[fly embrogenesis](https://en.wikipedia.org/wiki/Drosophila_embryogenesis).

Along the way Dr Jaeger highlighted many of the pitfalls that one need to be
aware of when modelling biological systems. Particular emphasis was put on the
model being a tool, not reality! An outcome of this is that one needs to
pay attention to the model and understand its limitations.

In its most simple form reverse-engineering can be split into three stages:

1. Create a dynamical model
2. Obtain quantitative measurements of data
3. Fit the model to the data

When fitting the model to the data there are two main questions to consider.

1. How do you measure the similarity between the data and the model?
2. Which algorithm are you going to use to fit the data?

One of the simplest ways of measuring the similarity between the model and the
data is to calculate the root mean square deviation. However, other
measures are available and the selection of one over another is context
dependent. It is therefore something that one needs to pay attention to.

Fitting the model to the data, i.e. estimating the parameters, is a global
optimisation problem and there are a number of algorithms available to tackle
it. Traditionally people have been using evolutionary strategy and simulated
annealing algorithms for these types of problems. Evolutionary algorithms are
relatively quick. However, when using them one suffers from not knowing whether
or not the solution identified is the real global minimum. Simulated annealing
algorithms can be more robust, but they are also slower.

Dr Jaeger then mentioned that his lab has had great success with the
[scatter search algorithm](http://www.cleveralgorithms.com/nature-inspired/stochastic/scatter_search.html).
In his hands it can be up to ten times quicker than simulated annealing.

Once one has found a solution one then needs to ask whether or not
it is appropriate. This can be achieved by bootstrapping, i.e.
fitting the model to noisy data. These results can be projected
onto the parameter landscape as ellipsoid confidence regions.
However, this can be slow. A quicker way to estimate these confidence
regions is to calculate the Hessian matrix of the system using linear
approximation.

After lunch 
[Dr Veronica Grieneisen](https://www.jic.ac.uk/directory/veronica-grieneisen/)
gave a talk about cell polarity and how it can be used to understand breaks of
symmetry.

Consider a morphogen gradient. How can it be "read" by cells? Further, how can
this lead to coordinated cell orientations?  Any solution will require some
sort of process of comparison.

The talk then took a slight detour into physics.

How can you make a compass? You can take a needle and magnetize it with an
external field. If you have many needles they will all align in the field. 
Importantly each subunit (needle) will have a "north-south" polarity in the
magnetic field.

Without going to far with the analogy Dr Grieneisen noted that by giving a
cell the concept of polarity it is given a mechanism for aligning within
a larger polarity such as a chemical gradient or a tissue polarity.

Dr Grieneisen then showed work using the cellular Potts model illustrating
how small G-proteins, which can act as molecular switches, can give rise to
cell polarity. However, the modelling found that there was an additional
requirement. The inactive form had to be able to diffuse on a quicker
time-scale than the active form. In this particular case this was achieved by
the active form being constitutively membrane bound, whereas the inactive form
was able to diffuse freely in the cytosol.

Dr Grieneisen pedagogical lecture was followed by a keynote lecture by Dr
Jaeger giving a more detailed exposition of his top-down approach to extracting
structures of networks from gene expression data and his analysis of the model
by looking at phase space and the attractors within it.

By using this reverse-engineering approach Dr Jaeger managed to establish
that the
[AC/DC circuit](http://rsif.royalsocietypublishing.org/content/10/79/20120826)
is a recurring motif in the
[Gap gene](https://en.wikipedia.org/wiki/Gap_gene) network. The AC/DC circuit
is interesting in that it can act both as a positive and a negative feedback
loop. By analysing the phase space and attractors in the AC/DC circuit one
finds that this simple network can give rise to different functions.
Specifically it can act as a:

- switch
- oscillator
- damped oscillator

Dr Jaeger then showed that the AC/DC circuit in the Gap system could be used to create:

- Stable boundaries in the anterior of the fly embryo, set by attractors
- Moving boundaries in the posterior of the fly embryo, governed by a damped oscillator

The participants of the course were then invited to an open-panel session to discuss
"what models are for". This was followed by more hands-on computational exercises
simulating cell polarity in animal and plant cells.
