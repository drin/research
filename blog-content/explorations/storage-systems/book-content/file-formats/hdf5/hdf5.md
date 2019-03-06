# HDF5
HDF5 is a set of tools for storage of complex data. It consists of: a [file
format][web-hdf5-format], a [data model][web-hdf5-model],
and [software for working with the file format][web-hdf5-software] (See [intro to
HDF5][web-hdf5-intro]).

We are generally interested in HDF5 because it is popular for some scientific use cases and
becoming increasingly popular. Conveniently, it also serves an interesting role in data storage
that has some similarities to programmable storage.

There is an abstraction layer, the virtual object layer (VOL), in the HDF5 library that intercepts
API calls and forwards these calls to "object drivers" for the storage system underlying the HDF5
file being accessed. The motivation for programmable storage is to allow access to storage
subsytems through an interface alternate to the POSIX API. In some ways, the VOL has the same role,
allowing interactions with HDF5 files through the HDF5 library to communicate more directly with
storage systems (and potentially storage subsystems) than the standard POSIX interface allows.

A simple implementation of a declarative programmable storage system, then, may be something like
a declarative interface to the VOL, which then is able to communicate information with the
underlying programmable storage system in a way that allows for optimizations of data
placement, data access paths, data processing, and other intelligent operations pushed down to
lower abstraction levels.

# VOL

# RADOS
<!-- references -->

[rados-setupscript]: https://github.com/xweichu/object_size/blob/master/setup.sh
[rados-build]: http://docs.ceph.com/docs/master/install/build-ceph/
[rados-librados]: https://github.com/ceph/ceph/tree/master/src/librados
[rados-objstore]: https://github.com/ceph/ceph/blob/0e95a2893dbd257d6bc45ae2f154e405370756c5/src/os/ObjectStore.h

<!-- references -->
[web-hdf5-intro]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5
[web-hdf5-format]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-fileformat
[web-hdf5-model]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-datamodel
[web-hdf5-software]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-software
