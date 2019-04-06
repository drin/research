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
| 19/04/08 | Mon | [Consensus with One Faulty Process][one-faulty-proc]           | [review](./2019.04.08.review.md) |
| 19/04/10 | Wed | No Class                                                       |                                  |
| 19/04/12 | Fri | [Tracing and Fault Injection with Box of Pain][box-of-pain]    | [review](./2019.04.12.review.md) |

## Week 3

| Date     | Day | Title (and Paper Link)                                              | Review                           |
| -------- | --- | ------------------------------------------------------------------- | -------------------------------- |
| 19/04/15 | Mon | [Paxos made simple][paxos-simple]                                   | [review](./2019.04.15.review.md) |
| 19/04/17 | Wed | [Lineage-driven Fault Injection][ldfi]                              | [review](./2019.04.17.review.md) |
| 19/04/19 | Fri | [Stronger Semantics for Geo-Replicated Storage][geo-repl-semantics] | [review](./2019.04.19.review.md) |

## Week 4

| Date     | Day | Title (and Paper Link)                                         | Review                           |
| -------- | --- | -------------------------------------------------------------- | -------------------------------- |
| 19/04/22 | Mon | [Consistent Transactions with Inconsistent Replication][tapir] | [review](./2019.04.22.review.md) |
| 19/04/24 | Wed | [Chord: Scalable Peer-to-Peer Lookup Service][chord]           | [review](./2019.04.24.review.md) |
| 19/04/26 | Fri | [Practical Byzantine Fault Tolerance][byzantine-fault]         | [review](./2019.04.26.review.md) |

## Week 5

| Date     | Day | Title (and Paper Link)                              | Review                           |
| -------- | --- | --------------------------------------------------- | -------------------------------- |
| 19/04/29 | Mon | Lightning Talks                                     |                                  |
| 19/05/01 | Wed | Lightning Talks                                     |                                  |
| 19/05/03 | Fri | [Reasoning About Knowledge... ][knowledge-analysis] | [review][./2019.05.03.review.md] |

## Week 6 (Here and below is still in flux)

| Date     | Day | Title (and Paper Link)                                    | Review                           |
| -------- | --- | --------------------------------------------------------- | -------------------------------- |
| 19/05/06 | Mon | [Bitcoin: A Peer-to-Peer Electronic Cash System][bitcoin] | [review][./2019.05.06.review.md] |
| 19/05/06 | Mon | [Managing update conflicts in Bayou][bayou-conflicts]     | [review][./2019.04.10.review.md]   |

## Week 7

| Date     | Day | Title (and Paper Link)                                            | Review                                        |
| -------- | --- | ----------------------------------------------------------------- | --------------------------------------------- |
| 19/05/15 | Wed | [A view of Cloud Computing][view-of-cloud]                        | [review][./2019.05.15.view-cloud.review.md]   |
| 19/05/15 | Wed | [Towards A Cloud Computing Research Agenda][toward-cloud-agenda]  | [review][./2019.05.15.cloud-agenda.review.md] |
| 19/05/17 | Fri | [Sinfonia][sinfonia]                                              | [review][./2019.05.17.review.md]              |

## Week 8

| Date     | Day | Title (and Paper Link)                                               | Review                           |
| -------- | --- | -------------------------------------------------------------------- | -------------------------------- |
| 19/05/20 | Mon | [MapReduce][mapreduce]                                               | [review][./2019.05.20.review.md] |
| 19/05/22 | Wed | [A comprehensive study of Convergent and CRDTs][convergent-crdt]     | [review][./2019.05.22.review.md] |
| 19/05/?? | ??? | [Logic and Lattices for Distributed Programming][logic-and-lattices] | [review][./2019.05.xx.review.md] |
| 19/05/24 | Fri | [Coordination Avoidance in Database Systems][coord-avoid-dbms]       | [review][./2019.05.24.review.md] |

## Week 9

| Date     | Day | Title (and Paper Link)                             | Review                           |
| -------- | --- | -------------------------------------------------- | -------------------------------- |
| 19/05/29 | Wed | [Just Say NO to Paxos Overhead][no-paxos-overhead] | [review][./2019.05.29.review.md] |
| 19/05/31 | Fri | Room for Activities                                |                                  |

