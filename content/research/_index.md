---
author: "Rot127"
title: "Research"
layout: "blog"
url: "/research"
summary: "research"
tags: ["rizin", "research", "ai", "binary analysis", "reverse engineering"]

ShowToc: true
TocOpen: true
---

# Abstract

## On Restoring Knowledge

To give a concrete example of how such an inductive method could look,
consider the following.

### Inductive Example: Classifying a string parser function

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

Let's visualize how the indicators could impact an inferred certainty that the observed code
is indeed parsing strings.:

![Example of inferring from observations a new property](/images/example_string_parsing.svg)

The certainty of the indicators (mapped to a `[0,1]` interval
{{<footnote "Don't confuse this with probability. Probability theory obeys certain axioms. Using 'certainty' here is meant to be vague, so it simply conveys the idea instead of being too technical.">}})
can come from many different sources.
The `regex_match()` call is maybe statically known, and we are 100% sure the code calls it.
We also know that there is a loop that breaks on `\0`.
There was also some code _possibly_ checking for ASCII, but that is uncertain.
`atoi` was definitely not called, and while there were some strings referenced that were similar to "parser,"
there was no direct match. And lastly, the memory access pattern didn't really follow our requirements.

The result is 0.7, which can be plotted as a shade in a heat map, giving an idea
of how strong the "string parser" categorization is compared to other code regions.

It is important to understand that the certainties for these observations
can, in themselves, be a product of a similar reasoning structure.
For example, the `refs "parser" string` could come from a Jaro–Winkler distance,
`Checks ASCII` from symbolic execution, `Calls atoi` could be checked statically,
and `Loop breaks for \0` was maybe set manually by the user. {{<footnote "A.k.a information integration: https://en.wikipedia.org/wiki/Information_integration">}}
Of course, the network's rules don't need to use these naive computations of weighting and summing.
Factor graphs, Bayesian networks, Markov chains, or whatever you can implement in code are possible.

You quickly see that one can build pretty complex reasoning networks like that.
Changing their results the more observations happen.

In the end, this is what a reverse engineer does.

Except it is expressed in a computational form instead of scribbles in a notebook.

### Abductive Example: Enhancing LLM reasoning with rapid access to low level facts.

LLMs are already a huge help in generating hypothesis about artifacts in a binary.

"What is the purpose of this function?", "What does the data could represent?",
"What should I do, if I look for the bootloader code in this binary?"

Of course, the LLM might hallucinate more or less in the answers.
Though, if the researcher is not familiar with the type of binary and struggles
to find come up with new ideas, even a rough direction can help.

Consider the following function from a niche architecture (Hexagon).

```
; segment.modem.b02
┌ fcn.fe102000();
│           0xfe102000      ┌   immext(##0x80)
│           0xfe102004      └   R3:2 = combine(##0xad,#0x0)
│           0xfe102008      ┌   P0 = boundscheck(R1:0,R3:2):raw:lo
│       ┌─< 0xfe10200c      │   if (P0.new) jump:t 0xfe102030
│       │   0xfe102010      └   R2 = ##0x0
│       │   0xfe102014      [   R2 = ##0xce
│       │   0xfe102018      ┌   immext(##0xc0)
│       │   0xfe10201c      └   R3:2 = combine(##0xda,R2)
│       │   0xfe102020      ┌   P0 = boundscheck(R1:0,R3:2):raw:lo
│      ┌──< 0xfe102024      │   if (!P0.new) jump:nt 0xfe102050
│      ││   0xfe102028      └   R2 = ##0x4
│      ││   0xfe10202c      [   R0 = add(R0,##0xffffff32)
│      │└─> 0xfe102030      ┌   immext(##0xfe1082c0)
│      │    0xfe102034      │   R3 = memw(R1+##0xfe1082f4)
│      │┌─< 0xfe102038      └   if (cmp.eq(R3.new,#0x0)) jump:nt 0xfe102050
│      ││   0xfe10203c      [   R3 = add(R3,R1)
│      ││   0xfe102040      [   R3 = memw(R3+R2<<#0x2)
│      ││   0xfe102044      [   R3 = add(R3,R1)
│     ┌───< 0xfe102048      ┌   jump 0xfe102054
│     │││   0xfe10204c      └   R0 = memw(R3+R0<<#0x2)
│     │└└─> 0xfe102050      [   R0 = ##0xfffffbad
└     └───> 0xfe102054      [   jumpr LR
```

Assuming you have no decompiler it will take a short while, until you figured out
what it does.

