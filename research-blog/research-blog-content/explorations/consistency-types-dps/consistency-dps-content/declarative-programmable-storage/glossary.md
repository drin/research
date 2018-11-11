# Glossary
For conciseness in other areas, many definitions are provided here.

## Programmable Storage

### Ceph
OSD:    [**O**bject **S**torage **D**aemon][osd-doc] is a daemon that is
        responsible for storing objects on a local file system and providing
        access to them over the network.

CRUSH:  [**C**ontrolled **R**eplication **U**nder **S**calable **H**ashing][crush-paper]
        is a pseudo-random data distribution algorithm that efficiently and
        robustly distributes object replicas across a heterogenous, structured
        storage cluster[^crush-fn].

Cluster Map:    Set of 5 maps that represent the toplogy of the ceph storage
                cluster: (1) Monitor, (2) OSD, (3) PG, (4) CRUSH, (5) MDS.

fsid:   A unique identifier for an OSD. The "fsid" term is used interchangeably
        with "uuid".

MDS:    Ceph metadata software or metadata management service

## Declarative Programmable Storage

[osd-doc]: http://docs.ceph.com/docs/mimic/man/8/ceph-osd/
[crush-paper]: https://ceph.com/wp-content/uploads/2016/08/weil-crush-sc06.pdf
[^crush-fn]: This is defined in the abstract and introduction of the [CRUSH paper][crush-paper]
