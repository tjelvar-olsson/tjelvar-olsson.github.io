---
layout: post
title: "Day 11: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The eleventh day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
course was started by
[Dr Arthur D. Lander](http://lander-office.bio.uci.edu/landerfacts.html)
giving pedagogical lecture on morphogen gradients from an engineering
viewpoint.

Dr Lander started off by showing a painting by Hanbusa Itcho representing the
allegory of 
[blind monks examining an elephant](https://en.wikipedia.org/wiki/Blind_men_and_an_elephant).
Where each monk, holding onto a different part of the elephant, is convinced that
his "view" of the elephant is the truth.

Many people working on biological problems approach them using a physics mindset.
In physics the goal is to understand the phenomena. Organisation arises by emergence.
Understanding therefore means grasping how casual laws produce complex phenomena.
In essence the question under investigation is: *how does it work?*

An alternative approach is that used in engineering. In engineering the goal is to
understand performance. Organisation arises by selection for performance.
Understanding therefore means grasping how complex systems achieve specific goals.
In essence the question under investigation is: *why is it built that way?*

These different approaches have led to contrasting views of how biological complexity
has arisen. The physics mindset can lead to thinking that complexity has arisen
by chance as "frozen accidents”, whereas complexity using the engineering mindset appears
to be something that has arisen out of necessity. These two contrasting views are
very similar to the blind monks examining the elephant.

Dr Lander then turned his attention to the vein patterning of Drosophila wings,
which is controlled by two morphogen gradients.
The "textbook" representation of the gene regulatory network involved in this
wing patterning is deceptively simple. But in fact when one looks at all the
data available for the network it balloons into a complex "hairball" diagram.

Creating a morphogen gradient is not inherently difficult. All you need is:

- Localised production
- Random diffusion
- Any sort of decay process

So in this instance the question is not *how*, but *why*. Why is the gene
regulatory network for patterning the Drosophila wing so complex if it is easy
to create a morphogen gradient?

To answer this one needs to consider what the performance objectives might be.
Reasonable biological performance objectives include:

- Stability
- Efficiency (especially important for bacteria)
- Timing
- Evolvability
- Robustness

The reliability of morphogenesis suggests it has high robustness.
Examples include the formation of complex tissues such as the heart and the
striking similarities of monozygotic twins.

Robustness can be defined as being able to achieve a desired goal in face of
perturbations.

So what might the goals be? Some reasonable goals include:

- Constancy (homeostasis)
- Accurately reaching an endpoint
- Adaptation

Perturbations include:

- Altered system parameters (e.g. temperature)
- Altered initial conditions
- Noise (extrinsic or intrinsic)

Dr Lander then went into some detail on how one can use the engineering concept
of sensitivity coefficients to quantify robustness in a unitless way. This looks
at what the fold change in the output is in response to any fold change of the input.
In engineering, a process is considered reasonably robust if the sensitivity coefficient is
lower than 0.3.

Dr Lander then illustrated how one could analytically evaluate the robustness of
different models for generating morphological gradients using sensitivity.
This concept was expanded on, after some coffee, when the participants of the
course were invited to experiment with the concept analytically using
[Mathematica](http://www.wolfram.com/mathematica/).

After lunch
[Dr Nick Monk](http://nick-monk.staff.shef.ac.uk)
gave a talk outlining some of the concepts from his paper
[The Inheritance of Process: A Dynamical Systems Approach](http://onlinelibrary.wiley.com/doi/10.1002/jez.b.22468/abstract).

As scientists we want to understand evolution. So far most of our understanding
has been based on the assumption that there map relating
[genotype](https://en.wikipedia.org/wiki/Genotype) to
[phenotype](https://en.wikipedia.org/wiki/Phenotype)
has got a one-to-one
([bijective](https://en.wikipedia.org/wiki/Bijection)) mapping.

However, the "traditional" view of the genotype-phenotype map as a one-to-one
relationship is probably too simplistic. Take for example the case of
[polyphenism](https://en.wikipedia.org/wiki/Polyphenism), an
ecologically important trait that helps organisms adapt to variable
environment. Polyphenism is widespread and occurs in many plants and animals.

This means that one needs to take the environment into account when thinking
about the genotype-phenotype map. Where a genotype actually encodes a set of
potentialities. Which potentiality is realised is influenced by the environment
and can be stochastic. It is not a simple bijective mapping.

Dr Monk continued to illustrate how the traditional view of the
genotype-phenotype map falls short in terms of helping us understand
evolution as a process before presenting a new formalism
for genotype-phenotype maps by representing them as dynamical systems. 

In this formalism a genotype is specified by:

1. A network topology
2. A set of interaction parameters

Which can be written down using a network model (e.g. boolean network, or a set of ODEs).
The genotype-phenotype map can then be represented using
[phase space](https://en.wikipedia.org/wiki/Phase_space), which has a number of
descriptors that can be useful for understanding the mapping.

1. Attractors (fixed points, cycels, chaotic)
2. Basins of attraction
3. Separatrices
4. Trajectories
5. Initial conditions

Thinking of a genotype as a network means that one has the possibility to trigger
a number of different phenotypes. In other words a single genotype (network) can
have multiple attractors (end points) and phenotypes correspond to trajectories
(not just to attractors). The latter is useful as phenotypes are plastic.

Dr Monk finished by stating that rather than focussing on how evolution can
change the genotype-phenotype, perhaps we should be thinking about how evolution
can change phase space. Perhaps we should think of evolution as an inheritance
of process where discrete changes in genome sequence and allele frequency
results in a change to an underlying continuous dynamics.

Dr Monk's talk was followed by a keynote talk by Dr Lander which expanded on some
of the concepts outlined in the morning's lecture.

Biology is driven by performance objectives. The mechanisms that exist to achieve
these kinds of gaols are collectively referred to as **control**.

What are the problems that you get into when you want to achieve "control"?

Basically control makes things more complex. In fact there is a very strong
relationship between the two and in engineering this is referred to as the
"no-free-lunch principle". In other words, achieving good performance in one
arena often comes at the expense of good performance in another.

In fact curious things happen when you try to achieve control over a great many
things at the same time. The possible solutions start diverging and scatter
exponentially (for more detail see
[Landscape analysis of constraint satisfaction problems; Krzakala and Kurchan; 2007](http://journals.aps.org/pre/abstract/10.1103/PhysRevE.76.021122)).

Dr Lander then illustrated the interplay between control, complexity and
tradeoffs using the example of Drosophila wing patterning. Using modelling, Dr
Lander showed many examples of how introducing a process for controlling a
particular aspect of the morphogen gradient also resulted in loss of control
for a different aspect.

A fundamental issue, in terms of Drosophila wing patterning, may be that there
is not be enough information in a single morphogen gradient. Dr Lander then
illustrated how more control can be realised by using two morphogen gradients,
particularly in conjunction with the toggle-switch architecture.

Dr Lander concluded by stating that if we wish to understand not just what
happens in biology, but why biological systems are built the way they are, we
need to interpret biological organisation in light of principles of control,
and the constrains imposed by selection for control. Trade-offs (the
no-free-lunch principle) are likely to drive the evolution of complexity.  By
focussing on performance, trade-offs and control, we can find *potential*
explanations for at least some of the intricate feedback and feed-forward
interactions that we observe in patterning systems.
