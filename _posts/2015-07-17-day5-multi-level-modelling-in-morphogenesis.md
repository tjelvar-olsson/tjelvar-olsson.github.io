---
layout: post
title: "Day 5: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The fifth day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/)
course was started by
[Professor Przemyslaw Prusinkiewicz](http://www.cpsc.ucalgary.ca/~pwp)
giving an introductory lecture to
[L-systems](https://en.wikipedia.org/wiki/L-system).

However, before going into L-systems Professor Prusinkiewicz introduced the
topic of computational modelling more generally. In particular he highlighted
[J. C. R. Licklider](https://en.wikipedia.org/wiki/J._C._R._Licklider), who amongst
other things was one of the founders of the internet. In his essay
[Man-Computer Symbiosis](http://groups.csail.mit.edu/medg/people/psz/Licklider.html)
Licklinder had the vision that man will start interacting with computers
in the same way as they would interact with a colleague.

Professor Prusinkiewicz also highlighted the work of
[Alan Kay](https://en.wikipedia.org/wiki/Alan_Kay) who had the vision of
[A Personal Computer for Children of All Ages](http://www.mprove.de/diplom/gui/Kay72a.pdf) in 1972.

At the time the computational resources were not available for either of these
visions. However, now they are! Which means that this is a great time to be a
modeller. *You can now treat your laptop as a colleague whose skills
supplement your own.*

Professor Prusinkiewicz then went on to discuss some of the issues of modelling
in developmental biology. There are two main issues. First of all development is
a spatio-temporal process. Secondly, a developing organism is a dynamical
**system** with a dynamical **structure**. For example, one could look at a plant
as a system of components that are all developing over time.

These issues were dealt with by
[Astrid Lindenmayer](https://en.wikipedia.org/wiki/Aristid_Lindenmayer) in
[Mathematical models for cellular interactions in development](http://www.sciencedirect.com/science/article/pii/0022519368900799).
The formalisms developed by Lindenmayer are now often referred to as
L-systems or Lindenmayer systems.

A L-system basically consists of an alphabet, a set of productions (rules for
converting letters of the alphabet into new strings of letters from the
alphabet), and an axiom (the starting string).  Using a recursive algorithm a
string, starting from the axiom, is continually transformed by the production
rules.

Using
[turtle geometry](https://en.wikipedia.org/wiki/Turtle_Geometry) L-systems can
used to create fractal structures using very simple rules.

Professor Prusinkiewicz then went on to describe how the L-system can be used
to model the development of plants by having an alphabet of basic plant modules
such as stems, branches, flowers, etc.


This was followed by a practical session where people got to experiment with
L-system modelling using the
[Vlab](http://algorithmicbotany.org/virtual_laboratory/) software.

The practical session was followed by a pedagogical lecture on phyllotaxis
by
[Dr Yves Couder](http://www.msc.univ-paris-diderot.fr/spip.php?rubrique140&lang=en).

[Pyllotaxis](https://en.wikipedia.org/wiki/Phyllotaxis) is the arrangement of
leaves on a plant stem. There are a very small number of archetypes of organisations.
Leaves can be organised into spiral nodes or whorled modes.

Spiral patterns are also important in parastichy, which can be observed for example
in pine cones and the organisation of sun flower seeds.

![Parastichy pattern](https://upload.wikimedia.org/wikipedia/commons/a/aa/Pflanze-Sonnenblume1-Asio.JPG)

Interestingly the spiral node pattern of leaves can be related to parastichy by
compressing the stem.

Previously many people thought that these types of complex patterns could only
be the result of complex biological processes. Particularly as these patterns
where only observed in botany.

However, Dr Couder illustrated how he could reproduce these pattern using a
physical system consisting of a ferrous material dropped into oil on a petri
dish. The ferrous drops where pushed towards the edge of the petri dish by a
magnetic field. As more ferrous drops were added the parastichy pattern emerged
by virtue of the repulsive dipole interactions with the nearest neighbor drops
that had already been deposited in the oil.

Dr Couder then went into a more mathematical
description of these patterns and how they relate to
[Fibonacci numbers](https://en.wikipedia.org/wiki/Fibonacci_number)
and the
[golden ratio](https://en.wikipedia.org/wiki/Golden_ratio).
For more details on this fascinating work relating botany to mathematics see
the excellent Science News article
[The Mathematical Lives of Plants](https://www.sciencenews.org/article/mathematical-lives-plants).

After lunch the participants were invited to continue thinking about modelling
by going out into the field and taking photographs of interesting plant
patterns.  This was followed by a show and tell session where people were
encouraged to think about and discuss possible mechanisms that could be used to
produce the pattern in question. This was followed by more computer modelling
using L-systems.
