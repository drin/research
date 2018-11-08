# Consistency Types
This subsection provides necessary background to understand how type systems
for explicit interaction with mixed consistency models have been approached.

## Related Work
In [CMPS290S][course-website], we have been reading a handful of papers
discussing consistency models and various tools for understanding, analyzing,
and reasoning about consistency in a distributed system. There are a few
categories that I would group related work into:

1. Consistency type systems
2. Analysis of consistent, distributed logic

Consistency type systems are type systems that explicitly accommodate
consistency constraints on abstract data types. For this category, this blog
post is influenced by:
* [Disciplined Inconsistency with Consistency Types][disciplined-inconsistency]
* [MixT: A Language for Mixing Consistency in Geodistributed
  Transactions][mixt]

Analysis of consistent, distributed logic is analysis for understanding, proving,
and designing distributed systems with respect to the requirements of various
consistency models that abstract data types and operations satisfy. For this
category, this blog post is influenced by:
* [Replicated Data Types: Specification, Verification, Optimality][rdt-svo]
* ['Cause I'm Strong Enough: Reasoning about Consistency Choices in Distributed
  Systems][strong-enough]

## Consistency type systems

## Analysis of consistent, distributed logic

[course-website]: http://composition.al/CMPS290S-2018-09/
[disciplined-inconsistency]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[rdt-svo]: https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/replDataTypesPOPL13-complete.pdf
[strong-enough]: http://software.imdea.org/~gotsman/papers/logic-popl16.pdf
