# Consistency Types
As described in the [Consistency Models](consistency-models.md) section, a consistency model is a
framework for defining what consistency is. Because there are many consistency models, it is useful
to understand how to compare them, but especially how to verify and enforce them. Consistency type
systems associate data types with the consistency model we would like the data types to conform to.
The consistency model is defined by consistency constraints (or requirements), in a way that the
programming language or system can enforce. The association of data types with consistency models
allows the developer, and the system, to explicitly reason over consistency constraints in
applications.

## Related Work
In [CMPS290S][course-website], we have been reading a handful of papers that discuss various tools
for understanding, analyzing, and reasoning about consistency in a distributed system. There are a
few categories that I would group related work into:

1. Consistency type systems
2. Analysis of consistent, distributed logic

For the first category, consistency type systems, I first describe and analyze the following
existing implementations:
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