An LLM will, possibly in a shorter time, provide you a few observations about it:
- It does up to two bounds checks on the input value in `R1:0`.
- It accesses a multi-level lookup table and returns data from it.
- Due to the bounds check there are around ~60 and ~170 entries in the table to retrieve.
  Possibly two kind of versions for whatever is stored in there (one for each interval).
- From the `segment.modem.b02` flag it might infer that it is a
  system/HVM/interrupt call lookup or a constant/state getter function (at least it this in my experiment).

If the researcher is inexperienced, these points can already be helpful to look for further evidence to
reject or accept these hypothesis.
But it would be even better if the agent could test the hypothesis on its own.

An obvious solution is of course to provide the agent a MCP server to run commands in Rizin.
It could disassemble the locations where the function is called and track
the returned value.

But this has disadvantages:
- The LLM has to keep potentially a lot of text in its context.
  The more often the function is used, the faster the context grows.
- LLMs will always, by design even, hallucinate.
  This likelihood will go up, if the input is not strongly represented in its training data (niche assembly might be such a case).
  Combined with an arbitrary context growth rate, the probability for degrading output will go up.
- To prevent overarching context usage, LLM agents could dispatch individual tasks to other agents.
  But with each passing of messages to another agent mistakes in communication can
  quite literally happen.

It is like letting an LLM build a math proof.
It maybe is sufficient to let it run on its own, but it is almost certain it
will be faster and more precise if it is given a formal proof checker.
In cooperation it will also be better to give an agent a piece of code it can run to _obtain_ `X`,
instead of a lengthy explanation how to _calculate_ `X`.

So, instead of letting the LLM do it's reasoning on simple text alone,
we can expect to get better results if it could formally verify hypothesis or evidence.

It could query a tainting algorithm, checking if the function's return value
is used in an indirect call.
If it finds such a case, it supports the system/HVM call hypothesis.
If it doesn't it can check the alternative hypothesis again.

Additionally, it could dispatch a job to another agent.
Asking it to find vulnerabilities in this function.
That agent could query for the exact semantics of `P0 = boundscheck(R1:0,R3:2):raw:lo`,
learning that the bounds check is `R0 in (0xad,0x00]` instead of `R1:0 in [0xad,0x00]`.
It might also spot the fact that `R1` is added to the table address but is never bounds checked.
A potential read or write primitive, if `R0` were user-controlled.

Instead of answering with a vague informal explanation of this finding,
the agent could store the formal prove simply in the knowledge base,
and only return the proof id and a short node that controlling `R0`
could lead to an out of bounds read or write.

Any agent who has access to the knowledge base, can later query the knowledge base again
to make use of that fact.

---

We can be almost certain that building formal tools for agents will yield
results. Because it was already done before.
LLM coding agents are relatively good because they can, at any time,
test their generated text against a formally correct machine (a compiler, an interpreter, a test suite).

But reverse engineering tools rarely provide such _formalized_ and _rule based_ access and results.
{{<footnote "A notable exception is BAPs reasoning system Saluki: https://github.com/BinaryAnalysisPlatform/bap-plugins/tree/master/saluki">}}
Note that an MCP server for command access is not sufficient!
The value is added by reducing the complexity of the task for the agent.
And by storing results in an information theoretical dense form (formalized, instead of natural language).

What we need to achieve is to have a sufficiently powerful query language, knowledge base, and inference system
to express hypothesis and proofs. Accessibly for LLMs and humans alike.

Then an LLM can rely on short queries and their formally correct truthiness,
instead on endless generated walls of text.
Humans on the other hand can add the bits and pieces agents cannot yet comprehend.

## Knowledge Engineering & Expert Systems

As many of you could already guess, the problem is essentially what many
call knowledge engineering, expert systems {{< citation "ExpertSystem2026" >}},
or semantic reasoning systems {{< citation "SemanticReasoner2026" >}}.

These topics started to become very prominent in the 80s onward
and are now common techniques in many fields.
SAT-solvers, software supporting strategic decisions in business or the military,
or simply AI in games are all examples of that.

Of course, in the field of knowledge engineering the hot topic currently are LLMs.

### A word about AI/LLMs

Current reports show that LLMs seem very much capable of finding unintended behavior in source code.
While no one can know how much better the models will become,
we can assume that there will be improvement.

Our effort here doesn't seek to replace LLMs.
Quite the opposite, even, as you can see in the example above.

LLMs and, in effect, neural networks are a subset of knowledge engineering.
We believe that neural networks will play a big role in the reverse engineering field, given
the technology's ability to recognize requested patterns in complex data.

We do believe, though, that neural networks shouldn't be seen as the
"ultimate solution for everything."

