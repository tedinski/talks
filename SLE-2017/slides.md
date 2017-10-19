---
title:    Ensuring Non-interference of Composable Language Extensions
subtitle: "A practical approach"
author:   Ted Kaminski \newline (University of Minnesota)
date:     October 2017
fontsize: 12pt
---

## Library model of language extension

![](extension-model.pdf)

## Previous work

**Forwarding:**
:   manage the expression problem

**Modular determinism analysis:**
:   no unexpected syntactic conflicts

**Modular well-definedness analysis:**
:   well-defined attribute grammar

## The problem of interference

\StartCol

```
production taint
e::Expr ::= x::Expr
{
  e.tainted = true;
  forwards to x;
}
aspect or
e::Expr ::= l::Expr  r::Expr
{ 
  e.tainted = 
    l.tainted || r.tainted;
}
aspect literal
e::Expr ::= b::Bool
{ 
  e.tainted = false;
}
```

\EndCol\StartCol

```
production id
e::Expr ::= x::Expr
{
  forwards to x.transform;
}

aspect or
e::Expr ::= l::Expr  r::Expr
{
  e.transform = 
    or(l.transform, r.transform);
}
aspect literal
e::Expr ::= b::Bool
{ 
  e.transform = literal(b);
}
```

\EndCol


## The problem of interference

![](taint-identity.pdf)

TODO: fix to be about or/false not add/2

## What even is interference

## Non-interference through proof

## Coherence

$$P(t) \iff P(t.forward)$$

## Non-interference

1. All properties are coherent.
2. All coherent properties are preserved.

## Unreasonable approach

$$t.s = t.forward.s$$

## Being less unreasonable (1/3): errors

Errors

## Being less unreasonable (2/3): trees

Tree-valued attributes and `.host`

## Being less unreasonable (3/3): pp

Closing the world.

## Automatic property testing

## Summary

- Modular and composable proofs through coherence
- But also supports a testing-based approach
- Can make extensions "feel native"

## Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Talk later this week:

- OOPSLA on Friday (11:37). AbleC.

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)


