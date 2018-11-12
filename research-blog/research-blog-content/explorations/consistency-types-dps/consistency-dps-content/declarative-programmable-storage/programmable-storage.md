# TODO
* Describe Ceph:
    * what is Ceph?
    * Why only strong consistency?
    * Why weaker consistency (Examples, examples, examples)?
    * How weaker consistency? (Also, Object Gateway?)
        * What granularity
        * How is it tunable
        * Quorum?

# Programmable Storage

## [Ceph][ceph-intro]
Ceph is an open source, distributed, large-scale storage system. Some quotes that nicely summarize
Ceph:

* As described by Noah Watkins, [in his dissertation][noah-dissertation], "Ceph is something of a
  storage Swiss army knife."
* According to [Ceph's introduction blog post][ceph-intro-blog], "the main goals of Ceph are to be
  completely distributed without a single point of failure, scalable to the exabyte level, and
  freely-available."

Ceph has been part of storage systems research at UC Santa Cruz for several years under Carlos
Maltzahn, including the [CRUSH algorithm][crush-paper] (2006), the data store, [RADOS][rados-paper]
(2007), up to and including Noah's dissertation earlier this year (2018). I have just
started working with Carlos Maltzahn and Peter Alvaro on [declarative programmable
storage](declarative-storage), making Ceph a natural choice for investigating related, initial
research questions. While I am not personally experienced with Ceph, if I can provide a layer for
enforcing consistency types on top of Ceph, then Noah's [programmable storage work on top of
Ceph][noah-zlog] could benefit. Then, this carries over nicely into assessing the utility of
sequential, causal, or weaker consistency for a programmable storage system.

Ceph's architecture is designed around the
[**R**eliable **A**utonomous **D**istributed **O**bject **S**tore (RADOS)][rados-paper].
This data store is a unified system that provides storage interfaces for objects,
blocks, and files. A Ceph storage cluster consists of two types of daemons:

* Ceph Monitor
* Ceph **O**bject **S**torage **D**aemon (OSD)

The Monitor daemon maintains a master copy of [*the cluster map*](glossary.md#cluster-map)
including:

* cluster members
* state
* changes
* overall health of the storage cluster

*The cluster map* is a set of 5 maps that altogether represent the storage
cluster topology:

1. [Monitor Map](#monitor-map)
2. [OSD Map](#object-storage-daemon-map)
3. [**P**lacement **G**roup Map](#placement-group-map)
4. [**C**ontrolled **R**eplication **U**nder **S**calable **H**ashing Map](#controlled-replication-under-scalable-hashing-map)
5. [**M**eta **D**ata **S**oftware Map](#meta-data-software-map)

#### Monitor Map
A map of Ceph Monitor daemons to their:
* fsid
* position
* name address
* port
* current epoch
* creation timestamp (of the map)
* timestamp of last update (of the map)

#### **Object** **S**torage **D**aemon Map
A map of OSD damones to their:
* fsid
* creation timestamp (of the map)
* timestamp of the last update (of the map)
* list of pools
* replica sizes
* PG numbers
* list of OSDs and their status


#### **P**lacement **G**roup Map
A map containing:
* PG version
* PG timestamp
* last (previous?) OSD map epoch
* full ratios
* details on each placement group
* PG ID
* Up Set
* Acting Set
* PG State (e.g. active + clean)
* data usage statistics for each pool

#### **C**ontrolled **R**eplication **U**nder **S**calable **H**ashing Map
TODO



#### **M**eta **D**ata **S**oftware Map
TODO



### Ceph **O**bject **S**torage **D**aemon
The Ceph OSD relies upon the stability and performance of the underlying
filesystem[^osd-fs-fn] when using [the filestore
backend][ceph-backend-filestore]. The file system currently recommended for
production systems is XFS, although btrfs is supported. On the other hand, the
[new BlueStore backend][ceph-backend-bluestore] allows Ceph to directly manage
storage devices, bypassing the extra layer of abstraction that comes with the
use of kernel file systems (e.g. XFS, btrfs).

### Ceph Object Gateway and Eventual Consistency for Disaster Recovery
Ceph is able to [support multiple data centers][data-center-faq], but only
provides strong consistency. When a client writes data to Ceph the primary
OSD will not acknowledge the write to the client until the secondary OSDs have
written the replicas synchronously. Ceph [achieves
scalability][ceph-cuttlefish-arch] through "intelligent data replication."

The Ceph community is working to ensure that OSD/monitor heartbeats and peering
processes operate effectively with the additional latency that may occur when
deploying hardware in different geographic locations. See Monitor/OSD
Interaction for details.

If your data centers have dedicated bandwidth and low latency, you can
distribute your cluster across data centers easily. If you use a WAN over the
Internet, you may need to configure Ceph to ensure effective peering, heartbeat
acknowledgement and writes to ensure the cluster performs well with additional
WAN latency.

The Ceph community is working on an asynchronous write capability via the Ceph
Object Gateway (RGW) which will provide an eventually-consistent copy of data
for disaster recovery purposes. This will work with data read and written via
the Object Gateway only. Work is also starting on a similar capability for Ceph
Block devices which are managed via the various cloudstacks.

[^osd-fs-fn]: This is mentioned in [recommendations for the RADOS configuration][ceph-fs-recommendation]

[disorderlylabs]: https://disorderlylabs.github.io/
[maltzahn-website]: https://users.soe.ucsc.edu/~carlosm/UCSC/Home/Home.html
[programmable-storage]: http://programmability.us/
[noah-dissertation]: https://cloudfront.escholarship.org/dist/prd/content/qt72n6c5kq/qt72n6c5kq.pdf?t=pcfodf
[noah-zlog]: https://github.com/cruzdb/zlog
[ceph-intro]: https://ceph.com/ceph-storage/
[ceph-intro-blog]: https://ceph.com/geen-categorie/ceph-storage-introduction/
[ceph-cuttlefish-arch]: http://docs.ceph.com/docs/cuttlefish/architecture/#how-ceph-scales
[ceph-fs-recommendation]: http://docs.ceph.com/docs/jewel/rados/configuration/filesystem-recommendations/#filesystems
[ceph-backend-bluestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#osd-backends
[ceph-backend-filestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#filestore
[data-center-faq]: http://docs.ceph.com/docs/cuttlefish/faq/#can-ceph-support-multiple-data-centers
[rados-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-rados-pdsw07.pdf
[crush-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-crush-sc06.pdf
