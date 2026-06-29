---
author: "Rot127"
title: "Epistemology"
layout: "blog"
tags: ["rizin", "research", "ai", "binary analysis", "reverse engineering"]

ShowToc: true
TocOpen: false
---

Our task here is essentially to make reasoning computable.
For that we need to consider what we consider _reasoning_ to be, how it
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

## Bayes, The Stability Theory of Belief, Additive Logic of Epistemic Reasons {{< citation LeitgebAdditiveLogicReason2026 >}}

- What are the limits of these theories. Is that how people actually reason? {{< citation douvenRoleExplanatoryConsiderations2015 >}}
  

<!-- ## Common technical terms from a philosophical angle: Abstractions, Relations -->

<!-- **Is abstraction the correct term for what is described below?** -->

<!-- Wikionary {{< citation "Abstractus2025" >}}: -->

<!-- > abstractus (feminine abstracta, neuter abstractum, adverb abstractiter); first/second-declension participle -->
<!-- >  1. drawn away from, having been drawn away from -->
<!-- >  1. alienated from, having been alienated from -->
<!-- >  1. (figuratively) diverted from, having been diverted from -->
<!-- >  1. (Medieval Latin, by extension) abstract (rather than concrete) -->

<!-- - abstractions and relations {{< citation "pollardWhatAbstraction1987" >}} and their modality. -->
<!-- - the relations between abstractions are together again abstractions. -->
<!-- - Which can stand in relation. -->
<!-- - And so forth. -->
<!-- - The initial abstractions are just assumed to be a posteriori. -->
<!-- But people should be able to replace them with a priori knowledge (which are inferred from a posteriori knowledge). -->

### Believe, Uncertainty, and modality

- Lockean thesis and the Lottery-Paradox {{<citation leitgebStabilityTheoryBelief2014>}} {{<citation huberDegreesBelief2009>}}
  - Lottery paradox for our use case: There is a 99% chance that each X isn't Y, but I am certain that at least one X must be a Y.
    I am absolutely certain that the binary reads a file, but the reasoning gave for every code region a 99% chance that it isn't. What to do with that?

- Each abstraction and relation is only true with a certain probability and certainty.
  - The reverse engineers a posteriori knowledge is not the same as the developers had.
    Because we don't know all inputs (source code, compiler, high level design, and intention) leading to that binary.
- discovering relations between abstractions, also inherits some of the certainty and probability.

## Reasoning - On restoring Knowledge

- We write new abstractions by defining new relations between existing abstractions.
- A new abstraction emerges.
- Writing good abstractions and relations is the hard part.
  - How do we know if an abstraction is useful/good/provides something?
- Exploring which abstractions and relations make sense.

- **reasoning methods**: logic, probabilistic axioms, Bayes, Markov, what else?
  - Give examples for each of them.
  - How could they be applied?
  - What can they represent, what can they not?

----

{{< citation_list "Citations" >}}

----

{{< footnote_list "Footnotes" >}}
