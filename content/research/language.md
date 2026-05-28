---
author: "Rot127"
title: "Language"
layout: "single"
url: "/research/language"
summary: "research"
tags: ["rizin", "research", "ai", "binary analysis", "reverse engineering"]

ShowToc: true
TocOpen: true
---

# Language

## Requirements

- We shouldn't invent it on our own: eats too many resources, adds maintenance burden.
- It shouldn't be too difficult to port existing recipes from BAP or Binja's BNQL.
- Query language should be both human-readable and machine-readable.
  - Clear (not too bloated) syntax.

## Language/Language Engine candidates to use

### [Wirelog](https://github.com/semantic-reasoning/wirelog/) - C implementation of a Datalog engine.
  - Currently active developed.
  - Part of the startup [Cleverplant](https://cleverplant.com).
  - Requires signing of [commercial](https://github.com/semantic-reasoning/wirelog/blob/main/CLA.md) license for contributions.
