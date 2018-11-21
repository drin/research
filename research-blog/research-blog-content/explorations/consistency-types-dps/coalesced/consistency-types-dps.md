---
title: Mixing Consistency in a Declarative Programmable Storage System
author: Aldrin Montana
layout: single
classes: wide
---

by Aldrin Montana &middot; edited by Abhishek Singh and Lindsey Kuper

<!-- ------------------------------>
<!-- SECTION -->
# Introduction
Modern distributed applications call for ways to store and access data using a range of consistency
guarantees. Consider the shared log, described in the [FuzzyLog][fuzzylog-paper] paper. A log
service is useful in many contexts for many applications including data management systems and
storage systems. The FuzzyLog paper describes a common usage of log services for funneling updates
for data management systems and other services (e.g., metadata and coordination services,
filesystem namespaces) within data centers. They motivate their work on a _distributed shared log_
by discussing the benefit of distributing the log across several servers and relaxing constraints
on data consistency.

Developing on top of distributed systems can be difficult. The complexity of consistency models and
making sure code is correct is one thing, but it becomes even more difficult in cases where data
accessed at one level of consistency is used in the computation for data at a different level of
consistency. FuzzyLog makes operations to a single color across regions causally consistent, while
operations to a single color within a region are serializable. In this way, FuzzyLog uses at least
two levels of consistency in its implementation: causal and serializable.

Recent systems such as [QUELEA][quelea-paper], [IPA][ipa-paper], and [MixT][mixt-paper] aim to make
programming in a mixed-consistency world safer and easier. QUELEA allows the developer to
declaratively specify the consistency guarantees provided by a datastore and the consistency
requirements for application-level operations. IPA provides data types representing consistency
levels that can be mixed into standard ADTs, and consistency policies for determining the
consistency of a data type at runtime. MixT is a domain-specific language (DSL) that uses
information flow analysis to prevent weakly consistent data from influencing strongly consistent
data.

In this blog post, we are interested in exploring approaches for specifying a range of consistency
guarantees in a _declarative programmable storage_ (DPS) system. A quick mention of what this blog
post (and a subsequent blog post) will cover (which is quite a few things):
1. informal overview of data and operation consistency in distributed systems
2. what is a programmable storage system
3. how can we mix consistency in a programmable storage system
4. what is a declarative programmable storage system
5. a motivating example using FuzzyLog and [ZLog][noah-blog-zlog] (an implementation of CORFU on a
   programmable storage system)

<!-- ------------------------------>
<!-- SECTION -->
# Data Consistency
In a distributed sytem where data is copied between multiple servers, it is necessary to know
*where* to access a copy of the data, and how certain that copy is *correct*. For introductory
purposes, let's assume a small system where each _data object_ has 2 copies total
(Copy<sub>1</sub> and Copy<sub>2</sub>) distributed over 2 servers. Consistency addresses the key
question, what if you're accessing Copy<sub>1</sub> and it is somehow different from
Copy<sub>2</sub>? There are many scenarios in which the two copies can be different, which have
been significantly studied.

A _consistency model_ defines the scenarios in which we are willing to tolerate *uncertainty*
that Copy<sub>1</sub> and Copy<sub>2</sub> are the same. Some consistency models (with brief
descriptions) that are relevant for us in this blog post:
* strong
    All clients agree on the order that operations on a data object appear.
* linearizable
    Any operation on a data object should appear as if applied instantaneously (atomic) and any
    subsequent operations should be applied with respect to the new data object state: if the first
    operation was a write, then the result of the write should be visible to the second operation;
    if the first operation was a read, then the value that was read should also be visible to the
    second operation.
* eventual
    Copies of a data object may be appear different at any point, but when given enough time
    without updates, all copies will converge to the same state.
* red-blue
    Operations on data objects may be strongly consistent (red) or a weaker consistency (blue),
    such as eventual. Red operations must be ordered with respect to each other, while blue
    operations may be in any order (commutative).
* causal+
    Operation effects are transitive - The result of both operations, Op0 and Op1, should be visible
    to a subsequent operation, Op2, if:
        * Op2 depends on Op1
        * Op1 depends on Op0

