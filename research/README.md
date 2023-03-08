# Research Overview

One aspect of my research is to figure out how to allow scientists to express queries so that they
can access and process their data in a way that allows the storage system and storage devices to
optimize more freely. This means: (1) we **need** scientists to be **more** declarative in how they
access data and (2) we **need** to provide a data access model and query interface that aligns well
with system performance regardless of what optimizations the system may make. More specifically,
for the second objective, we want to avoid scenarios where users feel compelled to learn system
internals in order to improve performance.


## Data management landscape and our niche

Data management and processing is a vast field and/or topic. If we consider any particular
end-to-end system, there can be many systems used to fill specialized roles; on the other
hand, there may also be fewer, generalized systems to satisfy the same needs. Here, I want
to elaborate on a particular ecosystem that I am trying to fit into and how I view our
role or niche in relevant end-to-end systems.

For my purposes here, I think of an ecosystem as a particular set of interchangeable
systems that can be integrated to satisfy a purpose. Any particular composition of such
systems would be an end-to-end system. Each system therein could stand on it's own, but if
it is specialized to the point of depending on other systems, then it may also be
considered a sub-system.

Although I am trying to re-use as many existing technologies as I can, I imagine that I
want to have a "SQL-like" interface that is familiar to database developers but expressive
for the user's application domain.


## Projects

For implementation and usability reasons, I have defined my research into a few projects.
Each project captures some aspect that others may want to leverage for their own research,
or that I may want to swap for other sub-systems or deploy separately.

For now, I have three defined projects:
1. `Skytether`--A computational storage system that extends the core concepts of SkyhookDM
   to accommodate computational storage devices (CSx)

2. `Mohair`--A query processing library or subsystem that can split a query plan up to be
   executed throughout a storage hierarchy (distributed system) and communicates the query
   plan (and its portions) using substrait.

3. `MSG Express`--A domain-specific application that sits on `Skytether` to bridge
   analysis and management of single-cell gene expression data with computational storage.


### Skytether

Originally, I was trying to determine a reasonable name for my project that was relevant to
`SkyhookDM`. My work was initially imagined as something that would sit on top of--or live as an
extension of--`SkyhookDM`. For this reason, I have been calling my project `Skytether` (meant to
have the connotation of a particular part of a [skyhook][wiki-skyhook]). For a first name, it
seemed reasonable.

Now, beyond names, most of the code that I have written is meant to eventually integrate with
[SkyhookDM][web-skyhookdm], providing the necessary functionality to be able to delegate some
processing to computational devices that a computational storage server may be using. Similarly to
how `SkyhookDM` brings tabular data management to an existing storage system, `Ceph`, I imagine
that `Skytether` would bring the use of **computational storage devices** to an existing
**computational storage system**, `SkyhookDM`.


### Mohair

##### Query Lifecycle

The query lifecycle starts with constructing a query plan from a query expression. How queries will
be expressed is currently undecided as well as how a query plan will be represented. Once a query
plan is constructed, it is then optimized to try and run as much processing close to the data as
possible without overloading or losing end-to-end latency. The optimized query plan is then
executed and the result persisted in storage or propagated to the user. During query execution, the
plan may be altered dynamically to allow for runtime constraints to be accommodated.

**Query Plans.** Mohair will take some form of input (SQL-like expression or something
lower level) and construct a query plan that can be communicated to other processes that
use Mohair (or that understand substrait; possibly with Mohair-specific extensions. At a
high level, this means that a query plan could be arrow compute engine code or it could be
a higher level representation. In either case, the final output to communicate for query
execution will be a query plan with **initial** optimization passes. A simple example of
an optimization pass may be to push predicates down the query plan to the leaves (source
nodes).

**Query Execution.** Mohair will use the `Acero` to execute queries on data. While the
arrow compute engine provides mechanisms for execution of relational operators, Mohair
will manage incremental execution of a query plan such that portions of the query will be
executed at disparate locations that may change dynamically. For example, if execution of
a sub-tree in the query plan would overload the storage device then that sub-tree will be
further decomposed and the portion that will **not** be executed on the device will be
"pushed back" to a "higher-level location" (higher level in the stack or simply the origin
of a query request).

**Cross-cutting Concerns.** The distributed nature of Mohair and our desire to allow for
"pushback" means that there will be 2 primary phases of query optimization: (1)
construction and (2) execution. If optimization during the construction phase is done well
enough, then there should be less pressure and complexity on optimization during the
execution phase. Further, all pushdowns occur during the construction phase and pushbacks
occur during the execution phase. Finally, each location of query execution should
describe which portions of the query plan were executed and which are not. This means that
if a storage device executed only a part of the plan it was expected to execute, it should
be able to notify the requesting entity of the updated query plan and which portion should
be executed next.


### MSG Express -- **M**anagement of **S**ingle-cell **G**ene **Express**ion

As I was working on `Skytether`, I began to feel like I should differentiate some of the work I was
doing as a domain-specific extension of `SkyhookDM`. Thus, I initially named the repository
`skytether-singlecell`. However, I recently gave a presentation at a [CROSS][web-cross] to propose
my work for funding as an open source project. In preparing for this presentation, I realized that
my research really should have multiple parts: (1) `Skytether` is an extension and modification of
`SkyhookDM` to leverage computational storage devices, and (2) `MSG Express` is a domain-specific
data management system that uses `Skytether` to support data processing and storage of single-cell
gene expression data. Thus, `MSG Express` will be an open source project that provides benefit to
the bioinformatics community (specifically use cases that use single-cell RNA sequencing),
meanwhile `Skytether` will most likely be an umbrella project that consists of functionality pushed
into other, various open source projects--[arrow][web-arrow] and [SkyhookDM][web-skyhookdm]. I
specifically imagine that `MSG Express` will then provide integration between application-level
libraries, such as [scanpy and anndata][web-scverse], and `Skytether` (or `SkyhookDM`).


# High-level Design

**Work in Progress.**


<!-- Resources -->
[wiki-skyhook]:  https://en.wikipedia.org/wiki/Skyhook_(cable)

[web-skyhookdm]: https://sites.google.com/view/skyhookdm/home
[web-cross]:     https://cross.ucsc.edu/
[web-arrow]:     https://arrow.apache.org/
[web-scverse]:   https://scverse.org/projects/
