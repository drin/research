# Supporting mixed consistency in a Declarative Programmable Storage System

## Introduction
In this blog post, we explore how to enforce various consistency models in a **D**eclarative
**P**rogrammable **S**torage (DPS) system. In the following sections, we describe what we mean by
DPS systems:

* [**D**eclarative **P**rogrammable **S**torage](declarative-programmable-storage/index.md):
* [*programmable storage* systems](declarative-programmable-storage/programmable-storage.md)
* [glossary of DPS system terminology](declarative-programmable-storage/glossary.md)

Once we know what a DPS system is, and what type of backend data store we are using, We analyze
implementations of consistency type systems (e.g. [IPA][ipa-paper], [MixT][mixt-paper]) and
declarative programming models (e.g. [QUELEA][quelea-paper]) for explicitly accommodating, and
reasoning over, a variety of consistency models. Background for understanding what consistency
means and what consistency models are, can be found in these sections:

* [Consistency](consistency/index.md)
* [consistency models](consistency/consistency-models.md)

And then we explore our main topics of interest in these sections:

* [consistency type systems](consistency/consistency-type-systems.md)
* [consistency as a property](consistency/consistency-as-a-property.md)

### Overview
For a DPS system, There are 2 major features that I explore to allow developers specify
consistency requirements over data types:
1. Mechanisms for supporting weaker consistency models in the backend storage system.
2. A way to define, and enforce, consistency requirements for data types.

The IPA and QUELEA implementations used [Cassandra][cassandra-datastore] as the backend storage
system, while the MixT implementation used [PostgreSQL][postgres-dbms] as the backend storage
system. To support consistency types on top of a data store, it would be ideal to use a data store
that supports weaker consistency models, such as Cassandra, that way logic only needs to be added
on top of the data store. Cassandra provides flexible consistency by allowing writes to be sent to
any number of replicas within each datacenter or across datacenters. In contrast, a data store that
only supports strong consistency, such as PostgreSQL, requires an alternate approach to adding
support for consistency types. MixT defines its own transaction context, and breaks this
transaction down into separate PostgreSQL transactions in order to control consistency.

For my data store backend, I am interested in using Ceph, a programmable storage system. Some
[initial work on a DPS system][noah-dissertation], by Noah Watkins, uses Ceph as the backend
storage system for designing a DPS system. In general, the purpose of DPS systems is to address
maintainability and expressiveness of *programmable storage* systems using declarative languages.
For traditional storage systems, strong consistency is a must. Although Ceph is a distributed,
large-scale storage system, Ceph was designed to fill the role of traditional storage systems as
reliable, durable storage. This expectation is common (and preferred) for many applications,
especially scientific applications, where the complexity of distributed systems and consistency
models weaker than linearizability is too difficult to work with. This makes Ceph's support for
only strong consistency reasonable. However, there are many other applications with large data
volumes, rich data models, but with inherently low business risk that prefer to trade strong
consistency for availability and performance. This is especially common in social, web-based
applications. To build a storage system that can support these types of applications, it would be
ideal to provide the ability to specify weaker consistency models over low risk, or non-essential,
data types.

Even beyond the support of mixed consistency levels, this investigation will be useful for future
work, exploring the generalization of data properties. MixT claims that consistency is a property
of data. For storage systems which are designed entirely around the storage of data, being able to
enforce pre- and post-conditions and reason over general properties of data seems incredibly
useful. This is a secondary motivation of this blog post, to explore a generalization of
consistency types to data type properties.

[ipa-paper]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt-paper]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[quelea-paper]: http://kcsrk.info/papers/quelea_pldi15.pdf
[noah-dissertation]: https://cloudfront.escholarship.org/dist/prd/content/qt72n6c5kq/qt72n6c5kq.pdf?t=pcfodf

[cassandra-datastore]: http://cassandra.apache.org/
[postgres-dbms]: https://www.postgresql.org/docs/9.4/release-9-4.html

[hacky-comment-1]: reason_about_consistency_from_a_dataflow_perspective.
