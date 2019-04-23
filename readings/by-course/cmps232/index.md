# Reading Schedule

## Week 1

| Date     | Day | Title (and Paper Link)                                 | Review                           |
| -------- | --- | ------------------------------------------------------ | -------------------------------- |
| 19/04/01 | Mon | [A Note on Distributed Computing][note-distributed]    | [review](./2019.04.01.review.md) |
| 19/04/03 | Wed | [Time, Clocks, and Ordering of Events][lamport-clocks] | [review](./2019.04.03.review.md) |
| 19/04/05 | Fri | [Detecting Causal Relationships][detect-causal]        | [review](./2019.04.05.review.md) |


## Week 2

| Date     | Day | Title (and Paper Link)                                         | Review                           |
| -------- | --- | -------------------------------------------------------------- | -------------------------------- |
| 19/04/08 | Mon | [Tracing and Fault Injection with Box of Pain][box-of-pain]    | [review](./2019.04.08.review.md) |
| 19/04/10 | Wed | [Paxos made simple][paxos-simple]                              | [review](./2019.04.10.review.md) |
| 19/04/12 | Fri | No Class                                                       |                                  |

## Week 3

| Date     | Day | Title (and Paper Link)                                              | Review                           |
| -------- | --- | ------------------------------------------------------------------- | -------------------------------- |
| 19/04/15 | Mon | [Consensus with One Faulty Process][one-faulty-proc]                | [review](./2019.04.15.review.md) |
| 19/04/17 | Wed | [Chord: Scalable Peer-to-Peer Lookup Service][chord]                | [review](./2019.04.17.review.md) |
| 19/04/19 | Fri | [Stronger Semantics for Geo-Replicated Storage][geo-repl-semantics] | [review](./2019.04.19.review.md) |

## Week 4

| Date     | Day | Title (and Paper Link)                                           | Review                           |
| -------- | --- | ---------------------------------------------------------------- | -------------------------------- |
| 19/04/22 | Mon | [Managing update conflicts in Bayou][bayou-conflicts]            | [review](./2019.04.22.review.md) |
| 19/04/24 | Wed | [Towards A Cloud Computing Research Agenda][toward-cloud-agenda] | [review](./2019.04.24.review.md) |
| 19/04/26 | Fri | [Practical Byzantine Fault Tolerance][byzantine-fault]           | [review](./2019.04.26.review.md) |

## Week 5

| Date     | Day | Title (and Paper Link)                              | Review                           |
| -------- | --- | --------------------------------------------------- | -------------------------------- |
| 19/04/29 | Mon | [Lineage-driven Fault Injection][ldfi]              | [review](./2019.05.03.review.md) |
| 19/05/01 | Wed | Guest/Sub/No Class                                  |                                  |
| 19/05/03 | Fri | Lightning Talks                                     |                                  |


## Week 6

| Date     | Day | Title (and Paper Link)                                           | Review                           |
| -------- | --- | ---------------------------------------------------------------- | -------------------------------- |
| 19/05/06 | Mon | Lightning Talks                                                  |                                  |
| 19/05/08 | Wed | [Reasoning About Knowledge... ][knowledge-analysis]              | [review](./2019.05.08.review.md) |
| 19/05/10 | Fri | [Consistent Transactions with Inconsistent Replication][tapir]   | [review](./2019.05.10.review.md) |

## Week 7

| Date     | Day | Title (and Paper Link)                                            | Review                             |
| -------- | --- | ----------------------------------------------------------------- | ---------------------------------- |
| 19/05/13 | Mon | [Sinfonia][sinfonia]                                              | [review](./2019.05.13.review.md)   |
| 19/05/15 | Wed | [MapReduce][mapreduce]                                            | [review](./2019.05.15.review.md)   |
| 19/05/17 | Fri | [A comprehensive study of Convergent and CRDTs][convergent-crdt]  | [review](./2019.05.17.review.md)   |

## Week 8

| Date     | Day | Title (and Paper Link)                                   | Review                           |
| -------- | --- | -------------------------------------------------------- | -------------------------------- |
| 19/05/20 | Mon | [Noria][noria]                                           | [review](./2019.05.20.review.md) |
| 19/05/22 | Wed | [Amazon Aurora][aurora]                                  | [review](./2019.05.22.review.md) |
| 19/05/24 | Fri | [Maelstrom: Mitigating Data-Center Disasters][maelstrom] | [review](./2019.05.24.review.md) |

## Week 9

