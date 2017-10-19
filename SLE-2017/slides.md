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

## The problem of interference

- Both worked perfectly in isolation.
- Who is at fault?
- What went wrong?
- How can we prevent this?

## Non-interference through verification

- Extensions come with properties
- Ensure these properties are preseved under composition
- In a sense, composable proofs

\vspace{1pt}

- Tricky: proofs *and* specifications become incomplete

## Coherence

$$P(t) \iff P(t.forward)$$

- Tool to complete specifications

## Non-interference

1. All properties are coherent.
2. All coherent properties are preserved.

## So what went wrong?

```
production taint
e::Expr ::= x::Expr
{
  e.tainted = true;
  forwards to x;
}
```


# Can we do this without verification?


## Unreasonable approach

$$t.s = t.forward.s$$

## Being less unreasonable (1/3): errors

$$t.errors = t.forward.errors$$

\StartCol

```
production errorExpr
e::Expr ::= msgs::[Message]
{
  e.errors = msgs;
}
```

\EndCol\StartCol

```
production bridge
e::Expr ::= x::ExtensionAST
{
  forwards to
    if null(x.errors) then
      x.translation
    else
      errorExpr(x.errors);
}
```

\EndCol

## Being less unreasonable (2/3): trees

- "Strict equality" is interfering, which is actually useful
- $.host$ as recursive $.forward$
- Fact: $P(t) \iff P(t.host)$
- Trick: $t.s.host = t.host.s$

![](typerep-commutes.pdf)

## Being less unreasonable (3/3): pretty-printing

- Normally preserve all coherent properties
- Important! Don't know what you need.
- Except with pretty printing

$$t.pp.parse.host = t.host$$

- Close the world
- The *only* property we know of `pp`

## Automatic property testing

- $t.errors = t.forward.errors$
- $t.typerep.host = t.host.typerep$
- $t.pp.parse.host = t.host$

\vspace{1pt}

- This is all QuickCheck-able!

## Summary

- Modular and composable proofs through coherence
- But also supports a testing-based approach
- Can make extensions "feel native"
- Restriction boils down to "must forward to actually-equivalent tree"

## Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Talk later this week:

- OOPSLA on Friday (11:37). AbleC.

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)


