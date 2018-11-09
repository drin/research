# Programmable Storage

## TODO
* Describe Ceph:
    * what is Ceph?
    * Why only strong consistency?
    * Why weaker consistency (Examples, examples, examples)?
    * How weaker consistency? (Also, Object Gateway?)
        * What granularity
        * How is it tunable
        * Quorum?

## Ceph

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
[data-center-faq]: http://docs.ceph.com/docs/cuttlefish/faq/#can-ceph-support-multiple-data-centers
[ceph-fs-recommendation]: http://docs.ceph.com/docs/jewel/rados/configuration/filesystem-recommendations/#filesystems
[ceph-backend-bluestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#osd-backends
[ceph-backend-filestore]: http://docs.ceph.com/docs/mimic/rados/configuration/storage-devices/#filestore
[ceph-cuttlefish-arch]: http://docs.ceph.com/docs/cuttlefish/architecture/#how-ceph-scales
