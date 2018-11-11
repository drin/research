# TODO:
* Link Declarative programmable storage and consistency back to IPA and
  MixT

# Introduction
In this blog post, I explore how to provide enforcement of various consistency models in a
**D**eclarative **P**rogrammable **S**torage (DPS) system. I analyze implementations of type
systems (e.g. [IPA][ipa-paper], [MixT][mixt-paper]) and declarative programming models (e.g.
[Declarative Programming over ECDS][quelea-paper]) for enforcing a variety of consistency models.

Necessary background information can be found in the following sections:
* [**D**eclarative **P**rogrammable **S**torage](declarative-programmable-storage/index.md):
    * [*programmable storage* systems](declarative-programmable-storage/programmable-storage.md)
    * [glossary of DPS system terminology](declarative-programmable-storage/glossary.md)
* [Consistency](consistency/index.md)
    * [consistency models](consistency/consistency-models.md)
    * [consistency type systems](consistency/consistency-type-systems.md)
    * [consistency as a property](consistency/consistency-as-a-property.md)

I imagine that enforcement of consistency models for a DPS system requires two major parts:
1. Mechanisms for supporting weaker consistency models in a storage system
2. A way to define consistency requirements for data, and a way to enforce those consistency
   requirements for operations on that data.

The IPA and QUELEA implementations used [Cassandra][cassandra-datastore] as the backend data store,
while the MixT impelemntation used the [PostgreSQL][postgres-dbms] data management system as the
backend data store. 

<!--TODO-->
Cassandra uses a quor

I am interested in using Ceph, a programmable storage system as
the backend. Some [initial work on a DPS system][noah-dissertation], by Noah Watkins, uses Ceph as
the backend storage system for designing a DPS system. In general, the purpose of DPS systems is to
address maintainability and expressiveness of *programmable storage* systems using declarative
languages.

For distributed storage systems, strong consistency is a must. While many data management systems
sacrifice strong consistency for availability and performance, a storage system that provides file
and object storage interfaces is expected to be durable. However, it is common for applications
with large data volumes and rich data models to prefer the storage system over a data management
system for ad-hoc data processing. This is especially common in social, web-based applications. For
applications that use the storage system for non-essential data persistence, trading weaker
consistency for availability and lower latency can be preferable.

Even beyond the use of declarative reasoning over mixed consistency levels,
this investigation will be useful for future work, exploring the generalization
of data properties enforceable with pre and post conditions, and how to reason
over them.

The meat of what this blog post then investigates, can be found in the
[Consistency as a Property](consistency/consistency-property.md)
subsection of the [Consistency](consistency/intro.md) section. The
Investigation section, overall, describes how consistency as a property may be
implemented and reasoned over in a declarative programmable storage system.

## Short Term Objective
To design and implement a DPS system that is able to optimize over, and generate an "execution
plan," for a storage application *that can accommodate several consistency models*.

## Long Term Objective
 (and similar
properties) 

[ipa-paper]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt-paper]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[quelea-paper]: http://kcsrk.info/papers/quelea_pldi15.pdf
[noah-dissertation]: https://cloudfront.escholarship.org/dist/prd/content/qt72n6c5kq/qt72n6c5kq.pdf?t=pcfodf

[cassandra-datastore]: http://cassandra.apache.org/
[postgres-dbms]: https://www.postgresql.org/docs/9.4/release-9-4.html

[hacky-comment-1]: reason_about_consistency_from_a_dataflow_perspective.
