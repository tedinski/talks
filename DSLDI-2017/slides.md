---
title:    Reliable composition of domain-specific language features
subtitle: "Making language extension a practical tool"
author:   Ted Kaminski and Eric Van Wyk \newline (University of Minnesota)
date:     October 2017
fontsize: 12pt
classoption: aspectratio=169, xcolor=dvipsnames
---

## Language extension

- Sometimes we do not need a whole language
- Re-use of a "host language" a plus

\vspace{1pt}

- But this approach hasn't really caught on
- Macros have, to some extent...

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

$$\big(\forall i \in [1,n]. \mathit{local\_restrict}(H \triangleleft E_i)\big) \implies \mathit{global\_property}(H \triangleleft \{ E_1, ..., E_n \})$$

Tools:

- Attribute grammars with _forwarding_
- Copper: LALR(1) _modular determinism analysis_
- Silver: AG _modular well-definedness analysis_
- Non-interference through _coherence_

\vspace{1pt}

- AbleC: an extensible C compiler front-end


## Extensions we can build for AbleC

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

## Impact of restrictions on extension design

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

# Remaining challenges


## Challenge: IDE support

- We can build support into the host language
    - Highlighting, Building, Error reporting, Reference tracking
- But that's not the best extensibility model
    - Consider refactoring
    - Can build, e.g., renaming into the host language
    - Can't introduce it as an extension

## Challenge: Debuggers

- Our model might work.
- But would require significant development effort.

![Hypothetical debugger](hypothetical-dwarf-llvm.pdf)

## Challenge: Host language evolution

- Language changes potentially break extensions
- Good news: fewer changes to host, if implemented as extensions
- Bad news: Not just language changes, but compiler changes too
- Good news: MWDA can detect potentially breaking changes
- Good news: We've got a nice CI system

## Summary

- Silver & Copper as platform for building extensible languages
- AbleC as extensible C compiler

## Impact

How do we get new language features?

- No consensus? No new feature!
- No experience using it until after standards committee decides
- Stuck with initial design forever; no breaking changes!

Imagine if libraries were like this.

This is why we want the library model of language extension.

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


