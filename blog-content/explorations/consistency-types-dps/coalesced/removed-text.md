# Removed Snippets
This file simply contains snippets that I have removed from the running draft, but would like to
keep around for reference.

<!-- -->
    In general, I tend to be interested in approaches to lifting various concepts to first-class
    citizens of a programming model. Of course, there is a lot of contextual important to be
    considered, but when I think of an application domain and how a problem might be addressed, I
    tend to wonder: is there a concept germane to the given domain such that considering the
    concept early on in development would make design and implementation more natural and concise.

    In the case of this blog post, consistency models are an important aspect of any distributed,
    important computation. So, how can we make reasoning about consistency of data types and
    computations natural during development? Distributed systems have been well studied for many
    years, and there now exist logical frameworks, tools, declarative approaches, and even type
    systems for bringing consistency models to the forefront of the developer's mental model. While
    this is the case, there are still data management and storage systems that do not include
    consistency models in their short list of design goals. Although, it may be fair to say that in
    this blog post, the considered approaches for mixing consistency in computations (I just say
    "mixing consistency" for conciseness hereafter), have happened in the last few years. So,
    "battle-hardened", production-capable systems were likely designed and began development before
    mixing consistency had been well explored. Specifically, the systems we consider in this blog
    post: [QUELEA][quelea-paper], [IPA][ipa-paper], and [MixT][mixt-paper], have all been released
    and published in the last 3 years.

<!-- -->
    For our high-level analysis of [mixing consistency](#mixing-consistency) to be meaningful, it
    is important to have _some_ understanding of what programmable storage is, and especially what
    a declarative programmable storage system is. In the above sections, background information is
    provided for both of these types of storage systems (really, it's just programmable storage,
    and then with a declarative layer on top). Part of the background information is the choice of
    backend data store for implementing a consistency type system on top of, and some of the
    motivations behind various trade-offs we might be interested in.

    Just to further anchor the analysis in this blog post (spoiler alert), the existing consistency
    type system implementations we choose to cover were discussed as part of [CMPS
    290S][course-website]. The analysis is provided in the section, [Mixing
    Consistency](#mixing-consistency). Background for understanding what consistency even is, and
    what consistency models are, can be found in these sections (TBD later, since this knowledge is
    assumed for the class, CMPS 290S):

<!-- -->
    For convenience (hopefully it's also useful), there is a [glossary](#glossary) at the end of this
    blog post.

    Due to time constraints (and perhaps the nature of my goal), this blog post will **only** cover the
    relevant implementations of using mixed consistency computations, and not the actual implementation
    of such a programming model in (or on top of) a dps system. However, the implementation is
    something that will be addressed in a follow up blog post in the next 3 - 4 weeks (and I cross my
    fingers for interesting results).

<!-- -->
    While I am not personally experienced with Ceph nor have I interacted with users of Ceph, the idea
    of allowing the developer to mix consistency on top of a storage system seemed interesting enough
    to explore. Especially, when considering that Ceph is distributed over a cluster (and potentially
    replicated to remote clusters).

<!-- -->
    Ceph's architecture is designed around the [RADOS data store](#rados). This data store is a unified
    system that provides storage interfaces for objects, blocks, and files.


<!-- -->
    Mentioned in [recommendations for the RADOS configuration][ceph-fs-recommendation], the osd
    relies upon the stability and performance of the underlying filesystem[^osd-fs-fn] when using
    [the filestore backend][ceph-backend-filestore]. The file system currently recommended for
    production systems is XFS, although btrfs is supported. On the other hand, the [new BlueStore
    backend][ceph-backend-bluestore] allows Ceph to directly manage storage devices, bypassing the
    extra layer of abstraction that comes with the use of kernel file systems (e.g. XFS, btrfs).

    While understanding Ceph in general is useful, the aspect that is relevant for what we want to
    explore in this blog post, is how Ceph replicates data, and what type of consistency is
    available to developers and users. Well, 

<!-- -->
    Since our class, [CMPS290S][course-website], has been spending a lot of time reading about
    consistency models up to this point, I will fill this section in later and assume that initial
    readers of this blog post are well acquainted with the relevant consistency models.

<!-- -->
    A rushed type represents latency bounds in which an answer is expected. For a latency bound of
    2 seconds, IPA would return a value meeting the strongest consistency constraints within 2
    seconds. If strong consistency could be achieved for the operation in 1 second, then that value
    would be preferred to a returned value that is only weakly consistent. When the latency
    threshold is reached, it may be possible that a value only satisfying weak consistency is
    available, and thus that would be returned.
