---
layout: post
title: "Day 2: Multi-level modelling in morphogenesis"
comments: true
tags:
  - scientific computing
  - computational biology
  - systems biology
---

The theme of the second day of the
[multi-level modelling in morphogenesis](https://www.jic.ac.uk/whats-on/events/2015/07/embo-practical-course-2015/).
course was
*emergent patterns and morphogens*.

The day started with a presentation by
[Dr Stan Maree](https://www.jic.ac.uk/directory/stan-maree/)
who posed the question:

<blockquote>
Can we get spontaneous formation of patterns out of nothing?
</blockquote>

![Zebra](https://upload.wikimedia.org/wikipedia/commons/a/af/ZebraLudolphus.jpg)

During the
[previous day]({% post_url 2015-07-13-day1-multi-level-modelling-in-morphogenesis %})
we learnt that equilibria can be stable or unstable. Now imagine a reaction with a
stable equilibrium and combine it with diffusion. Can we get patterns from that
combination?

In 1952 Alan Turing showed, theoretically, that a stable equilibrium could become
unstable solely due to the diffusion of the chemicals involved.

Dr Maree then worked through the logic of Turing's original paper
[The Chemical Basis of Morphogenesis](http://rstb.royalsocietypublishing.org/content/237/641/37).
In the end Turing showed that in a systems where ``A`` and ``I`` activate
and inactivate each other (or vice versa) an instability will occur if ``A``
also activates its own production, while ``I`` also inhibits its own production
and the diffusion of ``I`` is sufficiently faster than the diffusion of ``A``.

The patterns that are formed by the
[reaction-diffusion system](https://en.wikipedia.org/wiki/Reaction–diffusion_system) are
known as Turing patterns.

Dr Stan Maree's talk was followed by a more sociological talk by
[Dr Nick Monk](http://nick-monk.staff.shef.ac.uk)
who examined the interactions between experimental and theoretical biologists
in the context of the reaction-diffusion system.

In the early days the reaction-diffusion system did not really gain traction
with embryologist who did not like it as a model for development.
They thought it was too sensitive to environmental conditions and as such
too messy for development.

In 1970 Turing patterns started to gain traction with experimental biologists
primarily through the work of Hans Meinhardt.  These models did not have a
molecular basis that could be linked to experimental data - in the spirit of
the time, the were posed in terms of "activity".  A significant problem was
that there was no easy/obvious way of linking experimental data to the
models.

Unfortunately things took a turn for the worse in the 1980s, where perhaps
modellers tried to over reach.  At the time  molecular genetics really got into
its stride.  For a few systems, intense effort brought into focus the molecular
complexity of processes such as Drosophila segmentation and limb development.
At the same time, some reaction-diffusion modellers became bolds about the role
of their models. Unfortunately there was a lack of iterative modeling and
experimentation. Some modellers also failed to realise that a similarity
between ones model and an experimental system does not mean that it is the
model is correct.

At the time George Oester provided a voice of reason:

<blockquote cite="http://www.sciencedirect.com/science/article/pii/0025556488900703">
However, many developmental biologists are now talking a hard look at
the actual contribitnos pattern formaiton models have made ot their field,
and i sense some disillusionment."
</blockquote>

He used lots of data and examined lots of models to find that:

<blockquote cite="http://www.sciencedirect.com/science/article/pii/0025556488900703">
physical and chemical mechanisms hypothesized by the models may be quite different, they
all predict very similar kinds of spatial patterns. Therefore, since the underlying mechanism
cannot in general be deduced from the pattern itself, other criteria must be applied in
evaluation the usefulness of pattern formation models.
</blockquote>

Unfortunatley the paper was published in
[Mathematical Biosciences 90: 265-286 1988](http://www.sciencedirect.com/science/article/pii/0025556488900703)
and as such received little attention by experimental biologists.
Instead a paper by Michael Akam
[Making stripes inelegantly](http://www.nature.com/nature/journal/v341/n6240/abs/341282a0.html)
made the headlines. The paper which appeared as a News and Views article in
Nature claimed that patterns are just made by messy specific systems and that
reaction-diffusion systems were of little importance. This paper
had big negative impact on modellers being able to be listened to by
experimentalists for most of the 1990s.

However, things are getting much better now. Experimentalists are starting to
take reaction-diffusion systems seriously again and the interpretation of
the models are becoming more nuanced. Some experimentalists are even
being inspired by Turing to measure diffusion rates in order to be able
to create better models. Furthermore people are realising that 
reaction-diffusion systems act in concert with other mechanisms such as
gene regulatory networks. The later point was something that Turing pointed
out in his original paper:

<blockquote cite="http://rstb.royalsocietypublishing.org/content/237/641/37">
most of an organism, most of the time, is developing from one pattern into
another, rather than from homogeneity into a pattern
</blockquote>

Dr Monk finished off by re-iterating the key message of his talk, a message
that was made by Oester in 1988: it is the type of instability that is
important. That is what will give you insight. Then you can try to understand
what the underlying players are which allows the mechanism give rise to the
right pattern.

The participants were then invited to work through a number of exercises
and play around with various programs illustrating various aspects of
Turing patterns.

After lunch
[Dr Veronica Grieneisen](https://www.jic.ac.uk/directory/veronica-grieneisen/),
gave a lecture on morphogen gradients and plant development.

The lecture started off by outlining the
[French Flag Theory](https://en.wikipedia.org/wiki/French_flag_model)
created by
[Wolpart in 1969](http://www.sciencedirect.com/science/article/pii/S0022519369800160).
Noting in particular that the original paper was not originally concerned with
morphogen gradients per say, but with how morphogen gradients can be used to
tackle the issue of scaling.

Important considerations were then outlined in terms of:

- Spatial scales: what are the characteristic length and relevant tissue growth?
- Temporal scales: what is the time required to spread a signal growth?
- Robustness: how sensitive is the system's behaviour to perturbations?

The latter point of robustness is multi faceted. It can be parametric
robustness (dosage of genes, levels or rates of enzymes). As well as the
precision of gradients which needs to be considered in terms of having natural
variation among individuals vs. stochasticity within the individual growth

These topics were then considered in the context of a mesoscopic modelling
exercise of the auxin levels at the quiescent center of the root tip during
root growth. As it turned out the high concentration of auxin at the quiescent
centre were achieved by a reflux-driven maximum.  Interestingly, this type of
maximum turned out to have isomorphic counterparts in volcanic micro currents
as well as in counter current transport in kidneys.

Dr Grieneisen lecture was followed by a keynote lecture by
[Dr Yves Couder](http://www.msc.univ-paris-diderot.fr/spip.php?rubrique140&lang=en)
in which he outlined how the patterns formed by
[leaf venation](https://en.wikipedia.org/wiki/Leaf#Veins) are similar
to the cracks observed in old porcelain, as well as dried mud. In all cases
the cracks join up with each other, as opposed to the patterns observed
during crystal growth which does not join up.

These cracks observed in old porcelain can be explained by growth in a tensor
field.
Dr Couder then went on to illustrate several beautiful examples where they manged
to replicate various leaf vein patterns by drying gels on glass plates (where
the static glass plate provided the stress for the tensor field).

The talk then went into more theory and experiments looking at the role for
mechanical stress in growth. Concluding that:

- Externally applied mechanical stress generates an orientation of the
  microtubules (and cell divisions) along the direction of main stress
- In a normal meristem the microtubules become oriented along the direction of
  the mains stresses induced in the L1 layer by turgor pressure
- The deposited microfibrils and the new cell walls strengthen the tissue
  along the direction of largest stress

After all the talks the participants of the course were invited back to
experiment with workshop material on morphogen gradients, diffusion and
permeability, source-decay models, directed transport and the reflux
model.