| Date     | Day | Title (and Paper Link)                             | Review                           |
| -------- | --- | -------------------------------------------------- | -------------------------------- |
| 19/05/29 | Wed | [Canopy: End-to-End Performance Tracing][canopy]   | [review](./2019.05.29.review.md) |
| 19/05/31 | Fri | Room for Activities                                |                                  |

## Week 10

| Date     | Day | Title (and Paper Link)                   | Review                            |
| -------- | --- | ---------------------------------------- | --------------------------------- |
| 19/06/03 | Mon | [Logic and Lattices][logic-and-lattices] | [review](./2019.06.03.review.md)  |
| 19/06/05 | Wed | Room for Activities                      | [review](./2019.06.05.review.md)  |
| 19/06/07 | Fri | Room for Activities                      | [review](./2019.06.07.review.md)  |


<!-- Paper Links -->
[note-distributed]:     http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.41.7628
[lamport-clocks]:       http://amturing.acm.org/p558-lamport.pdf
[detect-causal]:        https://www.vs.inf.ethz.ch/publ/papers/holygrail.pdf
[dynamo]:               http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf
[bayou-conflicts]:       http://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf
[chord]:                https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf
[model-chord]:          http://www.sigcomm.org/sites/default/files/ccr/papers/2012/April/2185376-2185383.pdf
[one-faulty-proc]:      https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf
[knowledge-analysis]:   https://www.cs.cornell.edu/home/halpern/papers/UsingRAK.pdf
[paxos-simple]:         http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf
[statemachine-repl]:    https://www.cs.cornell.edu/fbs/publications/SMSurvey.pdf
[understand-consensus]: https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf
[ldfi]:                  https://people.eecs.berkeley.edu/~palvaro/molly.pdf
[detect-config-errors]:  https://www.usenix.org/system/files/conference/osdi16/osdi16-xu.pdf
[geo-repl-semantics]:   https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final149.pdf
[tapir]:                https://syslab.cs.washington.edu/papers/tapir-tr14.pdf
[byzantine-fault]:      http://pmg.csail.mit.edu/papers/osdi99.pdf
[bitcoin]:              https://bitcoin.org/bitcoin.pdf
[view-of-cloud]:        https://www2.eecs.berkeley.edu/Pubs/TechRpts/2009/EECS-2009-28.pdf
[toward-cloud-agenda]:  https://www.cs.purdue.edu/homes/bb/cs590/handouts/Cornell.pdf
[sinfonia]:             http://www.sosp2007.org/papers/sosp064-aguilera.pdf
[mapreduce]:            http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf
[convergent-crdt]:      http://hal.upmc.fr/inria-00555588/document
[logic-and-lattices]:   http://db.cs.berkeley.edu/papers/UCB-lattice-tr.pdf
[coord-avoid-dbms]:     http://www.vldb.org/pvldb/vol8/p185-bailis.pdf
[no-paxos-overhead]:    https://www.usenix.org/system/files/conference/osdi16/osdi16-li.pdf
[net-diff-provenance]:   http://www.cis.upenn.edu/~angchen/papers/sigcomm-2016.pdf
[pensieve]:             http://www.eecg.toronto.edu/~yuan/papers/pensieve-sosp17.pdf
[box-of-pain]:          https://arxiv.org/pdf/1903.12226.pdf
[noria]:                https://www.usenix.org/conference/osdi18/presentation/gjengset
[aurora]:               https://dl.acm.org/citation.cfm?id=3183713.3196937
[maelstrom]:            https://www.usenix.org/conference/osdi18/presentation/veeraraghavan
[canopy]:               https://research.fb.com/publications/canopy-end-to-end-performance-tracing-at-scale/

<!-- Orphaned Papers -->
<!--
[Lightweight Modeling to Understand Chord]      [model-chord]
[Dynamo: Amazon's Available Key-Value Store]     [dynamo]
[Early Detection of Configuration Errors]         [detect-config-errors]
[State machine replication]                      [statemachine-repl]
[Understandable Consensus Algorithm]             [understand-consensus]
[Bitcoin: A Peer-to-Peer Electronic Cash System] [bitcoin]
[A view of Cloud Computing]                      [view-of-cloud]
[Network Diagnostics with Differential Provenance][net-diff-provenance]
[Just Say NO to Paxos Overhead]                  [no-paxos-overhead]
[Coordination Avoidance in Database Systems]     [coord-avoid-dbms]
[Pensieve: Non-Intrusive Failure Reproduction]   [pensieve]
-->