The targeted problem here is of a broadly knowledge-engineering nature,
not (just) a question of how to connect neural networks together.

## Why Rizin

# The Philosophical Problem - The Gap in Computing Reasoning

Most of the tools we use in reverse engineering (RE) of computer programs are from the 2000s and earlier.
Maybe they were implemented more recently, but the algorithms and ideas are relatively
(for computer science time spans) old.
The basics were always the same: a disassembler, some flow and type analysis, optionally
a decompiler and a debugger. And besides the note book and the mind of the researcher,
that is essentially it.

But gradually restoring knowledge over the software at hand is more than just applying tools.
The experience of the researcher, their systematic way of detangleing the information
and making sense of it is the core of the work.\
_So why is it that we don't have more tools which mimic it?_

The tools and algorithms we use do automation. They are of deductive nature,
computing from one set of known facts another set of facts.
They _necessarily_ need all premises to be true so their conclusions are true as well.
That is the case for a disassembler, which outputs garbage
if the wrong bytes are fed in, and good output for a correct ones.
It is also true for basic control flow analysis: if a `jump 0x7000` instruction is located in an `r-x` map,
it is pretty much guaranteed to jump to `0x7000` when executed.

While these deductive methods are a center piece in RE they are unable to provide results,
if one or more premises are unknown or uncertain but needed for a conclusion.
If our `jump 0x7000` is located in an `rwx` map, suddenly we can't be sure anymore that it actually jumps to that address.
Because at the time of execution the instruction could have been overwritten.
There simply is no guaranteed clear answer our premises infer.
We simply miss the premise "does anything write to the location where `jump 0x7000` is?".
And with it our deductive algorithm fails.

Of course, that doesn't stop us from inferring valid conclusions.
The process of inferring just gets more fuzzy from here on.

Researchers work with hypotheses, educated guesses, or choose the most likely result from their point of view.
That kind of reasoning is called abduction. Abduction no longer makes it _necessary_ that inferring
from true premises leads to a true conclusion.\
If we give evidence `E` and hypothesis `H1, H2, ..., Hn` to an abductive reasoning process,
it chooses hypothesis `Hi` which best explains `E`, or which seems closest to the truth.
{{<citation douvenAbduction2025 >}}
{{< footnote "The exact definition is part of the philosophical debate." >}}
This already helps to argue with a lack of information, but these methods also have problems.\
How do we know if `Hi` is the _best_ explanation for `E`?
What does _best_ even mean?\
And even if we somehow know that, what happens if all our hypothesis we have are a bad lot? {{<citation douvenAbduction2025 >}}\
It is easy to imagine a novice researcher looking at some evidence and struggling to come up with any useful hypothesis.
Simply because she misses experience.

And lastly, there are the inductive reasoning methods.
These are usually understood as being of enumerative or cumulative nature.
The more observations we collect as evidence, the more
likely our hypothesis is true or false. {{< footnote "Of course this doesn't exclude the case where a single observation can flip the whole conclusion. If we observe 1 million white swans, there is a high probability that all swans are white. Of course only until we observed single black swan." >}}
{{< citation eagleProbabilityInductiveLogic2025 "Chapter 1.5">}}\
There are a few (often statistical) algorithms doing that.
Think of Value Set Analysis (VSA): {{<citation balakrishnanAnalyzingMemoryAccesses2004>}}
with every new observation the possible values of a memory location get more or less certain.\
In daily life we would simply call this reasoning "generalization from observations and experiences".
And of course inductive methods suffer from the same problem as generalization does:
It is simply not obvious which observations can count as evidence and which not. {{< citation eagleProbabilityInductiveLogic2025 "Chapter 1.5">}} {{<citation wikipediaRavenParadox2026>}}

I think we can all see how these modes of reasoning are applied in RE.
And sketching them out so explicitly it seems obvious that _most_ reasoning in RE is
either abductive or inductive.
Sure, it happens in the head and notebook of the researcher. But they are nontheless dominant!

As researchers we struggle by definition with the lack of information we are trying to regain.
And uncertain results can be exceptionally useful if the alternatives are
no results at all or if they are computationally unfeasible.
So we trade uncertainty for answers. We can grasp the bigger picture by letting go of
the strictness of deduction. Fuzzy reasoning can provide clarity and orientation in uncertainty.

And yet, here we are. The tools nowadays don't allow us to encode our experience in code.
It is too fuzzy.
They don't allow us to share intuition in a way that it is reusable by others.
The scripts and plugins almost always handle the specific case, not the general one we have in our head.
And by sharing the special case we share the deductive rules - which fail the moment a premise like a hardcoded offset changes.
We can't share the general case from our head so easily.
Because sharing it means we have to write it down in computable form.