## Week 10

| Date     | Day | Title (and Paper Link)                                                  | Review                                    |
| -------- | --- | ----------------------------------------------------------------------- | ----------------------------------------- |
| 19/06/05 | Wed | [Network Diagnostics with Differential Provenance][net-diff-provenance] | [review][./2019.06.05.network.review.md]  |
| 19/06/05 | Wed | [Pensieve: Non-Intrusive Failure Reproduction][pensieve]                | [review][./2019.06.05.pensieve.review.md] |
| 19/06/07 | Fri | The Declarative Imperative                                              | [review][./2019.06.07.review.md]          |


<!-- Paper Links -->
[note-distributed]:     http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.41.7628
[lamport-clocks]:       http://amturing.acm.org/p558-lamport.pdf
[detect-causal]:        https://www.vs.inf.ethz.ch/publ/papers/holygrail.pdf
[dynamo]:               http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf
[bayou-conflicts]:      http://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf
[chord]:                https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf
[model-chord]:          http://www.sigcomm.org/sites/default/files/ccr/papers/2012/April/2185376-2185383.pdf
[one-faulty-proc]:      https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf
[knowledge-analysis]:   https://www.cs.cornell.edu/home/halpern/papers/UsingRAK.pdf
[paxos-simple]:         http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf
[statemachine-repl]:    https://www.cs.cornell.edu/fbs/publications/SMSurvey.pdf
[understand-consensus]: https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf
[ldfi]:                 https://people.eecs.berkeley.edu/~palvaro/molly.pdf
[detect-config-errors]: https://www.usenix.org/system/files/conference/osdi16/osdi16-xu.pdf
[geo-repl-semantics]:   http://sns.cs.princeton.edu/docs/eiger-nsdi13.pdf
[tapir]:                https://syslab.cs.washington.edu/papers/tapir-tr14.pdf
[byzantine-fault]:      http://pmg.csail.mit.edu/papers/osdi99.pdf
[bitcoin]:              https://bitcoin.org/bitcoin.pdf
[view-of-cloud]:        https://www2.eecs.berkeley.edu/Pubs/TechRpts/2009/EECS-2009-28.pdf
[toward-cloud-agenda]:  https://www.cs.purdue.edu/homes/bb/cs590/handouts/Cornell.pdf
[sinfonia]:             http://www.sosp2007.org/papers/sosp064-aguilera.pdf
[mapreduce]:            http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf
[convergent-cdrt]:      http://hal.upmc.fr/inria-00555588/document
[logic-and-lattices]:   http://db.cs.berkeley.edu/papers/UCB-lattice-tr.pdf
[coord-avoid-dbms]:     http://www.vldb.org/pvldb/vol8/p185-bailis.pdf
[no-paxos-overhead]:    https://www.usenix.org/system/files/conference/osdi16/osdi16-li.pdf
[net-diff-provenance]:  http://www.cis.upenn.edu/~angchen/papers/sigcomm-2016.pdf
[pensieve]:             http://www.eecg.toronto.edu/~yuan/papers/pensieve-sosp17.pdf
[box-of-pain]:          https://arxiv.org/pdf/1903.12226.pdf

<!-- Orphaned Papers -->
<!--
| 19/04/12 | Fri | [Lightweight Modeling to Understand Chord][model-chord] | [review][./2019.04.12.lightweight-chord.review.md] |
| 19/04/08 | Mon | [Dynamo: Amazon's Available Key-Value Store][dynamo]    | [review][./2019.04.08.review.md]                   |
| 19/04/26 | Fri | [Early Detection of Configuration Errors][detect-config-errors] | [review][./2019.04.26.review.md]              |
| 19/04/22 | Mon | [State machine replication][statemachine-repl]                  | [review][./2019.04.22.statemachine.review.md] |
| 19/04/22 | Mon | [Understandable Consensus Algorithm][understand-consensus]      | [review][./2019.04.22.consensus.review.md]    |
-->
