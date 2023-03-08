# Overview

One aspect of my research is to figure out how to allow scientists to express queries so that they
can access and process their data in a way that allows the storage system and storage devices to
optimize more freely. This means: (1) we **need** scientists to be **more** declarative in how they
access data and (2) we **need** to provide a data access model and query interface that aligns well
with system performance regardless of what optimizations the system may make. More specifically,
for the second objective, we want to avoid scenarios where users feel compelled to learn system
internals in order to improve performance.


# Technologies

Although I am trying to re-use as many existing technologies as I can, I imagine that I want to
have a "SQL-like" interface that is familiar to database developers but expressive for the user's
application domain.

For the purposes of this post: `skytether` refers to the entire system, `skysql` refers to a query
interface or front-end, and `skyengine` refers to the distributed subsystem (query engine) that
executes a physical or concrete query plan.


# Query Lifecycle

The query lifecycle starts with constructing a query plan from a query expression. How queries will
be expressed is currently undecided as well as how a query plan will be represented. Once a query
plan is constructed, it is then optimized to try and run as much processing close to the data as
possible without overloading or losing end-to-end latency. The optimized query plan is then
executed and the result persisted in storage or propagated to the user. During query execution, the
plan may be altered dynamically to allow for runtime constraints to be accommodated.

## Query Plans

Skysql will take some form of input (SQL-like expression or something lower level) and construct a
query plan to be passed to skyengine. At a high level, this means that a query plan could be arrow
compute engine code or it could be a higher level representation. In either case, the final output
to send to skyengine will be a query plan with **initial** optimization passes. A simple example of
an optimization pass may be to push predicates down the query plan to the leaves (source nodes).

## Query Execution

Skyengine will use the "Arrow compute engine" (project name is still TBD) to execute queries on
data. While the arrow compute engine provides mechanisms for execution of relational operators,
skyengine will manage incremental execution of a query plan such that portions of the query will be
executed at disparate locations that may change dynamically. For example, if execution of a
sub-tree in the query plan would overload the storage device then that sub-tree will be further
decomposed and the portion that will **not** be executed on the device will be "pushed back" to a
"higher-level location" (higher level in the stack or simply the origin of a query request).

## Cross-cutting Concerns

The distributed nature of skyengine and our desire to allow for "pushback" means that there will be
2 primary phases of query optimization: (1) construction and (2) execution. If optimization during
the construction phase is done well enough, then there should be less pressure and complexity on
optimization during the execution phase. Further, all pushdowns occur during the construction phase
and pushbacks occur during the execution phase. Finally, each location of query execution should
describe which portions of the query plan were executed and which are not. This means that if a
storage device executed only a part of the plan it was expected to execute, it should be able to
notify the requesting entity of the updated query plan and which portion should be executed next.



<!-- TODO -->
