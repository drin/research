# TODO
* Describe Ceph:
    * what is Ceph?
    * Why only strong consistency?
    * Why weaker consistency (Examples, examples, examples)?
    * How weaker consistency? (Also, Object Gateway?)
        * What granularity
        * How is it tunable
        * Quorum?

## Programmable Storage

## [Ceph][ceph-intro]
Ceph is an open source, distributed, large-scale storage system. As described
by Noah Watkins [in his dissertation][noah-dissertation], "Ceph is something of
a storage Swiss army knife." Ceph's architecture is designed around the
**R**eliable **A**utonomous **D**istributed **O**bject **S**tore (RADOS). This
data store provides object, block, and file system storage in a unified storage
cluster. The backend for RADOS is the **O**bject **S**torage **D**aemon, which
runs on each node in the Ceph RADOS cluster.

### Why Ceph?
In the near future, I will be working on research related to [Noah's
dissertation][noah-dissertation], and he had chosen to do his research on
programmable storage on top of Ceph. So, for this blog post, the choice of Ceph
is to minimize time to ramp up on other technologies. While I am not personally
experienced with Ceph, if I can provide a layer for enforcing consistency types
on top of Ceph, then Noah's [work on top of Ceph][noah-zlog] could be made to
benefit. This then carries over nicely into a clear path for evaluation and
understanding if adding features for sequential, causal, or weaker consistency
is useful for a programmable storage system.

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
[ceph-cuttlefish-arch]: http://docs.ceph.com/docs/cuttlefish/architecture/#how-ceph-scales
[ceph-fs-recommendation]: http://docs.ceph.com/docs/jewel/rados/configuration/filesystem-recommendations/#filesystems
[ceph-backend-bluestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#osd-backends
[ceph-backend-filestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#filestore
[data-center-faq]: http://docs.ceph.com/docs/cuttlefish/faq/#can-ceph-support-multiple-data-centers
