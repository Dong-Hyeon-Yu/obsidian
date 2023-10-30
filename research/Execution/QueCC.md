
# Introduction
## (1) motivation
  - New multi-socket, many-core hardware architectures with tens or hundreds of cores are becoming commonplace in the market today
  - traditional transactional techniques fail to scale on these emerging hardware architecture becauese they rely on extensive coordination among threads
  - many of the existing deterministic approaches do not fundamentally redesign their algorithms for the many-core architec- ture, 

## (2) Challenges
  - the increased contention (due to increased parallelism) among many competing cores for shared resources
     e.g. failure to acquire highly contended locks (pessimistic) / failure to validate contented tuples (optimistic)
  - The role of con- currency control mechanisms in traditional databases is to deter- mine the interleaving order of operations among concurrent trans- actions over shared data
  -  But there is no fundamental reason to rely on concurrency control logic during the actual execution, 
  - the two tasks of establishing the order for accessing shared data and actually executing the transaction's logic are completely independent.
  - -->these tasks can potentially be performed in different phases of execution by independent threads. (ORTHRUS, LADS 등은 comm. overhead across threads 발생.)
- *is it possible to have concurrent execution over shared data without having any concurrency control?*

## (3) Emergence of Deterministic Data Store

  - Early proposals for deterministic execution --> data replication
  - The second wave --> deterministic execution in distributed environments, and lock-based approaches for concurrency control
	  - H-Store exclusively tailored for partitionable work- loads (e.g. [ 19 ]) as it essentially relies on partition-level locks and runs transactions serially within each partition
	  - Calvin all locks are acquired (in a pre-determined order to avoid dead- locks) before a transaction start
	  - Calvin and QueCC dovetails, the former sequences transactions pre-execution to essentially (almost) eliminate agreement protocol while the latter introduces a novel predetermined prioritization and queue-oriented execution model to essentially (almost) eliminate the concurrency protocol.
