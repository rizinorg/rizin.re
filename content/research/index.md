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

These pages document our effort to develop new software reverse engineering (RE) methods
focussing on fuzzy pattern recognition and inductive reasoning with code.

## The Problem

The definition for "Reverse Engineering" (RE) on Wikipedia is (highlights added):

> Reverse engineering [...] is a process or method through which one attempts
> to understand through **deductive** reasoning how a previously made device, process,
> system, or piece of software accomplishes a task with very little (if any) insight into exactly how it does so.

This informal definition reflects well what most people understand of RE today.
But more importantly it shows clearly a long standing assumption that the reasoning process of the
researcher, the algorithms, or the tools used are of **deductive** nature.

Being deductive usually means to infer conclusions from premises by using first- or high-order logic.
This process is formally very strict.
Given a set of premises one can only use these logic operations to reach conclusions.
In theory, deduction doesn't allow for uncertainty in the answer an algorithm gives or gives not.

Of course, this is not the reality.
All researchers work with hypothesis and educated guesses. Algorithms like
value set analysis (VSA) overestimate the values of data-objects.
Those methods are inductive by nature. They infer results from observations
and concluding that they are "likely" or "unlikely" true.

There is more emerging research on inductive algorithms allowing
for uncertainty in their results. Be it a simple sampling of
control flow graph (CFG) paths for dynamic analysis,
or by throwing decompiled code into a large language model (LLM) and let it annotate it.

These inductive methods are useful, because as reverse engineers we struggle by definition with the
lack of information we are trying to regain.
And uncertain results are be exceptionally useful, if the alternative are
no results at all or if it is computationally unfeasible to restore them.

More importantly even inductive methods are suitable for complexity.
Software is inherently complex.
It is simply impossible for a human to understand every moving part of a single binary.
No matter how small it is.

A high level and abstract representation of the binary, even if wrong in the details, brings value.
And yet, most RE Tools give you only magnifying glasses, but no map.

With this open research effort we would like to change that.
Built real world applicable maps, so you know where to look with your magnifying glass.

## Restoring Knowledge Structures

- abstractions and relations of abstractions yield knowledge. Philosophically it reminds me of Frege: a = b
- Each abstraction and relation is only true with a certain probability and certainty.
  - In the RE world a-priori knowledge is not the same as the developers had.
    Because we don't know all inputs (source code, compiler, high level design) leading to that binary. 
- discovering relations between abstractions, also inherits some of the certainty and probability.
- the relations between abstractions are together again abstractions.
- Which can stand in relation.
- And so forth.
- The trick must be that this whole relational network can
  be updated dynamically without recalculating everything.
- Writing good abstractions and relations is the hard part!
- Best case it can be explored and controlled by a machine agent with some intention.

Example: Handlers

- classify calls and tail calls
- observe they are part of a switch
- The switch depends on some integer input value to jump to some call
- That can be classified as a procedure handler (calling the right procedure for a flag).
- Defining these hierarchical and interdependent rules, gives a pattern classify handler code in the memory.

Example: Parsers

- Assume a function getting data, it checks it for being in a valid ASCII range. Returns an int.
- Function can be classified as string to int parsing.
- memory reads which increment their index, passing the data to the convert function.
- can be classified as parsing.

Example: Parser + Handlers

The parser could contain the hand

All those only work if the abstraction and relation rules are build on top of each other.
The more inference rules and abstractions are built, the better becomes the classification.

These classifications can be combined and updated with LLM input or user a-priori knowledge.

This is what a reverse engineer does. But expressed in a computational form instead of scribbles on a notebook.

## Related work

- Binary Ninja supports experimental [semantic search](https://docs.sidekick.binary.ninja/guide/semantic_indexing.html).
  The search term can be a vague description ("TLS handshake"), then matching functions
  are returned. The user has control over the similarity (uncertainty) level of the matching.

## A word about AI/LLMs

Current reports show that LLMs seem very much capable of finding unintended behavior in source code.
While no one can know how much better the models become, we can assume that there will be improvement.

Our effort here doesn't seek to replace LLMs.
Quite the opposite even. We believe that neural networks will play a big role for the reverse engineering field given
the technology's ability to recognize requested patterns in complex data.
In this regard our research overlaps with AI research.

We do believe though, that training on program semantics instead of syntax
(as it is currently done by processing source code),
will yield much better results.

Hence, making the reversed binary _semantics_ more accessible in a form to train on
is within the scope of this research.

## (Imagined) Technical Solutions

### Human<>Machine Interaction

- The user 

### Core implementations

- Implementing a knowledge base (KB) storing all observed facts, detected patterns,
  and rules.
- Implementing (or forking) a language for defining facts, probabilistic rules
  and inductions from other facts and rules.
  - Provide common reasoning structures (Bayes, Markov chains, ...)
- Performance: Implementations must be reasonable gently with resources and
  be designed to allow for improvements.

### Use case bound solutions

- Rule based classification defined by our language
  - Tail calls
  - Type inference from sampled observations.
- User defined point of view.
  If a user gives another a-priori probability for certain observations
  (e.g.: a write memory access at address `a` increases probability of unintended behavior by `p`)
  recompute the results with that probability assumed.
  Add or update that alternative POV to the KB.
- Heat map of binaries showing patterns X.
  - Show memory regions which likely yield unintended behavior.
  - Show memory regions which are likely to interact with a privileged layer.
- Restoring the binaries architecture
  - Classifying strongly connected components.
  - Show memory regions X with a relation R to regions Y.
- Semantic search (See [Semantic Indexing of Binja](https://docs.sidekick.binary.ninja/guide/semantic_indexing.html) as example).

## Ethics

- Everything done in open
- Results fully open source.
- Dual use nature of RE technology.
- Possible consequences if research succeeds and solutions work as intended.
  - ...

## Open Research Procedures

Define problem space, solution space, and add sub-categories for the research we review here.

- High level research strategy.
- Fundamental Concepts
  - Representing knowledge - Applicable questions of ontology and epistemology
    - Reasoning
  - Fuzzy pattern matching
    - Neural Networks
      - Reinforced Learning
  - Probabilistic reasoning
    - Epistemic Logic
    - Fuzzy logic
    - Bayesian reasoning and graphs
    - Markov Chains
- Implementation
  - Knowledge Bases design
  - [Probabilistic logic programming](https://rizin.re/research/language)
- Concrete use cases of the concepts for RE.
  - Extracting the architecture of one or more binaries.
  - Semantic understanding of programs
    - Classification of programs' semantics, binary properties, and attributes.
    - Detecting concrete unintended behavior.
    - Exploitation.
- Ethical considerations.
- ...?

- Add backlog of papers and books to read.
- Summarize each of them as blog post (in sub-category) for own learning,
  easy entry for newcomers, and proof of expertise for stakeholders.

## Want to join?

### Contacts

- [Email](mailto:core@rizin.re)
- [Mattermost](https://im.rizin.re)

### Tasks

- Correct mistakes, point out errors, report unclear explanations.
- Search and add articles to the reading list; explain why they are of interest.
- Sort papers and classify them by importance.
- Read a paper and write a blog-post like summary which condenses the idea +
  interprets it in respect to software reverse engineering.
- Write an overview page about one of the bullet points above.

I hope it is needless to say but:
No AI allowed at this stage.
Understanding is the target, not generating.
