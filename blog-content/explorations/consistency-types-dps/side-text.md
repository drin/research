

<!-- TODO: look at this later
In contrast to IPA's approach, [MixT's implementation][mixt-impl] is in C++ and much lower in the
development stack. Another interesting difference is that the backend data store used is
Postgres. What makes this interesting is that Postgres (to my understanding) supports strong
consistency, but various levels of *isolation*. MixT allows weaker consistencies by providing a
**d**omain **s**pecific **l**anguage (DSL) for defining computation in a *mixed-consistency
transaction*. Operations within these mixed-consistency transactions are then split into smaller
transactions to achieve weaker consistency.
-->

<!--
Initially, I thought that building a consistency type system on top of a data store
that *already supports* weaker consistency models seemed more natural.

Grouping operations (or enforcing
operation constraints) seemed easier to reason about than slicing operations into sub-groups, or
into isolated operations.

Operationally, this makes MixT a very different approach to enforcing
consistency types.


At a glance, this approach seems like it should be easier to build on top of
Ceph than IPA due to the lack of support for weaker consistency.

However, while the similarity between Ceph and Postgres providing strong consistency by default
seems like something that would make MixT applicable for being used on top of Ceph, it is not clear
that mixed-consistency transactions will be able to achieve weaker consistency on top of Ceph. I
suspect that support for weaker levels of isolation in Postgres enables MixT to support weaker
consistency by breaking up a transaction into smaller transactions. I may be misunderstanding
MixT's implementation, but it seems that weaker isolation is the only way to achieve weaker
consistency if the data store does not explicitly support weak consistency models. Before
attempting to architect an approach that layers MixT on top of Ceph, it will be important to
understand the effect of isolation levels on mixed-consistency transactions. 

It will also be important to understand whether transaction splitting has the desired semantics
over Ceph, given that Ceph does not seem to support transactions (though they are requested? It is
hard to tell when they were requested and if they were ever completed).

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

<!-- TODO:
    * develop "mechanisms" more
        * not just quorum reads/writes
    * develop "a way to define and enforce..."
        * not just an IPA-like layer over DPS

    * develop "arbitrarily complex data properties"
-->

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

<!--
There is an additional, related motive for studying mixing consistency over dps systems: exploring
the generalization of reasoning over arbitrarily complex data properties. From this perspective,
even beyond support for mixing consistency, this investigation will help guide the direction for future
work. To draw from MixT, which has a similar intuition, MixT claims that consistency is a property
of data. If this is at all true, then the storage system is a clear candidate for managing a
property such as consistency. If we minimally extend intuitions around consistency models to
general properties of data, being able to enforce, and reason over, pre- and post-conditions for
data seems useful.
-->
