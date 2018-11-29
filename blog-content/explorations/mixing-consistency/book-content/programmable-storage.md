## Programmable storage

Across all fields of computing, data storage is extremely important. In fact, even abstract models
of computation require the concept of _tape_, as an infinite, contiguous sequence of locations that
can store symbols. It likely isn't surprising that significant improvements in storage devices have
huge impacts for many areas of computing. However, hardware improvements have not always come fast
enough. As application requirements for storage have grown, storage systems have grown more
complex. To accommodate web-scale and high-performance applications, storage systems have become
distributed, and spanned many storage devices.

The performance of a storage system has significant impact on the design and implementation of
applications that communicate with it -- consider HDFS and cloud services such as Amazon's S3. In
the case of HDFS, knowledge of how it partitions files or caches data can help the application
programmer make choices to reduce latency or increase throughput. And so, as applications increase
in both complexity and concurrency, there is an increasing need to extract better performance from
the storage system.  In addition to growing application needs, there are also improvements in
hardware that storage systems have yet to make use of. Due to reliability needs, storage systems
must be well tested and extensively exercised.  On the other hand, due to increasing application
requirements and the rate at which underlying hardware is improving, it is necessary to iterate
quickly, or to periodically re-design various subsystems. Finally, there are a variety of storage
devices to tune for, and that can significantly affect software system design and implementation.

_Programmable storage_ is an approach to developing storage interfaces, pioneered by storage
systems researchers at UC Santa Cruz, that emphasizes programmability and reusability of storage
subsystems to address these challenges.  Due to the inherently high reliability expectations of
storage systems, the programmable storage approach discourages rewriting storage subsystems or
components, because this only invites younger, error-prone code. The intuition is that reusing
subsystems of a storage system means that the community supporting these subsystems is larger, and
these subsystems are exercised and improved more frequently. The [Malacology][malacology-paper]
programmable storage system is an interface built on top of the [Ceph](https://ceph.com/) storage
stack.  Malacology abstracts storage subsystems into building blocks that can be combined to more
easily build a service on top of the storage system.

Ceph is an open source, distributed, large-scale storage system that aims to be "[completely
distributed without a single point of failure, scalable to the exabyte level, and
freely-available][ceph-intro-blog]." Ceph has been part of storage systems research at UC Santa
Cruz for over a decade, from [the original Ceph paper][ceph-paper] (2006), the [CRUSH
algorithm][crush-paper] (2006) and the [RADOS][rados-paper] data store (2007), to [Noah Watkins's
recent dissertation][noah-dissertation] on programmable storage built on top of Ceph.

Although Ceph is a distributed, large-scale storage system, it was designed to fill the role of
reliable, durable storage. This expectation is common (and preferred) for many applications,
especially scientific applications, where the complexity of weaker consistency models is too
difficult to work with. This makes Ceph's support for only strong consistency, via
[primary-copy][ceph-replication] replication, reasonable. However, the trade-off between strong
consistency and availability or performance is very important for some [Dynamo-like][dynamo-paper]
applications. For Ceph to support these types of applications, it would need to offer weaker
consistency as an option.  Recent work on a weak consistency model for Ceph, [PROAR][proar-paper],
has been published by researchers at the Graduate School at Shenzhen, Tsinghua University.

Further building up our motivating example, we would like to consider extensions to
[ZLog][noah-blog-zlog], an distributed shared log developed on top of Malacology. ZLog is an
implementation of the [CORFU][corfu-presentation] strongly-consistent shared log. If Ceph (and
Malacology on top of it) supported multiple consistency levels, then ZLog could as well. It would
be interesting to compare FuzzyLog to a mixed-consistency version of ZLog built on Malacology.
Using an approach in the spirit of QUELEA, MixT, or IPA for mixing consistencies would align well
with the programmability aspect of programmable storage.

