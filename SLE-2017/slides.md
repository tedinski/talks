---
title:    Ensuring Non-interference of Composable Language Extensions
subtitle: "A practical approach"
author:   Ted Kaminski and Eric Van Wyk \newline (University of Minnesota)
date:     October 2017
fontsize: 12pt
classoption: aspectratio=169, xcolor=dvipsnames
---

## Library model of language extension

![](extension-model.pdf)

## Examples of extensions

\StartCol

\begin{alltt}
\small
\begin{tabbing}
aa\=aa\=aa\=aa\=aa\=aa\=\kill
\\
typedef \hib{datatype Tree} Tree; \\
\hib{datatype Tree \ttlbrace} \\
\>\hib{Fork (} Tree*\hib{,} Tree*\hib{,} const char* \hib{);} \\
\>\hib{Leaf (} const char* \hib{); }\\
\hib{\ttrbrace;}
\end{tabbing}
\end{alltt}

\EndCol\StartCol

\begin{alltt}
\small
\begin{tabbing}
aa\=aa\=aa\=aa\=aa\=aa\=\kill
\cilk{cilk} int count\_matches (Tree *t) \ttlbrace \\
\>\hib{match (} t \hib{) \ttlbrace} \\
\>\>\hib{Fork(t1,t2,str): \ttlbrace} \\ 
\>\>\>int res\_t, res\_t1, res\_t2; \\
\>\>\>\cilk{spawn res\_t1 = count\_matches(} t1 \cilk{);} \\
\>\>\>\cilk{spawn res\_t2 = count\_matches(} t2 \cilk{);} \\
\>\>\>res\_t = (str \regex{=\textasciitilde{} /foo[1-9]+/}) ? 1 : 0; \\
\>\>\>\cilk{sync;} \\
\>\>\>\cilk{cilk return} res\_t1 + res\_t2 + res\_t \cilk{;} \\
\>\>\hib{\ttrbrace ;} \\
\>\>\hib{Leaf(str): \ttlbrace} return (str \regex{=\textasciitilde{} /foo[1-9]+/}) ? 1 : 0; \hib{\ttrbrace ;} \\
\>\hib{\ttrbrace} \\
\ttrbrace
\end{tabbing}
\end{alltt}

\EndCol

## Goals

- Extend syntax, extend analysis
- Want best of both worlds, external and embedded DSLs
- Reliable composition is key to library model

## Previous work

**Forwarding:**
:   manage the expression problem

**Modular determinism analysis:**
:   no unexpected syntactic conflicts

**Modular well-definedness analysis:**
:   well-defined attribute grammar

## Simple host language & Forwarding

\StartCol

```
nonterminal Expr with eval;

synthesized attribute eval :: Boolean;

production or
e::Expr ::= l::Expr  r::Expr
{ 
  e.eval = l.eval || r.eval;
}
production literal
e::Expr ::= b::Bool
{ 
  e.eval = b;
}
```

\EndCol\StartCol

\vspace{60pt}

```
production implies
e::Expr ::= l::Expr  r::Expr
{
  forwards to or(not(l), r);
}
```

\EndCol

## The problem of interference

\StartCol

Extension A\vspace{-8pt}

```
synthesized tainted :: Boolean;
production taint
e::Expr ::= x::Expr
{
  e.tainted = true;
  forwards to x;
}
aspect or
e::Expr ::= l::Expr  r::Expr
{ 
  e.tainted = l.tainted || r.tainted;
}
aspect literal
e::Expr ::= b::Bool
{ 
  e.tainted = false;
}
```

\EndCol\StartCol

Extension B\vspace{-8pt}

```
synthesized transform :: Expr;
production id
e::Expr ::= x::Expr
{
  forwards to x.transform;
}

aspect or
e::Expr ::= l::Expr  r::Expr
{
  e.transform = or(l.transform, r.transform);
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
- Who is at fault? Is there anyone blame?
- What went wrong?
- How can we prevent this?

## Non-interference through verification

- Extensions come with properties
    - $P(t) = t.tainted \iff \text{``$t$ contains a $taint$ node"}$
- Ensure these properties are preseved under composition with other extensions
- In a sense, modular and composable proofs
- To achieve this, we must constrain extensions (hopefully slightly)

\vspace{1pt}

- Tricky: proofs *and* specifications become incomplete

## A coherent property

$$P(t) \iff P(t.forward)$$

- To start with, this allows us to complete specifications

## Non-interference

1. All properties must be coherent.
2. Extensions must preserve all coherent properties.


- Extension developers check these restrictions independently
- All (coherent) properties hold of the composed language

## So what went wrong?

```
production taint
e::Expr ::= x::Expr
{
  e.tainted = true;
  forwards to x;
}
```

- This gives us a precise notion of blame

# Can we do this without verification?


## Unreasonable approach

$$t.s = t.\mathit{forward}.s$$

- Ensures we preserve all coherent properties
- Surprisingly good at ensuring intended properties are coherent

## Being less unreasonable (1/3): errors

$$t.errors = t.\mathit{forward}.errors$$

Then this becomes incoherent:

```
production bridge
e::Expr ::= x::ExtensionAST
{
  e.errors = x.errors;
  forwards to x.translation;
}
```

## Being less unreasonable (1/3): errors

$$t.errors = t.\mathit{forward}.errors$$

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

- "Strict equality" is incoherent, which is actually useful
- $.host$ as recursive application of $.\mathit{forward}$
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

## Being less unreasonable: summary

- Generic error production methodology
- Tree-valued attributes already are flexible
- Closing the world on a single attribute: `pp`

\vspace{1pt}

- Extensions "feel" like native language features

## Automatic property testing

- $t.errors = t.\mathit{forward}.errors$
- $t.typerep.host = t.host.typerep$
- $t.pp.parse.host = t.host$

\vspace{1pt}

- This is all QuickCheck-able!

## Summary

- Support expressive extensions: new syntax & analysis
- Modular and composable proofs through coherence
- But also supports a testing-based approach
- Can make extensions "feel native"
- Restriction boils down to "must forward to actually-equivalent tree"
    - Host-language relative restrictions

## Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Talk later this week:

- OOPSLA on Friday (11:37). AbleC.

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)


