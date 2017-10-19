---
title:    Reliable composition of domain-specific language features
subtitle: "Making language extension a practical tool"
author:   Ted Kaminski \newline (University of Minnesota)
date:     October 2017
fontsize: 12pt
---

## Language extension

- Sometimes we do not need a whole language
- Re-use of a "host language" a plus

\vspace{1pt}

- But this approach hasn't really caught on

## Hypothesis: reliable composition

- Composition of independent extensions prone to failure
    - Syntax, expression problem, interference
- Ecosystem is exponentially growing space of conflicts
- Users don't want to debug their compilers

## The library model of language extension

- Import language extensions like libraries
- Must be no possibility of conflicts
    - Except as with libraries: name clashes
    - Must handle just as easily

## Approach: modular analyses

- Apply modest restrictions to extensions
- Eliminate conflicts globally

Tools:

- Attribute grammars with _forwarding_
- Copper: LR(1) modular determinism analysis
- Silver: AG modular well-definedness analysis
- Non-interference through _coherence_

\vspace{1pt}

- AbleC: an extensible C compiler front-end


# Demo


## 30 sec Silver crash course

```
production or
lhs::Expr ::= a::Expr  b::Expr
{
  lhs.pp = s"${a.pp} | ${b.pp}";
  lhs.eval = a.eval || b.eval;
}

production implies
lhs::Expr ::= a::Expr  b::Expr
{
  lhs.pp = s"${a.pp} -> ${b.pp}";
  forwards to or(not(a), b);
}
```


# So what kind of extensions can we build?


## Modular determinism

- Bridge productions

\vspace{-10pt}

```
production bridge
HostNT ::= MarkingTerminal ExtensionNT
```

Okay: `match(Expr) { ...`  
Not okay: `id<TypeExpr>`

- Follow sets

Okay: `terminal(Expr, Expr)`  
Not okay: `Expr :+: Expr`  
(Okay: `BinOp ::= ':+:'`)

## Modular well-definedness

- Use forwarding

\vspace{-10pt}

```
production bridge
h::HostNT ::= MarkingTerminal e::ExtensionNT
{
  forwards to e.asHostAst;
}
```

\vspace{-10pt}

- Flow type restrictions

Okay: `lhs.newSyn = ... lhs.newInh`  
Not okay: `forwards to ... lhs.newInh`  
(Okay: creative use of `lhs.env`)

## Non-interference

- Forward to something actually equivalent

Not okay: `letRec` forwards to `let` altering `env` and `translation`  
Okay: arbitrary analysis of `lhs.env` and subtrees  
(Not okay: expecting to find forwarding productions there)

- Modular proofs, testing methodology, and neat tricks!
- See our talk Tues at SLE!


# Remaining challenges


## Challenge: IDE support

- We can build them
- The extensibility model isn't great
    - Refactorings, in particular.

## Challenge: Debuggers

- Our model might work.
- But would require significant development effort.

![Hypothetical debugger](hypothetical-dwarf-llvm.pdf)

## Challenge: Host language evolution

- Language changes potentially break extensions
- Bad news: Not just language changes, but compiler changes too
- Good news: MWDA can detect potentially breaking changes
- Good news: We've got a nice CI system

## Impact

Imagine if libraries were like language features

- No consensus? No library!
- No experience using it until after standards committee decides
- Stuck with initial design forever; no breaking changes!

## Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Talks this week:

- SLE on Tuesday (11:20). Non-interference.
- OOPSLA on Friday (11:37). AbleC.

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)


