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
focusing on fuzzy pattern recognition and inductive reasoning with code.

## The Problem

The definition of "Reverse Engineering" (RE) on Wikipedia is (highlights added):

> Reverse engineering [...] is a process or method through which one attempts
> to understand through **deductive** reasoning how a previously made device, process,
> system, or piece of software accomplishes a task with very little (if any) insight into exactly how it does so.

This informal definition reflects well what most people understand of RE today.
But more importantly, it shows clearly a long-standing assumption that the reasoning process of the
researcher, the algorithms, or the tools used are of a **deductive** nature.

Being deductive usually means inferring conclusions from premises by using first- or higher-order logic.
This process is formally very strict.
Given a set of premises, one can only use these logical operations to reach conclusions.
In theory, deduction doesn't allow for uncertainty in the answer an algorithm gives or does not give.

Of course, this is not the reality.
All researchers work with hypotheses and educated guesses. Algorithms like
value set analysis (VSA) overestimate the values of data objects.
Those methods are inductive by nature. They infer results from observations
and conclude that they are "likely" or "unlikely" true.

There is more emerging research on inductive algorithms allowing
for uncertainty in their results. Be it a simple sampling of
control-flow graph (CFG) paths for dynamic analysis
or throwing decompiled code into a large language model (LLM) and letting it annotate it.

These inductive methods are useful because, as reverse engineers, we struggle by definition with the
lack of information we are trying to regain.
And uncertain results can be exceptionally useful if the alternatives are
no results at all or if it is computationally unfeasible to restore them.

More importantly, even inductive methods are suitable for complexity.
Software is inherently complex.
It is simply impossible for a human to understand every moving part of a single binary,
no matter how small it is.

A high-level and abstract representation of the binary, even if wrong in the details, brings value.
And yet, most RE tools give you only magnifying glasses, not a map.

With this open research effort, we would like to change that.
Build real-world applicable maps, so you know where to look with your magnifying glass.

## On Restoring Knowledge

To give a concrete example of how such an inductive method can look,
consider the following.

### Example: Classifying a string parser function

You open a binary and are interested in checking string parsers in it
because they are often a source of bugs.
What we want is that users can just type a prompt and
see a heat map of code regions.
The brighter certain spots are, the higher the likelihood they are
string-parsing code.

How could this be done?

---

Let's consider what indicators there are to infer that some code is
parsing a string:

- It could call functions that have something to do with regular expressions.
- It could contain loops breaking on a read `\0` byte.
- It could check read bytes if they are in the ASCII range.
- It could pass read bytes to `atoi`.
- It references strings that have the word "parser," "parsing error," or "parse" in them.
- Its memory access patterns could be
  - sequential (reading character by character, word by word)
  - or jump once, then read sequentially (reading an offset to a substring, parse the substring).
- And probably many more indicators...

Some of these indicators give stronger evidence than others.
For example, if code references a string that reads something like "parsing error at 0x%x,"
it is a strong indicator it parses something (not necessarily strings, though!).
The memory access patterns in the loops are maybe less significant.
But if we observe a "parsing error" message referenced in a loop that breaks on a `\0` byte,
we can be pretty certain.

Let's visualize how the indicators could impact an inferred probability that the observed code
is indeed parsing strings:

![Example of inferring from observations a new property](/images/example_string_parsing.svg)

The probability assigned to the indicators can come from many different sources.
The `regex_match()` call is maybe statically known, and we are 100% sure the code calls it.
We also know that there is a loop that breaks on `\0`.
There was also some code _possibly_ checking for ASCII, but that is uncertain.
`atoi` was definitely not called, and while there were some strings referenced that were similar to "parser,"
there was no direct match. And lastly, the memory access pattern didn't really follow our requirements.

The result is 0.7, which can be plotted as a shade in a heat map, giving an idea
of how strong the "string parser" categorization is compared to other code regions.

It is important to understand that the probabilities of these observations
can, in themselves, be a product of a similar reasoning structure.
For example, the `refs "parser" string` could come from a Jaro–Winkler distance,
`Checks ASCII` from symbolic execution, `Calls atoi` could be checked statically,
and `Loop breaks for \0` was maybe set manually by the user.

You quickly see that one can build reasoning networks like that.
Of course, these don't need to use these naive computations of weighting and summing probabilities.
Bayesian networks, Markov chains, or whatever you can implement in code are possible.

In the end, this is what a reverse engineer does.

Except it is expressed in a computational form instead of scribbles in a notebook.

---

For a more fundamental philosophical discussion of this process, see the [Epistemology](/research/epistemology) page.

## A word about AI/LLMs

Current reports show that LLMs seem very much capable of finding unintended behavior in source code.
While no one can know how much better the models become, we can assume that there will be improvement.

Our effort here doesn't seek to replace LLMs.
Quite the opposite, even. We believe that neural networks will play a big role in the reverse engineering field given
the technology's ability to recognize requested patterns in complex data.
In this regard, our research overlaps with AI research.

