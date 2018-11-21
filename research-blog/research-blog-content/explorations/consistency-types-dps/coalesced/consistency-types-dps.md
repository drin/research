---
title: Mixing Consistency in a Declarative Programmable Storage System
author: Aldrin Montana
layout: single
classes: wide
---

by Aldrin Montana &middot; edited by Abhishek Singh and Lindsey Kuper

<!-- ------------------------------>
<!-- SECTION -->
## Introduction
Modern distributed applications call for ways to store and access data using a range of consistency
guarantees.

<!-- TODO: example -->

Recent systems such as [QUELEA][quelea-paper], [IPA][ipa-paper], and [MixT][mixt-paper] aim to make
programming in a mixed-consistency world safer and easier.

<!-- TODO: brief descriptions of implementations -->

In this blog post, we explore approaches for specifying a range of consistency guarantees in a
_declarative prgorammable storage_ (DPS) system.


<!-- ------------------------------>
<!-- SECTION -->

# Programmable Storage
Programmable storage is an approach to developing storage interfaces, coined by storage systems
researchers at UC Santa Cruz, that emphasizes programmability and reusability of storage
subsystems.


<!-- TODO: develop motivation for programmable storage? -->
business analytics, scientific simulations, etc.
    -> application requirements push the limits of high-performance storage

application complexity and concurrency grow
    -> increased pressure on developers from existing I/O interfaces

because next gen OSS storage systems being developed (2012)
    -> good time for architectures and abstractions for domain specific interfaces

majority of storage systems assume a byte-stream I/O interface
    -> applications unable to represent domain-specific data models
    -> led to:
        -> middleware libraries providing data model abstractions
            -> HDF5, NetCDF
        -> I/O stack extensions that circumvent scalability bottlenecks
            -> MPI-IO, PLFS, IOFSL
    -> co-design of applications and middleware ends at file interfaces
        -> services forced into one-dimensional abstraction that is difficult to scale:
            * metadata management
            * data translation
            * alignment
            * views

- Many of the services implemented by middleware libnraries already exist in distributed storage systems -

Mantle
    -> introduce a programmable storage system that lets the designer inject custom balacning logic
    -> flexibility and transparency of approach
        -> replicate strategy of state-of-the-art metadata balancer
        -> compare strategy to other custom balancers on same system


Due to reliability needs, storage subsystems are robust and well tested. Some complex
reliability features that all good storage systems provide are disaster recovery, durability, and
data redundancy. To address modern availability and maintenance needs, large-scale, distributed
storage systems must also provide replication and strong consistency.

Storage systems have inherently very high expectations of reliability, so programmable storage
discourages rewriting storage subsystems or components, because this only invites younger,
error-prone code. The intuition is that reusing subsystems of a storage system means that the community
supporting these subsystems is larger, and these subsystems are exercised and improved more
frequently.


<!-- TODO: tie it back to work at UCSC -->
<!--
    * DataMods
    * In-Vivo
    * Mantle
    * Malacology
    * Noah's Thesis
-->
[Recent work by Noah Watkins][noah-dissertation] on programmable storage shows it to be
a viable approach to storage systems development in the future.

"exposes internal services and abstractions of the storage stack as building blocks for
higher-level services"

"A programmable storage system exposes internal subsystem abstractions as “interfaces” to enable
the creation of higher-level services via composition"

<!-- TODO:
    * develop malacology interface
    * develop ZLog
        * find some way to allude to possibly comparing to FuzzyLog
    * generalize programmable storage interface
    * highlight the parts we think can benefit from this work
-->

In his dissertation, Noah details his implementation of a distributed, shared log on top of the
[Ceph][ceph-intro] storage system, which he also described [in a blog post][noah-blog-zlog]. Since
Ceph has already dserved as a substrate for programmable storage work, we will consider using Ceph
as the backend for a declarative programmable storage system that supports mixing consistency
guarnatees.


## Ceph
Ceph is an open source, distributed, large-scale storage system that aims to be, "[completely
distributed without a single point of failure, scalable to the exabyte level, and
freely-available][ceph-intro-blog]." Ceph has been part of storage systems research at UC Santa
Cruz from [the original Ceph paper][ceph-paper] to the [CRUSH algorithm][crush-paper] (2006) and the
[RADOS][rados-paper] data store (2007); [Noah's recent dissertation][noah-dissertation] on
programmable storage also involved building on top of Ceph.

Considering Noah's previous work on ZLog, it may be interesting to evaluate the addition of mixed
consistency guarantees using [recently published work on FuzzyLog][fuzzylog-paper]. Because Ceph
does not currently support consistency guarantees weaker than strong consistency, with the addition
of weaker consistency guarantees, an implementation of FuzzyLog may be possible.

Although Ceph is a distributed, large-scale storage system, Ceph was designed to fill the role of
reliable, durable storage. This expectation is common (and preferred) for many applications,
especially scientific applications, where the complexity of weak consistency models is too
difficult to work with. This makes Ceph's support for only strong [primary-copy consistency
model][ceph-replication] reasonable. Recent work on a weak consistency model for Ceph,
[PROAR][proar-paper], has been published by researchers at the Graduate School at Shenzhen,
Tsinghua University. [Some applications][dynamo-paper], however, prefer to trade strong consistency
for availability and performance. For Ceph to support these types of applications, it would need to
    offer weaker consistency as an option. 

