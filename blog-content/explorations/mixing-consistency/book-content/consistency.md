## Data consistency

In a distributed system where data is copied between multiple servers, keeping the copies
consistent with each other may not always be possible. For instance, consider a small system where
each _data object_ has two copies (Copy<sub>1</sub> and Copy<sub>2</sub>), distributed over two
servers. What if you're accessing Copy<sub>1</sub> and it is somehow different from
Copy<sub>2</sub>?

A _consistency model_ defines the ways in which Copy<sub>1</sub> and Copy<sub>2</sub> may disagree.
Informally, _strong consistency_ means that all clients agree on the order that operations on a
data object appear.  Under _eventual consistency_, on the other hand, copies of a data object may
appear different at any point, but given enough time without updates, all copies will converge to
the same state. In a hybrid consistency model, such as [RedBlue consistency][redblue-paper],
individual operations on data objects may be strongly consistent (red) or eventually consistent
(blue). Red operations must be ordered with respect to each other, while blue operations may be in
any order (and must commute).

In some contexts, such as in the [MixT][mixt-paper] and [IPA][ipa-paper] programming models,
consistency is considered a property of the _data_ being operated on, rather than a property of the
operations themselves. Since ADTs are defined by the operations that can be invoke on them, though,
these two points of view are necessarily difficult to disentangle. 

## Mixing consistency

For developers working in distributed systems, it can be cumbersome to think about whether the
correct consistency guarantees are being satisfied, especially when building on storage systems
that support a mixture of consistency levels. In the past few years, there have been a variety of
language-level abstractions that support reasoning about mixtures of consistency guarantees.

### Inconsistent, Performance-bound, Approximate (IPA) programming

[IPA][ipa-paper] provides a _consistency type system_ that makes consistency models explicit in
types.  Developers are able to verify that important data types are used at an appropriate
consistency level. This type of support from the programming model is useful for developers to be
both more efficient and more correct. IPA is motivated by taking a principled approach to the
trade-off between consistency and performance.

There is [a prototype implementation of IPA][ipa-impl] in Scala on top of Cassandra. Scala's
powerful type system allows for ergonomically deconstructing consistency types. This approach
allows the developer to directly interact with the consistency type of their data, using features
such as pattern matching. IPA allows consistency guarantees to be specified as a policy on an ADT
in two ways:

  1. _Static consistency policies_ allow specification of a consistency model (e.g., strong, weak,
     causal) that can be enforced by the data store.
  2. _Dynamic consistency policies_ allow specification of performance or correctness bounds within
     which to achieve the strongest consistency possible, and which require additional runtime
     support to enforce.

Static consistency policies can be implemented relatively straightforwardly on top of the data
store. For this particular implementation, consistency levels for data store operations are
determined via Cassandra's use of quorum reads and writes.

Dynamic consistency policies are specifications of performance or behavior properties, within which
the strongest consistency constraints should be satisfied. More concretely, IPA provides two
dynamic consistency types: rushed types and interval types. The dynamic consistency types are out
of scope for this blog post, but they are a very interesting aspect of the IPA paper.

### MixT: consistency enforced with information flow

[MixT][mixt-paper] is a domain-specific language that aims to keep consistent computations
"untainted" by inconsistent computations. Because the use of inconsistent data can weaken
computations that are expected to be strongly consistent, MixT takes an information flow approach
to preventing unsafe, or unexpected, interactions between computations running at different
consistency levels.  Furthermore, MixT offers support for _mixed-consistency transactions_ that
execute in separate phases depending on the consistency level of the operations therein.

### QUELEA: declarative programming over eventually consistent data stores 

[QUELEA][quelea-paper] takes a declarative programming approach that allows developers to specify
constraints on a data store and on the operations that interact with it. Programmers can annotate
operations with _contracts_ that enforce application-level invariants, such as preventing a
negative bank account balance. An SMT-based _contract classification_ system analyzes these
contracts and automatically determines the minimum consistency level at which an operation can be
run, simplifying reasoning about consistency from the developer's perspective. Further, QUELEA
supports transactional contracts even when the backend datastore does not.

## Mixing consistency in programmable storage

The typical IO path to Ceph's storage cluster does not support consistency models other than strong
consistency. To support weaker consistency models in our programmable storage system, we would need
to build support for a range of consistency models directly in Ceph. That, in turn, would motivate
the need for a language-level abstraction to help programmers deal with the resulting "consistency
zoo".

To support an IPA-like system on top of Ceph with as little modification as possible, it would be
useful to add a quorum interface to Ceph. Ceph's RADOS datastore uses OSDs (object storage daemons)
for data persistence. By communicating with these OSDs directly, rather than through a RADOS
gateway (RGW), it may be possible to provide a quorum interface in Ceph. Understanding the details
of OSD communication will be important for understanding whether Ceph can provide a similar
interface to IPA as what Cassandra provides.

MixT, being built on top of Postgres, describes an alternate possible mechanism for supporting weak
consistency in a programmable storage system. To enable causal consistency on top of Postgres,
Milano et al. replicated data over several Postgres servers. Clients were then partitioned to
separate servers and each server was configured to use snapshot isolation for transactions. Version
numbers for each row allowed operation ordering across servers, and vector clocks used
microsecond-resolution wall clock time. If we encode versions and vector clocks in data stored in
Ceph OSDs, it may be possible to simply treat each OSD as a replica server as MixT does with
Postgres, hence enabling causal consistency in Ceph.

The implementation of QUELEA, like IPA, uses Cassandra as the backend data store.  It uses a
["bolt-on"][bolton-paper]-style mechanism implemented in a
shim layer on top of the data store to enable causal consistency on top of Cassandra. Because
QUELEA requires programmers to specify contracts on operations that interact with the store and
ensures that store operations happen at a consistency level that satisfies those contracts, it
seems that implementing QUELEA on top of Ceph may benefit from the ability to communicate with Ceph
OSDs individually, just like IPA.

[ipa-paper]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt-paper]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[quelea-paper]: http://kcsrk.info/papers/quelea_pldi15.pdf
[bolton-paper]: http://www.bailis.org/papers/bolton-sigmod2013.pdf
[redblue-paper]: https://www.usenix.org/system/files/conference/osdi12/osdi12-final-162.pdf

[ipa-impl]: https://github.com/bholt/ipa/tree/master/src/main/scala/ipa