_So why can't we make our fuzzy intuition and experience computable?_

The answer is quite simple in our understanding:
There are simply not that many tools which allow it.
Most tools are not built to work with answers like "maybe", "possibly", and "a likelihood of p".
Not many algorithms use sampling and statistics. 
And even the ones which do rarely make the uncertainty transparent.
Let alone letting the researcher control the thresholds of it.

A huge part of RE is abductive and inductive, but there is no way to express it _computationally_ so it can be shared.
The non-deductive reasoning stays in the researcher's head.
For the lack of a method to preserve it.

Having a fuzzy understanding of the patterns in a binary, even if wrong in the details, brings value.
That intuition is a map helping with the exploration, finding the places one wants to look with the magnifying glass.

Expressing intuition in computable form enables us to share it.
Sharing our intuition means sharing our maps, so others can use them to find the places
quicker to use their magnifying glass.

With this open research effort, we would like to change that.

---

For a more fundamental philosophical discussion of this process, see the [Epistemology](/research/epistemology) page.

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

- Technology as a way to project power. Power for whom?
- Everything here is done in the open.
- Results fully open source.
- Possible consequences if research succeeds and solutions work as intended.
- What could be unknown unknowns?

### Human<>Machine Interaction

- Whatever the final implementation looks like, it absolutely **must be** intuitive to use.
  Interaction cannot mean felt friction. Because every piece of mental work going
  into figuring out how the tool can do X, is not spent on the actual research problem of the user.
- Semantic search. Type text, get objects served for that category.
- Multiple representations of knowledge.
  - the language, names, graphs, heat maps, plots, diagrams, highlights in assembly, what else?

### Core implementations

- Implementing a knowledge base (KB), a _knowledge representation storage_, for:
  all observed facts, detected patterns, and inference rules.
  (Rizin's KB issue: https://github.com/rizinorg/rizin/issues/5213)
  - How is it represented: table-like (row-, column-oriented), graph, hypergraph?
  - What implementations already exist? Are they maintained and somewhat production ready (very important category)?
    What pros and cons do they have? For what reasoning structures do they allow (predicate logic, probabilistic, modality)?
- Implementing (or forking) a language for defining facts, probabilistic rules,
  and inductions from other facts and rules.
  - Provide implementation of common reasoning structures (Bayes, factor graphs, Baconian Probability, Stability Theory of Belief, Markov chains, predicate logic, algebra, analysis?...)
  - Can the language be extended if needed?
- **Performance**: Implementations must be reasonably gentle with resources and
  be designed to allow for improvements.

### Specific use case solutions

- Rule-based classification defined by our language
  - Tail calls
  - Type inference from sampled observations
- User-defined point of view, [Modality](https://en.wikipedia.org/wiki/Modality_%28semantics%29) (what if X would be Y?).
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
- BAP has several classification plugins like the one classifying functions into no, static only, arbitrary memory write (https://github.com/BinaryAnalysisPlatform/bap-plugins/tree/master/staticstore).

## Open Research Procedures

Define the problem space, the solution space, and add subcategories for the research we review here.

- High-level research strategy
- Fundamental Concepts
  - Representing knowledge - Applicable questions of ontology and epistemology
    - Reasoning
  - Fuzzy pattern matching
    - Neural networks
      - Reinforcement learning
  - Fuzzy logic
  - Probabilistic reasoning
    - Epistemic logic
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
  - Specifically the [research channel](https://im.rizin.re/rizinorg/channels/research)

### Tasks

- How can a user interface and interaction look like? How can it be made as frictionless as possible?
- Search for and add articles to the reading list; very briefly point out why they are of interest.
- Correct mistakes, point out errors, report unclear explanations.
- Sort papers and classify them by importance.
- Read a paper and write a blog-post-like summary that condenses the idea and
  interprets it with respect to software reverse engineering.
- Write an overview page about one of the bullet points above.

I hope it is needless to say, but:
No AI allowed at this stage.
Understanding is the target, not generating.
Especially, because we don't know yet, if LLMs have a semantic understanding of the things they generate.

## Related work

- Binary Ninja supports experimental [semantic search](https://docs.sidekick.binary.ninja/guide/semantic_indexing.html).
  The search term can be a vague description ("TLS handshake"); then matching functions
  are returned. The user has control over the similarity (uncertainty) level of the matching.

---

{{< citation_list "References" >}}

---

{{< footnote_list "Footnotes" >}}
