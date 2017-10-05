---
title:    Reliable composition of domain-specific language features
subtitle: "Making language extension a practical tool"
author:   Ted Kaminski \newline University of Minnesota
date:     October 2017
fontsize: 12pt
---

# Language extension

- Sometimes we do not need a whole language
- Re-use of a "host language" a plus

\vspace{1pt}

- But this approach hasn't really caught on

# Hypothesis: reliable composition

- Composition of independent extensions prone to failure
    - Syntax, expression problem, interference
- Ecosystem is exponentially growing space of conflicts
- Users don't want to debug their compilers

# The library model of language extension

- Import language extensions like libraries
- Must be no possibility of conflicts
    - Except as with libraries: name clashes
    - Must handle just as easily

# Approach: modular analyses

- Apply modest restrictions to extensions
- Eliminate conflicts globally

Tools:

- Copper: LR(1) modular determinism analysis
- Silver: AG modular well-definedness analysis
- Non-interference through _coherence_

\vspace{1pt}

- AbleC: an extensible C compiler front-end

# 

\Large{Demo}

# Modular determinism

# Modular well-definedness

# Non-interference

# Challenge: IDEs

# Challenge: Debuggers

# Challenge: Host language evolution

# Impact

Imagine if libraries were like language features

- No consensus? No library!
- No experience using it until after standards committee decides
- Stuck with initial design forever; no breaking changes!

# Thanks!

Get in touch

- Eric Van Wyk `<evw@cs.umn.edu>`
- Ted Kaminski `<tedinski@cs.umn.edu>`

Talks this week:

- SLE on Tuesday. Non-interference.
- OOPSLA on Friday. AbleC.

Check things out:

- [melt.cs.umn.edu](https://melt.cs.umn.edu)
- [github.com/melt-umn](https://github.com/melt-umn)