<!-- TODO (narrative):
    once ceph supports weaker consistency, we want to make it easy to program against
-->

A Ceph storage cluster consists of two types of daemons:
* Ceph [**O**bject **S**torage **D**aemon][osd-doc] (OSD)
* Ceph Monitor

A Ceph OSD is responsible for storing objects on a local file system and providing access to them
over the network. The OSD is part of the RADOS ([**R**eliable **A**utonomous **D**istributed
**O**bject **S**tore][rados-paper]) data store, which is the backend subsystem of Ceph that handles
distributed data storage.

The Ceph Monitor monitors the Ceph storage cluster, while the Ceph OSD handles data persistence on
a node in the Ceph storage cluster. One or more Monitors form a Paxos cluster that manage cluster
membership, configuration, and state. The primary responsibility of the monitor service is to
maintain a master copy of the cluster map. The cluster map represents the topology of the ceph
storage cluster by the use of 5 maps over the following services:
1. Monitor
2. OSD
3. [**P**lacement **G**roup][pg-docs] (PG)
4. [**C**ontrolled **R**eplication **U**nder **S**calable **H**ashing][crush-paper] (CRUSH)
   algorithm
5. [**M**eta**d**ata **S**erver][mds-docs] (MDS)

A PG logically represents the objects replicated by a particular set of devices. The PG for an
object depends on: the hash of the object name, the replication factor (number of replicas to
replicate to), and a bit mask for the total number of PGs.

The abstract of the CRUSH paper defines CRUSH as a "pseudo-random data distribution algorithm that
efficiently and robustly distributes object replicas across a heterogenous, structured storage
cluster."

The MDS manages the file system namespace.

<!-- TODO:
    * explain intelligent replication
    * simplify "heartbeats and peering processes"
-->
Ceph is able to [support multiple data centers][data-center-faq], but only provides strong
consistency. When a client writes data to Ceph, the primary OSD will not acknowledge the write to
the client until the secondary OSDs have written the replicas synchronously. Ceph [achieves
scalability through what it calls "intelligent data replication."][ceph-cuttlefish-arch] For
hardware deployed in differenge geographic locations, this will lead to additional latency in the
time to receive synchronous acknowledgements. Considering the possible (likely) latency, The Ceph
community is working to ensure that OSD/monitor heartbeats and peering processes still operate
effectively.

In the meantime, one must either use a single data center, or configure Ceph in a way that ensures
effective peering, heartbeat acknowledgement and writes. According to [Ceph's
faq][data-center-faq], there is an asynchronous write capability in progress via the Ceph Object
Gateway (RGW) which would provide an eventually-consistent copy of data for disaster recovery
purposes. However, this would only work with reads and writes sent via the Object Gateway. There is
also similar capability for Ceph Block devices which are managed via the various cloudstacks.
Unfortunately, it is not clear what the progress is on these capabilities, and the proposed use
cases sound particular to disaster recovery and not performance. This leaves some potentially
interesting work to be done on extending Ceph to support weaker consistency.


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

<!-- ------------------------------>
<!-- SECTION -->
# Consistency

<!-- TODO:
    * for sure define and describe consistency here.
-->

<!-- TODO: fill this in later -->
## Consistency Models
Consistency is an interesting property of data. We usually define an **A**bstract
**D**ata **T**ype (ADT) by the operations that we can invoke on them. From this
perspective, not only is consistency a property of data--does every client in
my system agree on the state of this data value--but it is inherently affected
by operations. The more complete our information about a data type is, the
easier it is to know if the relevant data value is *correct*. When we consider
this property in a distributed system, complete information becomes more
difficult to accumulate **efficiently**. So, we define correctness of our data
in a distributed system, as constraints with respect to complete, relevant
information. This definition of correctness given some set of constraints, is
what we call a *consistency model*. That is, given information about our *way
of thinking about consistency*, is a data value, or the state of a data type,
**consistent**.

In this section, we describe formal definitions of various consistency models
and intuitions for what they mean. We will also define terminology as
accessibly as possible in the [glossary](#glossary). Ultimately, formal
definitions of consistency models are necessary for distinguishing between them
and reasoning over them.

<!--
To be added when I have more time? Otherwise this looks too incomplete.

#### Linearizabilty
#### Sequential Consistency
#### Causal Consistency
#### Eventual Consistency
#### Weak Consistency
-->

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
