---
author: "Rot127"
title: "Bayes"
layout: "blog"
tags: ["rizin", "research", "bayes", "bayesian networks", "reverse engineering"]

ShowToc: true
TocOpen: false
---

# Literature and Experiments

**Experiments**

See https://github.com/rizinorg/rz-reason-exp/tree/main/bayes

**Recommended reading**

Chapter 1 in Foundations of the Theory of Probability by Kolmogorov {{< citation "FoundationsTheoryProbability" "Chapter 1" >}}.
This is the standard book defining all basics (and some non-basics) of probability theory.

For Bayesian networks you can refer to Stephenson {{<citation IntroductionBayesianNetworks2000>}} or
the Bayesian Network lectures of Stanford class CS221 {{<citation StandfordCS221Bayes>}}.

# Probability and Bayesian Networks

## Intro probability and Bayes

- Addititivity
- Independence of variables.
- Intro bayes, bayesian networks (model)

## The example

- Describe example.

## Discussion of applicability of bnets

- How to know the probability of events?
  - Prerequisits: Event probabilities all must sum up to one. There are no Unkown Unknowns.
    Which is of course not realistic.
  - Sampling
    - Some use cases provide this. Which ones?
      - E.g. Obfuscated code: Have a list of know code obfuscation techniques -> count them.
      - Measuring is not always possible. Or binary is too small to get a decent sample size.
  - Knowing/guessing
    - Maybe the most common case?
    - Expressing a feeling in terms of a probaility is almost certaintly wrong.
      Because: What is the disctribution? Why this probability? Where does this number come from.
      People like to do it a lot. But it _assumes_ that:
      - their previously observed events (experience) is equivalent to the real distribution of the population.
      - that they have no sampling bias, from that _felt_ distribution.
      All if probably not the case. Plenty of experts are wrong all the time.
      Maybe less so with our case.
  - Training
    - Is very resources extensive. Requires a lot of data, with information
      of about the variables we want to learn about. But those varaibles are
      by nature often not know ("undefined behavior" is not in the debug info).
    - Shit data in, shit data out.
- Independence of variables
  - How do we know that the variables chosen are independent?
    - Example ASLR and other write primitive variables.
    - Maybe an example?
  - For complex models this should almost be impossible to guarantee.
  - Could make addition to the model complicated. If we would like to add a new variable,
    but know it is dependent on some other.
    - This means we would need to split up this dependent variable into its own network.
- Addititivity, what does it mean for it?

## When to build a Bayesian Model?

- Probabilities can be measured.
- If training is viable (e.g. distinguish two functions with the same FLIRT signature).
- Most probabilities are known, rest can be guessed by researcher.
  - Guessed probabilities are not disbuted, i.e. almost all researchers would agree with these guesses (so the likelyhood of them being wrong is low).
- Researcher assumes very few Unkowns Unknowns.
- Variable independence?
- Additivity?


{{< citation_list "Citations" >}}

{{< footnote_list "Footnotes" >}}
