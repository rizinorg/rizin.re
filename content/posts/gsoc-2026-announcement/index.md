---
author: "xvilka"
title: "Google Summer of Code 2026 Announcement"
date: "2026-05-07"
summary: "An announcement of the Google Summer of Code 2026. Four accepted candidates."
tags: ["rizin", "gsoc"]
ShowToc: true
TocOpen: false
weight: 2
---

We're happy to announce that Rizin is back as a participating organization in [Google Summer of Code 2026](https://rizin.re/gsoc/2026). A huge thank you goes out to every single person who submitted an application this year—the volume of submissions speaks volumes about the growing enthusiasm around what we're building. To everyone who applied: best of luck with whatever comes next on your journey. Google also deserves recognition for making programs like this possible, giving open source projects like ours a chance to meet talented developers we might otherwise never cross paths with. Looking back at previous editions of GSoC and RSoC, it's been incredibly rewarding to watch so many participants stick around well beyond their program—some have grown into core contributors, others have stepped up as mentors themselves. Here's hoping the 2026 cohort follows in those footsteps.

This time we selected 4 projects, from improving static analysis, to diffing, to Frida-based dynamic analysis.

## Alok Kumar Mishra

Hello, I am Alok (aka [IndAlok](https://github.com/IndAlok)). I am an Electronics and Communication Engineering undergraduate at the Indian Institute of Information Technology Jabalpur, India. I became interested in reverse engineering through CTFs and low-level security challenges. That is also how I started using Rizin and Cutter more often.
Rizin fit naturally into my CTF workflow from the CLI. I could inspect functions, strings, references, and analysis output, then script small workflows around them. Cutter complemented that with a clearer visual view of control flow, xrefs, and types.

One project that brought me much closer to the Rizin ecosystem was [RzWeb](https://github.com/IndAlok/rzweb), a browser-based Rizin/WebAssembly RE project. It started from a practical CTF need: I wanted a Rizin environment that could run in the browser and reduce setup friction when quickly inspecting binaries.

Working on it pushed me beyond user commands. I had to think about browser-compatible I/O, frontend integration, the boundary between Rizin as an analysis engine and the interface around it. That experience made me much more interested in exploring and contributing to the project.

### Project

Originally, I was selected for the [classes-analysis project](https://rizin.re/gsoc/2026/#classes-analysis-for-cobjectivecswiftdlangjava-350-hour-project). It focuses on improving object-oriented language analysis in Rizin. After discussing the exact scope with my mentors, we decided to approach this from the dynamic-analysis side as well. The project will integrate **Frida** into Rizin and Cutter, then use it to recover class information from running programs.

The goal of this summer is to make Frida's runtime instrumentation available from inside the Rizin/Cutter workflow. Class information observed in a running target should connect back to Rizin's analysis model instead of staying isolated in separate scripts and terminals.

The implementation will focus on a **Rizin backend first**. It will cover Frida target connection, attach/spawn/launch workflows, script execution, and runtime information. It will also include memory access where it is reliable, events, and a practical debugging/tracing foundation. Cutter should expose the same backend through a small UI. The Frida logic should remain in Rizin.

For **Android Java/Kotlin** targets, Frida's runtime APIs can expose information that is difficult to recover statically. This includes loaded classes, class-loader context, methods, fields, inheritance, and related metadata. I aim to make that information available to Rizin in a structured form. Rizin analysis and Cutter views can then reuse it as part of the broader class-analysis workflow.

I have also been exploring [r2frida](https://github.com/nowsecure/r2frida), which is an important reference for this project. It shows how an RE shell can drive Frida through a native plugin and injected agent. For Rizin, the integration is expected to be native from the beginning: structured command output, explicit lifecycle handling, class-loader-aware recovery, Rizin class/type imports, Cutter integration, and tests around the core workflows.

### Past contributions

I started contributing to Rizin in late 2025 with minor fixes and gradually moved on ahead. Some of my contributions include:

- **November-December 2025:** hardened ELF version-symbol parsing in [rizin#5531](https://github.com/rizinorg/rizin/pull/5531), added a De Bruijn helper in [rizin#5532](https://github.com/rizinorg/rizin/pull/5532), and implemented a unified address description API in [rizin#5571](https://github.com/rizinorg/rizin/pull/5571). Alongwith few documentation changes, which made address names and offsets easier to represent consistently across Rizin.
- **February-March 2026:** added multi-path support to `rz-find` in [rizin#5875](https://github.com/rizinorg/rizin/pull/5875), implemented `rz-ar` archive extraction in [rizin#6036](https://github.com/rizinorg/rizin/pull/6036), fixed an escaped-string endless loop in [rizin#6049](https://github.com/rizinorg/rizin/pull/6049), and added enum immediate hints with `ahie` in [rizin#6052](https://github.com/rizinorg/rizin/pull/6052). This improved how enum-backed immediate values can be understood during analysis.
- **Rizin ecosystem work:** improved Rizin Notebook in [rizin-notebook#13](https://github.com/rizinorg/rizin-notebook/pull/13), including notebook workflow, binary attach/fetch behavior, frontend polish, safer rendering, and Rizin/Cutter integration paths. I also helped migrate `rz-silhouette` and `rz-silhouette-server` from Protocol Buffers to Cap'n Proto through [rz-silhouette#15](https://github.com/rizinorg/rz-silhouette/pull/15) and [rz-silhouette-server#4](https://github.com/rizinorg/rz-silhouette-server/pull/4).
- **April 2026:** fixed shared table query filtering in [rizin#6166](https://github.com/rizinorg/rizin/pull/6166), improved Windows console VT mode handling in [rizin#6169](https://github.com/rizinorg/rizin/pull/6169), and addressed memory leaks, overflow cases, and allocation-safety issues in [rizin#6217](https://github.com/rizinorg/rizin/pull/6217), [rizin#6218](https://github.com/rizinorg/rizin/pull/6218), [rizin#6219](https://github.com/rizinorg/rizin/pull/6219), [rizin#6220](https://github.com/rizinorg/rizin/pull/6220), [rizin#6221](https://github.com/rizinorg/rizin/pull/6221), [rizin#6222](https://github.com/rizinorg/rizin/pull/6222), and [rizin#6232](https://github.com/rizinorg/rizin/pull/6232).

Together, these contributions helped me get familiar with Rizin's C APIs, command behavior, tests, review process, Windows-specific issues, and the wider Rizin/Cutter ecosystem.

### Looking ahead

By the end of the program, the project aims to provide Rizin and Cutter with a practical foundation for **Frida-backed dynamic class analysis**. The goal is to connect runtime information from instrumented programs with Rizin's existing analysis and type systems.

I am excited to contribute to Rizin and help improve its RE workflows for users who need both static analysis and live runtime insight.

## Darshan Patel

Hey, I am Darshan Patel ([MrQuantum1915](https://github.com/MrQuantum1915)).  I am Computer Science Undergrad student at Indian Institute of Information Technology Vadodara, India. This summer I'll be building a Function Prelude Detection Plugin for Rizin as a GSoC 2026 contributor. A statistics tool to augment Rizin's current , hardcoded architecture specific signature database, with a pipeline that learns prelude patterns directly from the target binary.

Systems programming and open source were always the two things I wanted to work on seriously, because they get to the heart of how all the computing devices work and let me contribute to a real world software. I had prior exposure to security through CTFs and some random curious readings. But my deep dive into reverse engineering truly began when I started contributing to Rizin's codebase. The deeper I got (learning what constructs a valid ROP chain for attack, reading Intel's developer manual for correct instruction behavior, working on fixing x86 Zydis migration), the more I realized how much precision and low-level understanding this domain demands. 

My recent coursework in computer architecture and compiler design gave the right vocabulary; Rizin gave the problems worth applying the knowledge to.

My introduction to Rizin came through [Tushar Jain](https://github.com/tushar3q34), a senior from my JEE preparation days and a GSoC '25 Rizin contributor. His work and guidance made it clear this was the right org.

### My Project and Goals

Rizin's `aap` (Analyze function preludes) phase currently relies on a hardcoded database of architecture specific signatures. The problem with this approach is that it has to be written for every architecture and they also vary by compiler. They are hard to maintain and update and go stale fast. It also gives massive false positives for x86 which resulted in `aap` being entirely disabled for x86/x86_64 in the current codebase.

The plugin I'm building augments this with a multi stage statistical pipeline.  It fetches known function entry points from binary's symbol information. Then builds a Weighted Prefix Trie on the byte sequences (high traffic paths = fixed mask, heavy branching nodes = wildcard). Then mask generation phase (Mode-Frequency ratio + Shannon Entropy) to decide, at each branching trie node, whether to emit a fixed opcode, wildcard, or split into multiple distinct signatures. The generated signature then pass through a verification phase to kill false positives.

### Past Contributions

Interestingly all my contributions so far, trace back to a single effort of fixing inconsistency in the ROP search engine [#5491](https://github.com/rizinorg/rizin/issues/5491)! 

Fixing one thing kept exposing the next. That chain of issues produced the bulk of my work:

- [Fix mips analysis for 64bit + NanoMIPS](https://github.com/rizinorg/rizin/pull/6122)
- [Refactor ROP search for supporting JOP/COP separately + Fixed crashing bugs and Performance of search by using PVector instead of List](https://github.com/rizinorg/rizin/pull/6130)
- [Fix inconsistency in `/R` and `/Rg` + Fixed ROP search for delay slot arch](https://github.com/rizinorg/rizin/pull/6108)
- some PRs fixing memory leaks :  [#6278](https://github.com/rizinorg/rizin/pull/6278), [#6281](https://github.com/rizinorg/rizin/pull/6281)
- [Fix invalid 32 bit stack change output](https://github.com/rizinorg/rizin/pull/6242)

Currently working on:
- [Fix mistakes in capstone to zydis migration for x86](https://github.com/rizinorg/rizin/pull/6238)
- [Add JOP/COP support](https://github.com/rizinorg/rizin/pull/6257)
- [Fixing use of RZ_ANALYSIS_STACK_[INC|DEC] enum for stackop and stackptr in analysis of many architectures](https://github.com/rizinorg/rizin/pull/6260)

## Ron Stephen Mathew

Hi, I am Ron Stephen Mathew, a Computer Science and Engineering undergraduate at the Indian Institute of Information Technology Kottayam. I am grateful to have been selected for GSoC 2026 under the Rizin Organization, where I will be working on implementing **Diffing Mode in Cutter**.

### Project

For Google Summer of Code 2026, I will be working on implementing `rz-diff`-related features and a full diffing mode in Cutter. The project will be divided into two, and possibly three, major parts:

#### 1. Supporting Multiple Files and Core Diffing Infrastructure

Currently, Cutter supports opening only a single file at a time. The first part of this project involves enabling the ability to open an additional file for diffing against the currently loaded binary.

This will be achieved by introducing a new **diffing core**. When a user selects *File → Diff File*, a second binary will be loaded into this diffing core.

Once loaded, users will be able to toggle diff views within individual memory widgets such as Disassembly, Hex, and later Pseudocode. This will be implemented via a toggle button in the widget’s title bar. When enabled, the widget will display `rz-diff` output instead of the standard binary data.

Initially, diffing support will be added to:

* Disassembly widget
* Hexadecimal widget

To support this, APIs from `rz-diff.h` will be exposed within Cutter. With the introduction of the diffing core (likely as an additional `RzCoreFile` instance within CutterCore), existing data-fetching APIs will be extended. For example:

* `disassembleLines` → `disassembleDiffLines`

Additional diffing functionalities such as distance calculations and file-type-based diffing will be implemented in a separate widget. Relevant APIs such as:

* `rz_diff_myers_distance`
* `rz_diff_levenshtein_distance`

will also be exposed through the Cutter core.


#### 2. Diffing Modes for Graph and Pseudocode Views

Diffing support will be extended to the Graph view using a similar approach. When diff mode is enabled, users will be able to visualize control flow graphs of the same function from both binaries side by side, similar to tools like BinDiff.

Since the RzIL widget is still under development (PR #6255), pseudocode diffing will be implemented once that work is complete.


#### 3. Diffing Configuration Options (Optional)

An optional third phase involves introducing a dedicated preferences page for diffing. This will allow users to configure:

* Diffing algorithms
* Highlighting options
* Other visualization settings

### Past Contributions
I have been contributing to the Rizin project over the past few months, primarily focusing on improving user experience in Cutter. My contributions include:

1. Adding a unified interface for symbol servers ([#3584](https://github.com/rizinorg/cutter/pull/3584))
2. Making docking of widgets non-mandatory ([#3578](https://github.com/rizinorg/cutter/pull/3578))
3. Adding filtering and sorting features for the Xref widget ([#3579](https://github.com/rizinorg/cutter/pull/3579))
4. Working on the RzIL widget API and its implementation in Rizin ([#6255](https://github.com/rizinorg/rizin/pull/6255))

## Naren Sirigere

Hi! I am Naren Sirigere ([historicattle](https://github.com/historicattle/)), a sophomore at IIIT Gwalior. This summer I will be working on improving and implementing classes analysis for various OOP languages.

### Project
The core of my project involves enhancing Rizin’s ability to reconstruct object oriented structures across five major OOP languages.

#### 1. Analysis for Rust
- Extract vtable information by identifying fat pointers in Rust binaries
- Link methods with their corresponding Traits
- Handle `dyn Trait`, `Box<dyn Trait>`, and `fn trait` dispatch patterns

#### 2. Analysis for Java
- Extract vtable and itable information by simulating the JVM's class loading logic 
- Extract class hierarchy from bytecode

#### 3. Analysis of Objective C
- Complete the incomplete inheritance and devirtualization implemtation [#5415](https://github.com/rizinorg/rizin/pull/5415)

#### 4. Analysis of Swift
- Implement Protocol Witness Table dispatch
- Implement table dispatch by reusing Objective-C's message dispatch logic

#### 5. Analysis of D
- Write the D demangler into `rz-libdemangle`
- Extract vtable information from D binaries
- Extract class hierarchy from D binaries

#### 6. Windows Structured Exception Handling (SEH)
- In the x86 architecture, SEH is stack-based, where a linked list of registration records is stored on the stack, making it dynamic and difficult to track.
- x64 SEH is table-based, the exception handling information is stored in the `.pdata` section of the PE file, allowing for lookups without stack overhead.
- Complete the incomplete implementation for x64 SEH in [#2854](https://github.com/rizinorg/rizin/pull/2854/)

### Optional Goals
If the primary goals are completed and time permits, the following features will also be implemented:

- Implement vtable detection and devirtualization for FreePascal binaries
- Implement COM, C++, Objective-C Interfaces for D
- Implement error handling analysis for Swift, Rust, and Java

### Past Contributions

My contributions to Rizin began in late December 2025. 
Listed below in chronological order:

* Extend support for python 3.14 ([\#5662](https://github.com/rizinorg/rizin/pull/5662), [rizin-testbins\#224](https://github.com/rizinorg/rizin-testbins/pull/224))
* Add support for extended 6502 opcodes ([\#5748](https://github.com/rizinorg/rizin/pull/5748))
* Make 6502 magic constant configurable ([\#5817](https://github.com/rizinorg/rizin/pull/5817)) 
* Update PCRE2 to v10.47 ([\#5830](https://github.com/rizinorg/rizin/pull/5830)) 
* Support SerenityOS ([\#5905](https://github.com/rizinorg/rizin/pull/5905), [SerenityOS/serenity\#26589](https://github.com/SerenityOS/serenity/pull/26589), [SerenityOS/serenity\#26583](https://github.com/SerenityOS/serenity/pull/26583)) 
* Fix M680X aliases ([\#5939](https://github.com/rizinorg/rizin/pull/5939))
* Extend M680X support to RS08 ([capstone-engine/capstone\#2872](https://github.com/capstone-engine/capstone/pull/2872), [\#5977](https://github.com/rizinorg/rizin/pull/5977))
