---
author: "Rot127"
title: "Evidence"
layout: "blog"
tags: ["rizin", "research", "epistemology", "evidence"]

ShowToc: true
TocOpen: false
---

# Evidence

- Pure evidence = "Pure evidence may support one hypothesis but say nothing at all about other hypotheses."
- Mixed evidence = "Mixed evidence has some degree of probability under every hypothesis we are considering" {{ footnote KnowledgeEngineeringBuilding2016 "Chapter 1.3.3" }}

- Evidential Completeness = Evidence should sufficiently cover all relevant questions of H.
Otherwise it is possible to collect evidence for H, calculate the probability of H being true and come to a certain conclusion.
But if the evidence collected was only answering some questions for H, but didn't answer many others also relevant for H,
the evidence doesn't give a complete picture.

How do we know that we collected enough evidence to answer H?
How do we know if there are questions left unanswered, but relevant for H?

-> Whatever the answer might be, we should show the uncertainty to the user if known.

Assigning probability as hard numbers to evidence doesn't make sense if the evidence was only collected once.
Because where does the number come from?
If we have X samples and evidence E was seen Y times in it, E's probability of occurrence in the samples is Y/X.
But for evidence with very few samples, this doesn't make sense.

But still, people have a feeling for what is probable.
They should be able to express their feeling in natural language, and we assign ranges of
probabilities to these natural terms.

E.g.:
Certain: 0.98-1.0
Very, very likely: 0.90-0.98
Very likely: 0.80-0.90
likely: 0.70-0.80
etc.

That is in effect Fuzzy probability and logic


----

{{< citation_list "Citations" >}}

----

{{< footnote_list "Footnotes" >}}
