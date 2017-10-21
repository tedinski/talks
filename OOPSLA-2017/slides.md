---
title:    Reliable and Automatic Composition of Language Extensions to C
subtitle: "Making language extension practical"
author:   Ted Kaminski, Lucas Kramer, Travis Carlson, and Eric Van Wyk \newline (University of Minnesota)
date:     October 2017
fontsize: 12pt
classoption: aspectratio=169
---

## Language extension research program goals

Expression problem\footnote{Wadler} criteria, as applied to AST:

1. Introduce both new syntax and new analysis
2. Static checking of this representation
3. Without modifying the original code
4. Separate compilation

Independent extensibility\footnote{Zenger and Odersky}:

5. "Unordered," *composable* extensions

Our criteria:

6. Automatic composition, no glue code

## Example

## Distinguishing characteristics

Many approaches to extensible languages.

What distinguishes this work is how two questions are answered:

1. Who composes the language features?
2. How expressive are the supported features?

## Success!

- Copper & Silver: tools for creating extensible compilers
- AbleC: our application to C

## Pipeline figure

## Previous work

**Forwarding:**
:   manage the expression problem

**Modular determinism analysis:**
:   no unexpected syntactic conflicts

**Modular well-definedness analysis:**
:   well-defined attribute grammar

Concurrent work

**Coherent non-interference:**
:   extensions behave as specified

## This talk

- Modular analyses impose restrictions
- Previous work, let's skip over that

\vspace{1pt}

- What kinds of extensions can we build?
- What kinds of extensions can't we build for plain C?
- What host language modifications allow for more kinds of extensions?

## The example again

## More extensions

- Halide
- Sqlite
- Tensor/matrix/vector
- Go concurrency
- Matlab FFI
- Templates

## Effects of restrictions

- Examples of what's not possible
- Restrictions are host-language relative
- Spin this as positive! Clear line between extension and change.
- Motivates some changes to C host

## Enabling more extensions

- GCC extensions
- Operator overloading
- Lifting declarations
- Type qualifiers

## Summary

- Extended notion of expression problem
- Reliable composition
- Breadth of extensions possible
- Language extension impacts language design
    - We can get experience with extensions before standardization
    - Clear line between extension and modification
- Spell out: good for language designers, good for programmers

## Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)

