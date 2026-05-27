---
author: "Rot127"
title: "Research"
layout: "single"
url: "/research"
summary: "research"
tags: ["rizin", "research", "ai", "binary analysis", "reverse engineering"]

ShowToc: true
TocOpen: true
---

# Fuzzy Reverse Engineering

These pages document our effort to develop new reverse engineering (RE) methods
focussing on fuzzy pattern recognition, inductive reasoning with code,
and laying ground work for training RE neural networks on binary properties
instead of source code or natural language.

## The Problem

The definition for "Reverse Engineering" on Wikipedia is (highlights added):

> Reverse engineering [...] is a process or method through which one attempts
> to understand through **deductive** reasoning how a previously made device, process,
> system, or piece of software accomplishes a task with very little (if any) insight into exactly how it does so.

This informal definition reflects well what most people understand of RE today.
But more importantly it shows clearly a long standing assumption that the reasoning process of the
researcher, the algorithms, or the tools used are of **deductive** nature.

Being deductive means to infer conclusions from premises by using logic.
This process is formally very strict.
Given a set of premises one can only use logic operations to reach conclusions.
In theory there is no uncertainty in the answer an algorithm gives or gives not.

Of course, this is not the reality.
All researchers work with hypothesis and educated guesses. Algorithms like
value set analysis (VSA) overestimate the values of data-objects.
Those methods are inductive by nature. They infer results from observations
and concluding that they are "likely" or "unlikely" true.

There is more emerging research on inductive algorithms allowing
for uncertainty in their results. Be it a simply sampling of
control flow graph (CFG) paths for dynamic analysis,
or by throwing decompiled code into a large language models (LLM) and let them annotate it.

These inductive methods are useful because we struggle by definition with the
lack of information we are trying to regain.
And uncertain results can be exceptionally useful, if the alternative are
no results at all or computationally unfeasible.

With the exception of the latest LLM hype reverse engineers still use tools
and algorithms which are effectively from the 90s or 2000s.
Most of them limiting themselves to conclusions reached by deduction.
Although, our inherit lack of information makes it so much more suitable for
inductive algorithms and tools.

With this open research effort we would like to change that and implement
such tools.
Tools which are not academic proof of concepts, but designed for real world problems.

## A word about AI/LLMs

Current reports show that LLMs seem very much capable of finding unintended behavior.
While no one can know how much better the models become, we can assume that there will be improvement.

Our effort here doesn't seek to replace LLMs.
Quite the opposite even. We believe that neural networks will play a big role for the reverse engineering field given
the technology's ability to recognize requested patterns in complex data.
In this regard our research overlaps with AI research.

We do believe though, that training on program semantics instead of syntax (as it is currently done by processing source code),
will yield much better results.

Hence, making the reversed binary _semantics_ more accessible in a form to train on,
is within the scope of this research.

## Problems and (Imagined) Inductive Technical Solutions & Intended Human<>Machine Interaction.

Should be extended continuously.

- ...

## Ethics

- Everything done in open
- Results fully open source.
- Possible consequences if research succeeds and solutions work as intended.
  - ...

## Open Research Procedures

Contributions are welcome!
Happy to cooperate!

- Define problem space, solution space, and add sub-categories for the research we review here.
  - High level research strategy.
  - Intros to basic data structures and concepts. For example:
    - Fuzzy logic
    - Probabilistic logic programming
    - Knowledge Bases design
    - Neural Networks
    - Bayesian reasoning and graphs
    - Markov Chains
    - Factor Graphs
  - Concrete use cases of the concepts above for RE.
  - Ethical considerations.
  - ...?
- Add backlog of papers and books to read.
- Summarize each of them as blog post (in sub-category) for own learning,
  easy entry for newcomers, and proof of expertise for stakeholders.
