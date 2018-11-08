# Work in Progress
TODO:
* Fill in content:
    * Describe Ceph:
        * what is Ceph?
        * Why only strong consistency?
        * Why weaker consistency (Examples, examples, examples)?
        * How weaker consistency? (Also, Object Gateway?)
            * What granularity
            * How is it tunable
            * Quorum?
    * Describe Declarative Programmable Storage:
        * how does it interact with Ceph?
        * how could consistency level be specified
        * how could consistency level be enforced
    * Link Declarative programmable storage and consistency back to IPA and
      MixT
* Create and include graphics
* coalesce this mdbook into a single md-format post

# Introduction
This blog post explores how to reason about consistency (and similar
properties) in a declarative programmable storage system. **Declarative**
*Programmable Storage* (DPS) is a complex system with many components and
subsystems. While this post will necessarily discuss background, the intent is
to consider implementations of type systems for enforcing consistency levels
(e.g. [IPA][disciplined-inconsistency] and [MixT][mixt]) and how they can
inform intelligent enforcement of consistency levels in a storage system.

Even beyond the use of declarative reasoning over mixed consistency levels,
this investigation will be useful for future work, exploring the generalization
of data properties enforceable with pre and post conditions, and how to reason
over them.

# Navigating this blog post
For understanding work on consistency models that influences this blog post,
see the [Consistency Models](consistency/consistency-models.md) and
[Consistency Types](consistency/consistency-types.md) subsections of the
[Background](consistency/intro.md) section. For understanding what declarative
programmable storage is, and the relevance of consistency models and
consistency as a property of data to declarative programmable storage, see the
[Declarative Programmable
Storage](consistency/declarative-programmable-storage.md) subsection of the
[Background](consistency/intro.md) section.

The meat of what this blog post then investigates, can be found in the
[Consistency as a Property](consistency/consistency-property.md)
subsection of the [Consistency](consistency/intro.md) section. The
Investigation section, overall, describes how consistency as a property may be
implemented and reasoned over in a declarative programmable storage system.

[disciplined-inconsistency]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf

[hacky-comment-1]: reason_about_consistency_from_a_dataflow_perspective.