We do believe, though, that training models on more diverse inputs than just
source code will yield much better results.

Hence, making the reversed binary semantics more accessible in a form to train on
is within the scope of this research.

## Solutions

The notes below are very rough and the field of our work.
They should be understood as sketches, notes, and keywords.

If one of them was looked at in more detail, it will link to a page
describing its dimensions.

If any of those points below strike you as odd or you have questions about what they mean,
please don't hesitate to contact us (preferably on [Mattermost](https://im.rizin.re)).

### Philosophy of epistemology and ethics

The philosophical part is obviously not the main point of the research.
Nonetheless, I believe it is essential to concern ourselves with it:
1. The question of how to gain meaning from complexity is not a purely technical one.
  It is a philosophical question in itself.
  Ignoring the research of a whole discipline would simply hinder our effort for no good reason.
2. I assume that trying to look at our problem from philosophical points of view
  will help us keep the bigger picture.
  It is far too easy to get lost in technical details.
  There is no need to accelerate this notion.

#### Epistemology

- [Abstractions, relations, and their modality.](/research/abstraction_relation)
  Find a suitable description for our use case.
- How do we gain meaning about the world (about our technical system)?
- Is our language sufficient to describe the substance?
  - What are the consequences of building up knowledge about complexity?

#### Ethics

- Dual-use nature of RE technology.
- Technology as a way to project power. Power for whom?
- Everything here is done in the open.
- Results fully open source.
- Possible consequences if research succeeds and solutions work as intended.
- What could be unknown unknowns?

### Human<>Machine Interaction

- Semantic search. Type text, get objects served for that category.
- Multiple representations of knowledge.
  - the language, names, graphs, heat maps, plots, diagrams, highlights in assembly, what else?

### Core implementations

- Implementing a knowledge base (KB) or knowledge representation storing all observed facts, detected patterns,
  and rules.
  - How is it represented: table-like (row-, column-oriented), graph, hypergraph?
- Implementing (or forking) a language for defining facts, probabilistic rules,
  and inductions from other facts and rules.
  - Provide common reasoning structures (Bayes, Markov chains, ...)
- Performance: Implementations must be reasonably gentle with resources and
  be designed to allow for improvements.

### Specific use case solutions

- Rule-based classification defined by our language
  - Tail calls
  - Type inference from sampled observations
- User-defined point of view.
  If a user gives another a posteriori probability for certain observations
  (e.g., a write memory access at address `a` increases the probability of unintended behavior by `p`),
  recompute the results with that probability assumed.
  Add or update that alternative POV to the KB.
- Heat map of binaries showing patterns X.
  - Show memory regions that likely yield unintended behavior.
  - Show memory regions that are likely to interact with a privileged layer.
- Restoring binary architecture
  - Classifying strongly connected components
  - Show memory regions X with a relation R to regions Y.
- Semantic search (see Semantic Indexing of Binja {{<citation UsingSemanticIndexing>}} as an example).

## Open Research Procedures

Define the problem space, the solution space, and add subcategories for the research we review here.

- High-level research strategy
- Fundamental Concepts
  - Representing knowledge - Applicable questions of ontology and epistemology
    - Reasoning
  - Fuzzy pattern matching
    - Neural networks
      - Reinforcement learning
  - Probabilistic reasoning
    - Epistemic logic
    - Fuzzy logic
    - Bayesian reasoning and graphs
    - Markov chains
- Implementation
  - Knowledge base design
  - [Probabilistic logic programming](/research/language)
- Concrete use cases of the concepts for RE
  - Extracting the architecture of one or more binaries
  - Semantic understanding of programs
    - Classification of program semantics, binary properties, and attributes
    - Detecting concrete unintended behavior
    - Exploitation
- Ethical considerations
- ...?

- Add a backlog of papers and books to read.
- Summarize each of them as a short blog post (in subcategory) for our own learning,
  easy entry for newcomers, and proof of expertise for stakeholders.

## Want to join?

### Contacts

- [Email](mailto:core@rizin.re)
- [Mattermost](https://im.rizin.re)

### Tasks

- Correct mistakes, point out errors, report unclear explanations.
- Search for and add articles to the reading list; explain why they are of interest.
- Sort papers and classify them by importance.
- Read a paper and write a blog-post-like summary that condenses the idea and
  interprets it with respect to software reverse engineering.
- Write an overview page about one of the bullet points above.

I hope it is needless to say, but:
No AI allowed at this stage.
Understanding is the target, not generating.

## Related work

- Binary Ninja supports experimental [semantic search](https://docs.sidekick.binary.ninja/guide/semantic_indexing.html).
  The search term can be a vague description ("TLS handshake"); then matching functions
  are returned. The user has control over the similarity (uncertainty) level of the matching.

{{< citation_list "References" >}}
