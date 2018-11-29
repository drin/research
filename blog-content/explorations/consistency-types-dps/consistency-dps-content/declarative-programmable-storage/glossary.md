# Glossary
For conciseness in other areas, many definitions are provided here.

## Programmable Storage

### Ceph

##### OSD
[**O**bject **S**torage **D**aemon][osd-doc] is a daemon that is responsible for storing objects on
a local file system and providing access to them over the network.

#### PG
A [**P**lacement **G**roup][pg-docs] is a logical collection of objects that are replicated by the
same set of devices. An object's PG is determined by:
    * a hash of the object name
    * the level of replication
    * a bit mask, representing the total number of PGs in the system.

#### CRUSH
[**C**ontrolled **R**eplication **U**nder **S**calable **H**ashing][crush-paper] is a pseudo-random
data distribution algorithm that efficiently and robustly distributes object replicas across a
heterogenous, structured storage cluster[^crush-fn].

#### Metadata Server
The [**M**eta**d**ata **S**erver][mds-docs] (MDS) daemons maange the file system namespace.

#### Cluster Map
Set of 5 maps that represent the toplogy of the ceph storage cluster:

1. Monitor
2. [OSD](#osd)
3. [PG](#pg)
4. [CRUSH](#crush)
5. [MDS](#metadata-service)

#### fsid
A unique identifier for an OSD. The "fsid" term is used interchangeably with "uuid".

## Declarative Programmable Storage

[osd-doc]: http://docs.ceph.com/docs/mimic/man/8/ceph-osd/
[crush-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-crush-sc06.pdf
[pg-docs]: http://docs.ceph.com/docs/mimic/rados/operations/placement-groups/
[mds-docs]: http://docs.ceph.com/docs/master/man/8/ceph-mds/
[^crush-fn]: This is defined in the abstract and introduction of the [CRUSH paper][crush-paper]
