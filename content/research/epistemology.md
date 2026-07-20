---
author: "Rot127"
title: "Epistemology"
layout: "blog"
tags: ["rizin", "research", "ai", "binary analysis", "reverse engineering"]

ShowToc: true
TocOpen: false
---

# Literature

## Expected

- Knowledge engineering: building cognitive assistants for evidence-based reasoning - Chapter 1 {{ citation KnowledgeEngineeringBuilding2016 "Chapter 1"}}

## Recommended

- ...

# Philosophy as Starting Point

Our task here is essentially to make reasoning computable.
For that we need to consider what _reasoning_ is, how it
leads to knowledge, how we model it, and how we implement it.

By taking philosophy as a primary starting point, instead of AI research,
I hope we open up the space of ideas.

Even if philosophical theories are usually not as strictly stated as
computer science ones, analytically philosophy can already be specific enough
to see characteristics of interest for us.

Take Russel's Denotation Theory {{< citation russellDenoting1905 >}} as example.
It essentially describes propositional functions {{<citation PropositionalFunction2025>}}.

Let's take one example from his essay:

> E.g., it is true (at least we will suppose so) that the earth revolves round the sun,
> and false that the sun revolves round the earth; hence “the revolution of the earth round the sun” denotes an
> entity, while “the revolution of the sun round the earth” does not denote an entity.!

So for
\[
\begin{aligned}
E(x) := x \text{ is earth} \\
S(x) := x \text{ is sun} \\
a R b := a \text{ orbits around } b \\
\exists x, y (S(y) \land E(x) \land \forall z (E(z) \to z = x \oplus S(z) \to z = y)) \land x R y)
\end{aligned}
\]

## Reasoning Theories

Here we can add intros for each reasoning theory we took a look at.
If you find a good introduction about one, explaining the theory well,
it is possible to simply add a link to the resource here.

It is not necessary to write yet another introduction if there are already plenty.
But you must have read and understood the original work.
Just adding a reference and assume it is good is not sufficient.

Each theory should have computational examples attached.

TODO: Add link to repo

### Bayes

### Theory of Belief

### Baconian Probability

### The Stability Theory of Belief

Very, very new stuff

### Additive Logic of Epistemic Reasons {{< citation LeitgebAdditiveLogicReason2026 >}}

Very, very, very new stuff.
Possibly related to the "Stability Theory of Belief"

## Theory comparisons

- What are the limits of these theories.
- Is that how people actually reason? {{< citation douvenRoleExplanatoryConsiderations2015 >}}
- What can each of them do, what can they not?

----

{{< citation_list "Citations" >}}

----

{{< footnote_list "Footnotes" >}}
