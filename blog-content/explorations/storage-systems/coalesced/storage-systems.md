# Overview

TBD

# File Formats

Inspired by the [Albis][paper-albis] paper, one of the things my research group is interested in is
how file formats are an old standard that may hinder progress to future storage architectures.
Generally, file formats represent storage format and data layout (though, with some delay involved
in defragging, etc. the data).

Part of what we would like to do is find some way to bring insights found in Albis together with
the general ideas found in the [Boxwood][paper-boxwood] paper.

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

<!-- references -->

<!-- papers -->
[paper-albis]: https://www.usenix.org/conference/atc18/presentation/trivedi
[paper-boxwood]: https://dl.acm.org/citation.cfm?id=1251262

<!-- web -->
[web-hdf5-intro]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5
[web-hdf5-format]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-fileformat
[web-hdf5-model]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-datamodel
[web-hdf5-software]: https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5#IntroductiontoHDF5-software