It's useful to note (especially for the next blog post), that some research, such as
[MixT][mixt-paper], assert that consistency is a _property of the data_ being operated on. The
reason this is interesting, is that **A**bstract **D**ata **T**ypes (ADTs) are defined by the
operations that can be invoke on them. So the concept of data and operations are necessarily
difficult to disentangle. And, when we consider some of the informal definitions above, such as
red-blue consistency,they seem to be defined on operations rather than data. I just want to point
out, that most consistency models provide a logical interface for operating on data, but from the
perspective of storage systems, data is something that is persistent and accessed by many disjoint
logical interfaces (programs) that may not use the same consistency models. In this way, it's
possible to see that for storage, consistency is a property of data (hence, data consistency), even
though applications may frame consistency from the perspective of operations.

<!-- ------------------------------>
<!-- SECTION -->

# Programmable Storage
<!-- super general intro -->
Across all fields of computing, data storage is extremely important. In fact, even the earliest
models of computing require the concept of _tape_, as an infinite, contiguous sequence of locations
that can store symbols. It likely isn't surprising that significant improvements in storage devices
have huge impacts for many areas of computing. However, hardware improvements have not always come
fast enough. As application requirements for storage have grown, storage systems have grown more
complex. To accommodate web-scale and high-performance applications, storage systems have become
distributed, and spanned many storage devices.

<!-- Tie super general to storage systems -->
The performance of a storage system has significant impact on the design and implementation of
applications that communicate with it--consider HDFS and cloud-services such as Amazon's S3. And
so, as applications increase in both complexity and concurrency, there is an increasing need to
extract better performance from the storage system. But, in addition to growing application needs,
there are also improvements in hardware that storage systems have yet to utilize. Due to
reliability needs, storage systems must be well tested, and extensively exercised.  On the other
hand, due to the rate at which requirements are increasing from application interfaces, and the
rate at which underlying hardware is improving, it is necessary to iterate quickly, or to
periodically re-design various subsystems. Additionally, there is a variety of storage devices to
tune for, and that can significantly affect system design and implementation.

<!-- programmable storage intro -->
Programmable storage is an approach to developing storage interfaces, coined by storage systems
researchers at UC Santa Cruz, that emphasizes programmability and reusability of storage
subsystems. Over the last 7 years, various services have been built on top of storage systems, and
various abstractions developed to make programmability in storage systems easier. Due to the
inherently high reliability expectations of storage systems, programmable storage discourages
rewriting storage subsystems or components, because this only invites younger, error-prone code.
The intuition is that reusing subsystems of a storage system means that the community supporting
these subsystems is larger, and these subsystems are exercised and improved more frequently.
Recently, [Malacology][malacology-paper] was developed and published. An interface built on top of
the Ceph storage system, Malacology abstracts storage subsystems into building blocks that can be
combined to more easily build a service on top of the storage system.

