---
title: Hello
---

# Hoisting Applications into Rust and Validating Safe Translation</h2>



## { .list .institutions }
* [{{< image "/imgs/uw-logo.png" "University of Washington" >}}](https://www.cs.washington.edu)
* [{{< image "/imgs/better-bytes-logo.svg" "Better Bytes" >}}](https://betterbytes.org)

## { .list .pis }
* Tom Anderson
* Gilbert Bernstein
* Michael Ernst
* Baris Kasikci
* Amit Levy

For decades, C has been the primary language for writing
resource-sensitive and high-performance programs due to the low-level
and fine-grained control it provides over memory allocation, data
layout, instruction ordering, and concurrency. This has made it the de
facto language for interacting with hardware in operating systems and
embedded systems, for interacting with network and storage, for
high-performance parallel computing, and for military
applications. Much of this code is security critical, and the lack of
support for memory safety leads directly to frequent, avoidable
security vulnerabilities. For example, in our study [86] of severe
security vulnerabilities in released Linux code from 2010-2020, we
found over 600 separate flaws that could have been prevented through
the use of better programming languages like Rust.

Our society has a huge overhang of technical debt: billions of lines
of memory- and concurrency-unsafe C code in active use in
security-critical settings. Rewriting has proven successful in some
large-scale deployed systems: Internet systems (Dropbox) and clients
(Firefox), package managers (NPM), language runtimes (Deno,
WebAssembly), operating systems (Windows, Firecracker VMM, Tock); and
several companies have reported on significant reduction in memory
vulnerabilities in programs written in Rust (Google, Microsoft).
However, a crash program to rewrite all C code in a memory-safe manner
using existing tools would take tens of thousands of programmers
working full-time for decades. And because humans are fallible, those
programmers might well introduce new bugs in the process. Translating
concurrent programs---inherent in many of the use cases for C---adds
an extra level of complexity.

A mass code translation effort would need to develop novel program
analysis tools to guide the translation, techniques to infer
programmer intent, coding standards so that the produced code is
maintainable, and a feedback loop that learns from experience to
incrementally remove error from the translation process.

HARVEST follows this roadmap. Our goal is to automate C to Rust code
translation, by following the procedure that a human manager would use
to guide an army of programmers in performing this task. We take each
step of that procedure — symbolic execution on sample input data,
static and dynamic program analysis, introduction of higher-level
abstractions, code generation, etc. — as the target for a tool that
could be used by an army of humans and then automated to act as if it
was used by an army of humans.

The HARVEST approach can be succinctly characterized as hybrid static/dynamic program modeling,
translation, and validation with machine learning assistance. Modeling reverse-engineers ("lifts") C
source code to a higher-level intermediate representation called HARVEST-IR (pronounced “harvester”).
HARVEST-IR contains both the program source code and its abstract syntax tree (AST). It is more abstract
than C in that it uses collection operations instead of loops and pointer chasing, collects related data and func-
tions into classes, removes convenience pointers, represents error handling, and conveys variable lifetimes and
other analysis information. Various passes over HARVEST-IR add analysis and transform the result, using
static analysis, dynamic analysis, and machine learning (NLP, LLMs, etc.). Machine learning is integrated
throughout our process — not as an unreliable main performer (since each of the tasks is well beyond current
capabilities) but as an assistant synthesizing and improving the quality of the information used to guide
decisions. By capturing the statistical likelihood of code patterns, machine learning disambiguates underdeter-
mined choices in favor of more idiomatic translations. Translated Rust programs are scalably validated by a
combination of symbolic execution and dynamic analyses. If the translated Rust program is not correct (safe,
idiomatic, and equivalent to the original C program), the byproducts of the validation step are fed back into the
lifting and test-generation steps to start the HARVEST flow again, until a correct Rust program is produced.

We have built several analysis tools that can serve as the basis of
HARVEST-IR, and the team has a deep level of experience translating C
to Rust for low level systems code. To avoid overfitting, a key step
in our approach is applying our tools against a large body of open
source C code drawn from embedded systems and other domains, down
filtered by milestone. We also plan to open-source our tools and the
HARVEST-IR to help catalyze additional contributions from the program
analysis research community.
