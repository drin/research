Thoughts
--------

A summary of my thoughts from the meeting or things that were said that I thought were interesting.

* physical design management - Is it ideal for our evaluation to compare a
  dbms to a dbms over storage system:

    * dbms has a built-in storage manager that perhaps physical design
      parameters can be communicated with
    * does the underlying storage system provide a physical design interface

* object storage:

    * splits the front-end of file system (namespace) from back-end (data
      storage)
    * how can you take as much as possible from file system to a distributed
      architecture to efficiently scale
    * namespace provides global view of the system (data?)
    * when you have an object store, you can then extend it independently
      from namespace
    * physical design management - we need "these things" and here is how we
      implement in object storage

* transformations:
    * why are they important
    * ways to describe/term "layout" and "organization"

* physical design management:
    * physical design is:
        * the format of how the data is stored
        * what are the indexes; are there indexes
        * physical design management looks at physical design across multiple data stores

    * object storage is one way to introduce

* related work:
    * physical design tuning
    * in-storage compute (active storage, samsung's work, kd-drive-toshiba/seagate, kinetic-drive)
    * file support (support in firmware)
    * custom file systems (teradata, oracle, etc.)

* The Visualization Toolkit: An Object-Oriented Approach to 3D Graphics (Schroeder - Kitware 2006):
    * Basic Data Representation - The Dataset
