# Mixing Consistency in a Programmable Storage System

&middot; by Aldrin Montana
&middot; edited by Abhishek Singh and Lindsey Kuper

## Introduction

Modern distributed applications call for ways to store and access data using a range of consistency
guarantees. Consider a distributed shared log, like that described in the
[FuzzyLog][fuzzylog-paper] paper. A log service is useful in many contexts for applications
including data management systems and storage systems. The FuzzyLog paper describes the use of log
services for funneling updates for data management systems and other services (e.g., metadata and
coordination services, filesystem namespaces) within data centers. The authors motivate their work
by discussing the benefit of distributing the log across several servers and relaxing constraints
on data consistency.

Reasoning about mixed consistency becomes especially challenging where data accessed at one level
of consistency is used in the computation for data at a different level of consistency. In
FuzzyLog, for instance, updates to the log are marked with _colors_ that enable a variety of
consistency choices.  FuzzyLog makes operations to a single color across regions causally
consistent, while operations to a single color within a region are serializable.  Hence, FuzzyLog
uses at least two levels of consistency in its implementation.

The need to use a range of consistency levels in the same application motivates the need for
_language-level_ support for mixed consistency.  Recent language-level abstractions such as
[QUELEA][quelea-paper], [IPA][ipa-paper], and [MixT][mixt-paper] aim to make programming in a
mixed-consistency world safer and easier. QUELEA allows the developer to declaratively specify the
consistency guarantees provided by a datastore and the consistency requirements for
application-level operations. IPA provides types representing consistency levels that can be placed
on abstract data type (ADT) instances, and policies for determining the consistency of a data type
at runtime. MixT is a domain-specific language (DSL) that uses information flow analysis to prevent
weakly consistent data from influencing strongly consistent data.

In this blog post, we are interested in exploring approaches for specifying a range of consistency
guarantees in a _programmable storage_ system. After giving an informal overview of data
consistency in distributed systems, I'll discuss what programmable storage is and how a
programmable storage system might support a mixture of consistency levels.

[fuzzylog-paper]: https://www.usenix.org/system/files/osdi18-lockerman.pdf
[ipa-paper]: https://homes.cs.washington.edu/~luisceze/publications/ipa-socc16.pdf
[mixt-paper]: http://www.cs.cornell.edu/andru/papers/mixt/mixt.pdf
[quelea-paper]: http://kcsrk.info/papers/quelea_pldi15.pdf
