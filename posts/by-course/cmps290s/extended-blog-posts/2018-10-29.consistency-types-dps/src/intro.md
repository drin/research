# Work in Progress
TODO:
* Fill in content:
    * Describe background (consistency types and analysis of consistency)
    * Describe research question being investigated
    * Describe goals and long-term value of investigation
    * Describe investigation details:
        * design
        * implementation (low-hanging fruit)
        * future implementation
* Create and include graphics
* coalesce this mdbook into a single md-format post

# Introduction
This extended blog post explores how to reason about consistency (and similar
properties) in a declarative programmable storage system. On one hand, this
post will explore the use of types to enforce consistency, on the other hand,
this post will explore how to generalize the property of consistency and how to
reason about it from a dataflow perspective. Although it is likely out of scope
for this class (CMPS 290S), an approach to implementation is something that
this post will design, architect, and explore.

# Navigating this blog post
For understanding work on consistency models that influences this blog post,
see the [Consistency Models](background/consistency-models.md) and [Consistency
Types](background/consistency-types.md) subsections of the
[Background](background/intro.md) section. For understanding what declarative
programmable storage is, and the relevance of consistency models and
consistency as a property of data to declarative programmable storage, see the
[Declarative Programmable
Storage](background/declarative-programmable-storage.md) subsection of the
[Background](background/intro.md) section.

The meat of what this blog post then investigates, can be found in the
[Consistency as a Property](../investigation/consistency-property.md)
subsection of the [Investiation](../investigation/intro.md) section. The
Investigation section, overall, describes how consistency as a property may be
implemented and reasoned over in a declarative programmable storage system.
