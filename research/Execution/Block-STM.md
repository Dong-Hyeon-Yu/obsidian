#ConcurrencyControl #MVCC 

# 1. Introduction
- [[block-STM.pdf#page=1&selection=438,0,475,7|A central challenge is improving the throughput.]]  [[block-STM.pdf#page=1&selection=716,31,717,58|Blockchains are still bottle- necked by other components, such as transaction execution]]
- [[block-STM.pdf#page=1&selection=718,0,719,24|Our goal is to accelerate the in-memory execution of trans- actions via parallelism.]]
- [[block-STM.pdf#page=1&selection=727,0,727,48|Conflicts are the main challenge for performance.]] 
	1) [[block-STM.pdf#page=1&selection=727,50,735,15|STM]]
	2) [[block-STM.pdf#page=1&selection=750,0,761,5|Capitalized STM]] --> avoid precomputing overheads (CG)
	3) [[block-STM.pdf#page=1&selection=768,0,775,58|BOHM (deterministic database)]] -> avoid precomputing overheads (CG)
- Contributions
  - [[block-STM.pdf#page=2&selection=16,0,18,43|Block-STM, an in-memory smart contract parallel execution engine built around the principles of optimistically controlled STM]]
	- no require a priori knowledge of write-sets
	- no pre-computation (CG)
	- no further communication
	- multi-version shared data-structure (= BOHM)
	-  **key observation**: [[block-STM.pdf#page=2&selection=29,0,31,41|The key observation is that with OCC and a preset serialization, when a transaction aborts, its write-set can be used to efficiently detect future dependencies.]]
		  1) [[block-STM.pdf#page=2&selection=41,30,41,60|per-block update]] -> [[block-STM.pdf#page=2&selection=43,46,45,46|lazy commit based on two atomic counters and a double-collect technique]] (no need to commit txs individually)
		  2) smart contracts are executed on VM -> [[block-STM.pdf#page=2&selection=57,18,61,20|opacity [27 ] is not required to protect against fatal errors]]
		  3)  [[block-STM.pdf#page=2&selection=64,28,68,58|infinite loops, even speculatively, are not possible in the Blockchain use-case]]
   - novel collaborative scheduler
	  - [[block-STM.pdf#page=2&selection=78,17,79,16| prioritizing tasks for transactions lower in the preset order]]
	  - While concurrent priority queues are notoriously hard to scale across threads [ 6 , 42],
  - [[block-STM.pdf#page=2&selection=91,11,94,52|comprehensive correctness proofs for both Safety and Liveness]]  
  - A Rust implementation of Block-STM
	  - outperforms sequential execution by up to 20x on low-contention workloads, by up to 9x on high-contention ones
	  -  lock- STM suffers from at most 30% overhead when the workload is completely sequential
	  -  significantly outperforms a state-of-the-art deterministic STM [ 55] implementation, and performances closely to Bohm which re- quires perfect write-sets information prior to execution
---
# 2. Overview

![[Pasted image 20231021155207.png]]

- [[block-STM.pdf#page=2&selection=212,0,225,11|incarnation i]]
- [[block-STM.pdf#page=2&selection=225,25,231,35|incarnation abort]]
- [[block-STM.pdf#page=2&selection=231,37,238,18|version=(tx_id, incarnation_no)]]
- [[block-STM.pdf#page=2&selection=241,8,256,47|Multi-versioned in-memory data structure, make tx to read highest version written by highest transaction before tx]] -> how to lookup?
- For each incarnation, Block-STM maintains a write-set and a read-set
	- [[block-STM.pdf#page=3&selection=364,2,366,9|read_sets = (memory locations, versions)]]
	- [[block-STM.pdf#page=3&selection=366,10,369,47|write_sets = (memory location, value)]]
- [[block-STM.pdf#page=3&selection=369,49,374,58|After an incarnation executes it needs to pass validation]]

## Dependency Estimation
- [[block-STM.pdf#page=3&selection=379,45,381,29| Block-STM treats the write-set of an aborted incarnation as an estimation of the write-set of the next one]]
	- [[block-STM.pdf#page=3&selection=384,0,390,7|write-sets are replaced with ESTIMATE marker after aborted]] -> This signifies that the next incarnation is estimated to write to the same memory location
	- [[block-STM.pdf#page=4&selection=14,2,28,24|early abort]]

## Collaborative Schedular



## Optimistic validation

## Commit rule
![[Pasted image 20231021161234.png]]
---
# 3. Detailed Description




---
# 4. Evaluation

## (1) Implementation

- Rust
- Diem / Aptos (Move contract)
	-  Diem: [[block-STM.pdf#page=8&selection=286,0,294,23|no support suspending transaction execution -> re-execute later from scratch]] -> [[block-STM.pdf#page=8&selection=295,0,307,10|check the read-set of the previous incarnation for dependencies before re-execution]] & [[block-STM.pdf#page=8&selection=322,9,339,12|re-execute when dependencies are resolved]]
	- [[block-STM.pdf#page=8&selection=340,0,346,27|uses the standard cache padding technique to mitigate false sharing]] 
	- [[block-STM.pdf#page=8&selection=346,37,359,16|concurrent hashmap as the datamap]]

## (2) Experimental Results

- Amazon Web Services c5a.16xlarge instance (AMD EPYC CPU and 128GB memory) with Ubuntu 18.04 operating system
- [[block-STM.pdf#page=8&selection=369,0,372,31|Execute whole blocks consisting p2p payment transactions]]
	- Diem p2p transactions that perform 21 reads and 4 writes.
	- Aptos p2p transactions that perform 8 reads and 5 writes each,
- [[block-STM.pdf#page=9&selection=331,0,345,27|reported measurements not include persisting the outputs to storage]] 
### 1) BOHM, LiTM, Block-STM
- [[block-STM.pdf#page=9&selection=381,3,391,59|BOHM is executed with perfect write-sets, and its write-sets analysis time is not measured]]
- [[block-STM.pdf#page=9&selection=397,11,406,2|LiTM: a recent deterministic STM library]]

![[Pasted image 20231021153009.png]]
- [[block-STM.pdf#page=9&selection=456,42,463,9|Block-STM >= perfect BOHM]] ([[block-STM.pdf#page=9&selection=463,25,473,31|the overhead of constructing the multi-version data-structure of Bohm is significant]])
![[Pasted image 20231021153038.png]]
- [[block-STM.pdf#page=10&selection=241,17,259,45|Block-STM scales almost perfectly under low contention, achieving up to 90𝑘 tps and 160𝑘 tps, which is 18x and 16x over the sequential execution]]

## 2) Under high contention
![[Pasted image 20231021154315.png]]![[Pasted image 20231021154332.png]]

- [[block-STM.pdf#page=10&selection=265,28,275,6|30% overhead under completely sequential workload]]
- [[block-STM.pdf#page=10&selection=275,7,288,28|With 10 accounts Block-STM already outperforms the sequential execution and with 100 accounts Block-STM gets up to 8x speedup in both benchmarks]]
- [[block-STM.pdf#page=10&selection=288,29,302,43|limited scalability]]

## 3) Max TPS

![[Pasted image 20231021154900.png]]
![[Pasted image 20231021154919.png]]

- [[block-STM.pdf#page=10&selection=317,9,333,27|For 32 threads, Block-STM achieves up to 110𝑘 tps for Diem p2p (21x speedup over sequential) and 170𝑘 tps for Aptos p2p (17x speedup over sequential).]]
- [[block-STM.pdf#page=10&selection=333,28,352,10|For 16 threads, Block-STM achieves up to 67𝑘 tps for Diem p2p (13x speedup) and 120𝑘 tps for Aptos p2p (12x speedup).]]
- 