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

**Introductionary reading**

Chapter 1 in Foundations of the Theory of Probability by Kolmogorov {{< citation "FoundationsTheoryProbability" "Chapter 1" >}}.
This is the standard book defining all basics (and some non-basics) of probability theory.

For a more modern and less math heavy introduction see A modern introduction
to probability and statistics {{<citation dekkingModernIntroductionProbability2010>}}

For Bayesian networks you can refer to Stephenson {{<citation IntroductionBayesianNetworks2000>}},
the Bayesian Network lectures of Stanford class CS221 {{<citation StandfordCS221Bayes>}},
or the lecture notes from University of Torronto class CS 486/686 {{<citation dekkingModernIntroductionProbability2010>}}.

# Probability and Bayesian Networks

## Intro probability and Bayes

See what is necessary to refresh for the experiment.

## Examples

Let's turn to two examples to show how BNs could be used in RE.
The `unintended jump` example is intentionally somewhat loose, highlighting the problems of BN application.
The second example is a shortened version of the function detection from FunProbe {{<citation kimFunProbeProbingFunctions2023>}}.
It is an already implemented prototype and shows more down to earth application.
In the discussion we will compare the obvious problems from the first example to the second,
showing that they are still valid.

### Example - Unintended Jump Target

Our goal: Decide for a branch instruction, if it can branch to an unintended address.

> "Unintended" loosly means "any locations the developers did not intend to be reached from this point of the program".


## Discussion of applicability of BNs

- How to know the probability of events?
  - Sampling
    - Some use cases provide this. Which ones?
      - E.g. Obfuscated code: Have a list of know code obfuscation techniques -> count them.
      - Measuring is not always possible. Either the binary is too small to get a decent sample size.
        For some niche architectures there might not be enough public binaries to sample from.
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
- Dependence of variables
  - BNs have to be a DAG.
    - For complex models this might be impossible to guarantee.

## When to build a Bayesian Model?

- Probabilities can be measured.
- If training is viable (e.g. distinguish two functions with the same FLIRT signature).
- Most probabilities are known, rest can be guessed by researcher.
  - Guessed (a-priori) probabilities are not disbuted, i.e. almost all researchers would agree with these guesses (so the likelyhood of them being wrong is low).
- Researcher assumes to have very few Unkowns variables.
- Model is small enough, since computing BNs is relatively heavy **SOURCE**

{{< citation_list "Citations" >}}

{{< footnote_list "Footnotes" >}}
