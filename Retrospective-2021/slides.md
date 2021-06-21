---
title:    Reliably Composable Language Extensions
subtitle:
author:   Ted Kaminski
date:     June 2021
fontsize: 12pt
classoption: aspectratio=169, xcolor=dvipsnames
---

## Library model of language extension

![](extension-model.pdf)

## The Expression Problem

![](expression-problem.pdf)

## The Big Picture: solving conflicts _modularly_

![](solutions-overview.pdf)

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

- Extend syntax
- Extend analysis
- As capable as an external DSL
- Feels like native language features
- Reliable composition is key to library model

# Emergent incorrectness: a concrete example

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

# Non-interference through verification

## Modular and composable proofs

- Extensions come with properties
    - $P(t) = t.tainted \iff \text{``$t$ contains a $taint$ node"}$
- Ensure these properties are preseved under composition with other extensions

\vspace{1pt}

- To achieve this, we must constrain extensions (hopefully only slightly)

\vspace{1pt}

- Tricky: proofs *and* specifications become incomplete

\vspace{-10pt}

$$P(or(l,r)) \leftarrow \ldots$$
$$P(literal(b)) \leftarrow \ldots$$
$$P(id(t)) \leftarrow ~???$$

## Some simplified logic

$$\forall t \forall y. R_1(t, y) \implies R_2(t)$$

## Coherent relations

$$R(t) \iff R(t.forward)$$

- To start with, this allows us to complete specifications

## Non-interference

1. All properties must be coherent.
2. Extensions must preserve all coherent properties. (How?)


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

# How can we prove presevation of all coherent properties?

## The simplest thing that could work

$$t.s = t.\mathit{forward}.s$$

- Ensures we preserve all coherent properties
- But this would be failure!

## Coherent notions of equality

$$ id(literal(F)) \overset{?}{=} literal(F)$$

- The usual notion of equality is incoherent, which is actually useful
- $.host$ as recursive application of $.\mathit{forward}$
- Fact: $P(t) \iff P(t.host)$
- Trick: $t.s.host = t.host.s$

![](typerep-commutes.pdf)

## Practical difficulties?

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

## Engineering the host language

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


## Close the world

- Normally preserve all coherent properties
- Important! Don't know what we might need!
- But sometimes we do know.

$$t.pp.parse.host = t.host$$

- Close the world
- The *only* property we know of `pp`

## Automatic property testing

- $t.errors = t.\mathit{forward}.errors$
- $t.typerep.host = t.host.typerep$
- $t.pp.parse.host = t.host$

\vspace{1pt}

- We can test these!
- Specification-less!

## Summary

- Support expressive extensions: new syntax & analysis
- Modular and composable proofs through coherence
- But also supports a testing-based approach
- Can make extensions "feel native"
- Restriction boils down to "must forward to actually-equivalent tree"
    - Host-language relative restrictions

## Thanks!

Any questions?

Check things out:

- [github.com/melt-umn/silver](https://github.com/melt-umn/silver)
- [github.com/melt-umn/ablec](https://github.com/melt-umn/ablec)