Ceph is an open source, distributed, large-scale storage system that aims to be, "[completely
distributed without a single point of failure, scalable to the exabyte level, and
freely-available][ceph-intro-blog]." Ceph has been part of storage systems research at UC Santa
Cruz from [the original Ceph paper][ceph-paper] to the [CRUSH algorithm][crush-paper] (2006) and the
[RADOS][rados-paper] data store (2007); [Noah's recent dissertation][noah-dissertation] on
programmable storage also involved building on top of Ceph.

<!-- TODO: how does PROAR motivate their work -->

Although Ceph is a distributed, large-scale storage system, Ceph was designed to fill the role of
reliable, durable storage. This expectation is common (and preferred) for many applications,
especially scientific applications, where the complexity of weak consistency models is too
difficult to work with. This makes Ceph's support for only strong consistency, via
[primary-copy][ceph-replication] reasonable. However, the trade-off between strong consistency and
availability or performance is very important for some [dynamo-like][dynamo-paper] applications.
Recent work on a weak consistency model for Ceph, [PROAR][proar-paper], has been published by
researchers at the Graduate School at Shenzhen, Tsinghua University. For Ceph to support these
types of applications, it would need to offer weaker consistency as an option. 

Further building up our motivating example, we would like to consider extensions to
[ZLog][noah-blog-zlog], an distributed shared log developed by Noah on top of Malacology. ZLog is
an implementation of CORFU, which was mentioned in the FuzzyLog paper. Remember that FuzzyLog uses
multiple consistency levels to achieve a better performing distributed shared log across
multiple regions. It would be interesting to compare ZLog and FuzzyLog, and then compare FuzzyLog
to an implementation of the FuzzyLog abstraction on Malacology using some method for mixing
consistencies in our programmable service easier.

<!-- TODO: edit, then place this -->
Notice that a reason for using approaches to make
mixing consistencies easier, is because we believe that programmability of storage systems is a
priority.

<!-- ------------------------------>
<!-- SECTION -->
## Mixing Consistency

<!-- TODO:
    * Don't start with consistency type systems, start with overall motivation and description of
      mixing consistency
    * then, talk about a few approaches
        * IPA is just a type system
        * QUELEA is just a contract specification
        * MixT is something in between, using types but also using specification to restrict
          information flow
-->

In [CMPS 290S][course-website], we have been reading a handful of papers that discuss various tools
for understanding, analyzing, and reasoning about consistency in a distributed system. In these
readings, I have been particularly interested in two implementations of consistency type systems
for enforcing consistency requirements over data types:

* [Disciplined Inconsistency with Consistency Types][ipa-paper]
* [MixT: A Language for Mixing Consistency in Geodistributed Transactions][mixt-paper]

For brevity, I call "a type system that includes and enforces consistency" a consistency type
system. Consistency type systems try to lift consistency models to first-class citizens in
the type system of the distributed system programming model. By making consistency models explicit
in the type system, developers are able to verify that important data types are used at an
appropriate correctness level. The IPA paper claims:

    ...IPA allows the programmer to trade off performance and consistency, safe in the knowledge
    that the type system has checked the program for consistency safety.

This type of support from the programming model is necessary for developers to be both
more efficient and more correct. To further support mixing consistency, the MixT paper claims:

    ...engineers at major companies frequently find themselves writing distributed programs that
    mix accesses to data from multiple existing storage systems, each with distinct consistency
    guarantees.

There are many consistency models that are meaningful for developers working in distributed
systems. From the perspective of a consistency type system, we are interested in how to verify and
enforce them for associated data types. To do this, consistency type systems associate data types
with the consistency model we would like the data types to conform to.

<!-- TODO:
    * for below sections, follow this format:
        * concise description of motivation
        * describe high level features
        * describe features of interest to DPS
        * describe implementation
        * describe "how mappable" implementation details are to DPS
-->

### IPA Consistency Type System
[IPA's implementation][ipa-impl] is in Scala and leverages Scala's powerful type system. This
approach allows the developer to directly interact with the consistency type of their data, using
features such as pattern matching. IPA allows consistency guarantees to be specified as a policy on an
ADT in two ways:

1. Static consistency policies--Specify a consistency model (e.g. strong, weak, causal) that can be
   enforced by the data store.
2. Dynamic consistency policies--Specify performance or correctness bounds within which to achieve
   the strongest consistency possible, and which require additional runtime support to enforce.

Static consistency policies are roughly "passed-through" to the data store. Cassandra can support
multiple consistency levels through its use of read and write [quorums][wiki-quorum]. Quorum is
determined by the number of replicas operations are sent to:

* W - the number of replicas that *write operations are sent to*
* R - the number of replicas that *read operations are sent to*
* N - the *total number of replicas* available

When write and read quorum (W + R) is greater than the total number of replicas available, (W + R)
\> N, "quorum intersection" is achieved. With quorum intersection, write and read operations sent
to Cassandra can be strongly consistent. Weaker consistency policies can be satisfied by specifying
fewer replicas to write to (e.g., one or two) or fewer replicas to read from (e.g., one or two)
such that quorum intersection is **not achieved** ((W + R) < N). However, it's not possible to
express causal consistency using read and write quorums alone; therefore, enforcing causal
consistency in Cassandra would require the use of an additional mechanism. Proposed by Bailis, et
al, [Bolt-on Causal Consistency][bolton-paper] is a [shim layer][wiki-shim] approach to "upgrade
eventually consistent stores to provide convergent causal consistency."

Dynamic consistency policies are specifications of performance or behavior properties, within which
the strongest consistency constraints should be satisfied. More concretely, IPA provides two
dynamic consistency types: rushed and interval. These consistency types are out of scope for this
blog post, where we explore only static consistency types.

<!-- TODO:
    * Tie changes to Ceph/DPS back to areas of programmable storage/DPS that we think will benefit
      from this work
    * develop the idea of where in the implementation, and how that is made **available** to the
      developer
    * develop the idea that we can make changes in a few places, and why we're interested in making
      the changes in the area we propose
-->

Because the typical IO path to Ceph's storage cluster does not support various consistency models,
an IPA-style consistency type system would have to be modified, or a new storage interface on top
of Ceph be provided. In the quorum style of supporting various consistency models, Ceph may require
communicating with the OSDs directly. A potential problem could be if the OSD has synchronization
with other OSDs in the configured cluster built-in. If it is possible to communicate writes to OSDs
individually, it would be possible, potentially even "trivial", to enable a quorum approach to
consistency over Ceph OSDs. Understanding the details of OSD communication will be important for
understanding whether Ceph can provide a similar interface to IPA as what Cassandra provides.

<!-- TODO: work in these concepts of isolation and consistency
    One easy way to compare isolation levels with consistency levels is by
    considering single-operation transactions.  In that setting, snapshot
    isolation (SI) (with a notion of session order added on) looks at
    least as strong as causal consistency (CC).  But lots of other
    concepts in isolation levels don't really make sense on single
    operations; read-committed is particularly weakened, as there's not
    much "intermediate" transaction state to observe.  But your question
    really focused on multi-operation transactions, along with whether the
    isolation levels could improve upon the guarantees of causal
    consistency.  The answer to this is a definite yes!  A degenerate
    example is perhaps easiest to see; a transaction can violate atomicity
    while still preserving causal consistency for each of its individual
    operations by simply allowing uncommitted reads; in essence,
    per-operation consistency doesn't "see" the begin/commit/abort events
    as particularly important, and makes no requirements on them.

    But what if we decide that each transaction will instead be
    interpreted as an atomic operation for the purposes of causal
    consistency?  SI's "write skew" looks a bit like CC; it's possible for
    two concurrent transactions to read the same snapshot and commit
    without conflict, even if both transactions read values the other
    transaction would overwrite.  But we also start to see some serious
    differences between SI and CC.  As an example, a write-write conflict
    in SI (where two concurrent transactions attempt to overwrite the same
    value) should cause an abort, but would be perfectly permissible under
    CC!  Looking in the other direction, SI doesn't actually make very
    strong guarantees about *what* snapshot the transaction must operate
    against - so long as we define "snapshot" as "a valid prefix of the
    final serial order of transactions", we won't violate causality - but
    SI doesn't *have* to include session order as a constraint, which
    means it's entirely possible for a single client to execute one
    transaction in a snapshot T_n, and a subsequent transaction in a
    snapshot T_{n-1}.  This definitely violates CC!

    And this brings me to my real point; these isolation levels were all
    originally proposed with an implicit assumption of [mostly]
    session-free transactions operating against a centralized server.
    Snapshot isolation, in traditional centralized pessimistic databases,
    will read from the "latest" system snapshot; the *implementation* of
    SI prevents violations of causality, but the specification does not.
    Once you start to consider a weakly-isolated distributed database, a
    lot of traditional isolation levels start to have problems - a
    failure to track session order, no notion of merging, etc.  And
    databases can sometimes cheat; providing one isolation level for
    transactions w.r.t other transactions at the same replica, while
    providing a different notion of isolation between transactions at
    different replicas.  Weaker isolation levels are even worse!  They
    kind of work in the wrong direction; they're designed to make it
    forgivable for concurrent transactions to accidentally see too much
    without aborting. In a distributed setting, seeing *too many* events
    is rarely the problem - we're more interested in making sure clients
    see *enough* events.

    So to answer your question... I think there really ought to be more
    work done in specifying novel, principled isolation levels for
    distributed, multiversion databases.  There's a whole host of
    guarantees we might want which fit between SI and strict
    serializability, all of which could be compatible with causal
    consistency.  There's also the other direction to consider!
    [Monotonic] Prefix Consistency is actually the strongest consistency
    model possible while still preserving availability in the face of
    partitions, and is formalized directly in terms of a "correct prefix
    of the final serial order of operations" - the same idea from which
    the "Snapshots" in SI are derived.

    A first step is formalizing isolation levels and consistency models in
    common, easily understood terms.  There are a few efforts in this
    direction:
        * favorite recentish one is probably this one:
            https://arxiv.org/abs/1609.06670
        * But the Bible of these things is still Atul Adya's PhD thesis:
            http://pmg.csail.mit.edu/papers/adya-phd.pdf
-->

<!-- TODO: consider Lindsey's reference to MixT TR:
    "When running as a linearizable store, PostgreSQL is put in a “normal” operating mode with a
    single master per object and the SERIALIZABLE transaction isolation level. The coding overhead
    required to create this interface was pleasantly small; about 180 lines of C++ code, mostly for
    registering prepared statements.

    To configure PostgreSQL as a causally consistent store, we created four replicas of data, and
    partitioned client programs among the four copies. Each instance runs transactions with
    snapshot isolation enforcing the guarantees of causal consistency. To order operations
    occurring at distinct replicas, we use a vector clock as a per-row version number. Vector clock
    entries are just the microsecond-resolution time at each master, so vector clock maintenance
    does not add serialization conflicts."
-->

### MixT Consistency Type System
In contrast to IPA's approach, [MixT's implementation][mixt-impl] is in C++ and much lower in the
development stack. Another interesting difference is that the backend data store used is
Postgres. What makes this interesting is that Postgres (to my understanding) supports strong
consistency, but various levels of *isolation*. MixT allows weaker consistencies by providing a
**d**omain **s**pecific **l**anguage (DSL) for defining computation in a *mixed-consistency
transaction*. Operations within these mixed-consistency transactions are then split into smaller
transactions to achieve weaker consistency.

Initially, I thought that building a consistency type system on top of a data store
that *already supports* weaker consistency models seemed more natural. Grouping operations (or enforcing
operation constraints) seemed easier to reason about than slicing operations into sub-groups, or
into isolated operations. Operationally, this makes MixT a very different approach to enforcing
consistency types. At a glance, this approach seems like it should be easier to build on top of
Ceph than an IPA-style consistency type system due to the lack of support for weaker consistency.
However, while the similarity between Ceph and Postgres providing strong consistency by default
seems like something that would make MixT applicable for being used on top of Ceph, it is not clear
that mixed-consistency transactions will be able to achieve weaker consistency on top of Ceph. I
suspect that support for weaker levels of isolation in Postgres enables MixT to support weaker
consistency by breaking up a transaction into smaller transactions. I may be misunderstanding
MixT's implementation, but it seems that weaker isolation is the only way to achieve weaker
consistency if the data store does not explicitly support weak consistency models. Before
attempting to architect an approach that layers MixT on top of Ceph, it will be important to
understand the effect of isolation levels on mixed-consistency transactions. It will also be
important to understand whether transaction splitting has the desired semantics over Ceph, given
that Ceph does not seem to support transactions (though they are requested? It is hard to tell when
they were requested and if they were ever completed).

### Declarative Programming over Mixed Consistencies
[QUELEA][quelea-paper] takes a declarative programming approach to allowing developers directly
reason over the consistency policies used for ADTs. [QUELEA's implementation][quelea-impl] provides
a declarative language for specifying _contract_ for operations on an ADT to follow.

<!-- TODO:
    * Develop the relevance that may exist between DPS and QUELEA
        * why is this particularly "on-brand"
    * Develop how QUELEA's implementation may tie to Ceph and PS and DPS
    * Mention how QUELEA lets you put consistency contracts on datastores
        * then automatically classifies operations into weakest, satisfactory consistency level
-->

QUELEA, like IPA, uses Cassandra as the backend data store. Because QUELEA takes specifications for
an ADT and then communicates with the data store in a way that enforces the consistency
constraints, it seems that QUELEA may also require the ability to communicate with Ceph OSDs
individually, just like IPA. Once Ceph supports weaker consistency data operations, or some way of
communicating with Ceph allows weaker consistency, then QUELEA would be an ideal approach to take
for a DPS system.

<!-- ------------------------------>
<!-- SECTION -->

# Declarative Programmable Storage

<!-- TODO:
 Develop this a lot:
    I think I found the one liner to explain programmable storage:

    "A programmable storage system exposes internal subsystem abstractions as “interfaces” to
    enable the creation of higher-level services via composition"

    http://programmability.us/

    so, a layer over ceph's subsystems (malacology in that link) composes a programmable storage
    system. zlog was built as a service on malacology. zlog is the implementation of corfu on
    programmable storage, as described in declstore. specifying corfu in a declarative language in
    C++ is basically zlog written in a declarative language.
-->

<!-- TODO: place this in this section -->
I have just
started working with Carlos Maltzahn and Peter Alvaro on [declarative programmable
storage](declarative-storage), where there is some interest in continuing to use Ceph as the
underlying storage system implementation. 

Programmable storage tends to be a difficult, low-level task that requires lots of code and
detailed knowledge of storage subsystem implementations. Even when carefully written, storage
systems built on top of reusable components can still expose dependencies that make maintenance
prohibitively expensive. The idea of _declarative programmable storage_ (DPS), as [proposed by
Watkins, et al.][declstore-paper], is to use a declarative language for specifying interfaces over
storage systems such that maintainability and performance can be addressed by a query optimizer or
some other principled, automatic machinery.

<!-- TODO:
    * develop "mechanisms" more
        * not just quorum reads/writes
    * develop "a way to define and enforce..."
        * not just an IPA-like layer over DPS

    * develop "arbitrarily complex data properties"
-->

<!-- TODO: work in this text
    IPA, etc. allow developers to more correctly develop over *multiple* consistency models. This
    enables distributed systems to use stronger consistency when necessary.

    Doesn't this mean that the other direction is reasonable? IPA, etc. *enable* systems that
    wanted reilability first to gradually support more availability?

    I don't see why IPA, MixT, and QUELEA would only be useful for strengthening developing over
    weakly consistent systems? Now that these types of tools exist, doesn't it make it possible for
    developers who chose Ceph to transition in the other direction?

    I can't imagine it would be easy to go from Ceph to something like Cassandra, from an
    infrastructure perspective, or from a programmability perspective.
-->

For a DPS system, There are two major features that I explore to allow
developers specify consistency requirements over data types:
1. Mechanisms for supporting weaker consistency models in the backend storage system.
2. A way to define, and enforce, consistency requirements for data types.

There is an additional, related motive for studying mixing consistency over dps systems: exploring
the generalization of reasoning over arbitrarily complex data properties. From this perspective,
even beyond support for mixing consistency, this investigation will help guide the direction for future
work. To draw from MixT, which has a similar intuition, MixT claims that consistency is a property
of data. If this is at all true, then the storage system is a clear candidate for managing a
property such as consistency. If we minimally extend intuitions around consistency models to
general properties of data, being able to enforce, and reason over, pre- and post-conditions for
data seems useful.


Since Ceph has already served as a substrate for programmable storage work, we will consider using
Ceph as the backend for a declarative programmable storage system that supports mixing consistency
guarantees.

<!-- intro links -->
[cassandra-datastore]: http://cassandra.apache.org/
[postgres-dbms]: https://www.postgresql.org/docs/9.4/release-9-4.html

<!-- DPS links -->
[osd-doc]: http://docs.ceph.com/docs/mimic/man/8/ceph-osd/
[pg-docs]: http://docs.ceph.com/docs/mimic/rados/operations/placement-groups/
[mds-docs]: http://docs.ceph.com/docs/master/man/8/ceph-mds/

<!-- programmable storage links -->
[disorderlylabs]: https://disorderlylabs.github.io/
[maltzahn-website]: https://users.soe.ucsc.edu/~carlosm/UCSC/Home/Home.html
[programmable-storage]: http://programmability.us/
[noah-dissertation]: https://cloudfront.escholarship.org/dist/prd/content/qt72n6c5kq/qt72n6c5kq.pdf?t=pcfodf
[noah-zlog-impl]: https://github.com/cruzdb/zlog
[noah-blog-zlog]: https://nwat.xyz/blog/2014/10/26/zlog-a-distributed-shared-log-on-ceph/
[ceph-intro]: https://ceph.com/ceph-storage/
[ceph-intro-blog]: https://ceph.com/geen-categorie/ceph-storage-introduction/
[ceph-cuttlefish-arch]: http://docs.ceph.com/docs/cuttlefish/architecture/#how-ceph-scales
[ceph-fs-recommendation]: http://docs.ceph.com/docs/jewel/rados/configuration/filesystem-recommendations/#filesystems
[ceph-backend-bluestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#osd-backends
[ceph-backend-filestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#filestore
[data-center-faq]: http://docs.ceph.com/docs/cuttlefish/faq/#can-ceph-support-multiple-data-centers
[ceph-replication]: http://docs.ceph.com/docs/cuttlefish/architecture/#cluster-side-replication

[wiki-quorum]: https://en.wikipedia.org/wiki/Quorum_(distributed_computing)
[wiki-shim]: https://en.wikipedia.org/wiki/Shim_(computing)

<!-- paper links -->

<!-- paywalled papers:
    * [datamods-paper]: https://ieeexplore.ieee.org/document/6495800
    * [invivo-paper]: https://link.springer.com/chapter/10.1007/978-3-642-54420-0_3
-->

[ceph-paper]: https://www.ssrc.ucsc.edu/Papers/weil-osdi06.pdf
[crush-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-crush-sc06.pdf
[rados-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-rados-pdsw07.pdf

[datamods-paper]: https://users.soe.ucsc.edu/~jayhawk/watkins-pdsw12.pdf
[invivo-paper]: https://users.soe.ucsc.edu/~jayhawk/watkins-bdmc13.pdf
[mantle-paper]: https://dl.acm.org/citation.cfm?id=2807607
[malacology-paper]: https://dl.acm.org/citation.cfm?id=3064208
[declstore-paper]: https://www.usenix.org/conference/hotstorage17/program/presentation/watkins
[noah-dissertation]: https://cloudfront.escholarship.org/dist/prd/content/qt72n6c5kq/qt72n6c5kq.pdf?t=pcfodf

[fuzzylog-paper]: https://www.usenix.org/system/files/osdi18-lockerman.pdf

[proar-paper]: http://www.cs.nthu.edu.tw/~ychung/conference/ICPADS-2016.pdf
[ipa-paper]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt-paper]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[quelea-paper]: http://kcsrk.info/papers/quelea_pldi15.pdf
[bolton-paper]: http://www.bailis.org/papers/bolton-sigmod2013.pdf
[dynamo-paper]: https://dl.acm.org/citation.cfm?id=1294281
[ioflow-paper]: https://www.microsoft.com/en-us/research/wp-content/uploads/2013/11/ioflow-sosp13.pdf

<!-- consistency-types links -->
[course-website]: http://composition.al/CMPS290S-2018-09/
[rdt-svo]: https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/replDataTypesPOPL13-complete.pdf
[strong-enough]: http://software.imdea.org/~gotsman/papers/logic-popl16.pdf
[ipa-impl]: https://github.com/bholt/ipa/tree/master/src/main/scala/ipa
[mixt-impl]: https://github.com/mpmilano/MixT
[quelea-impl]: https://github.com/kayceesrk/Quelea
