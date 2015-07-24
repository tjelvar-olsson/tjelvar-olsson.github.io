---
layout: post
title: "Day 10: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The tenth day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
course was started by
[Professor Enrico Coen](https://www.jic.ac.uk/directory/enrico-coen/)
giving a pedagogical lecture on modelling growth and deformation.

Most of us are used to thinking about geometrical deformations. However, the
mathematical principles behind geometrical deformations are quite different
from the principles behind biological deformation. For example, transforming a
square into a trapezoid, as if viewing the box from a perspective, is
easy using a geometrical deformation/transformation. However, achieving this
type of deformation in a biological system using growth is non-trivial. One
of the complicating factors is that the tissue in a biological system is
an interconnected material.

What is growth anyway? Does it depend on the number of cells? Is it something
continuous or something discrete? Is the process of growth absolute (accretion)
or relative to the size of the tissue? Much time was spent discussing the
implications of these questions with regards to coming up with a definition of
growth.

For the purposes of his talk Professor Coen defined growth as a tissue getting bigger,
independently of the number of cells. And on the flip-side he defined a tissue
reducing its size as "shrinkage".

Professor Coen then expanded on the concept of growth rates, which he defined
to be relative and continuous in the context of the growth of a tissue. He then
explained how the growth rates can be inferred from velocities. If a velocity
is changing one is observing either growth or shrinkage. Similar to Dr Kabla,
the preivous day, professor Coen took the derivative of the velocity field to
get a growth tensor. The growth tensor can be represented as:

- Growth rate
- Anisotropy
- Direction
- Rotation

The growth tensor can be estimated by microscopy time laps movies, where
one can track cell vertices over time.

Furthermore, the growth tensor concept can be used to model tissue growth.
By specifying the growth rate, anisotropy and direction one can get deformations,
conflicts and rotations as emergent features.

The participants where then invited to experiment with these concepts by modelling
tissue growth in three dimensions.
