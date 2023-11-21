
ICDCS '23

# Introduction

- [[Smart_Contract_Parallel_Execution_with_Fine-Grained_State_Accesses.pdf#page=1&selection=135,45,157,15|the throughput bottleneck of blockchain systems is now shifting to smart contract execution]]
- Correct parallel transaction execution has to meet the requirement of deterministic serializability,
	-  A simple idea to achieve deterministic serializability is to allow non-conflicting transactions to be executed in parallel and conflicting ones still to be executed serially --> [[Smart_Contract_Parallel_Execution_with_Fine-Grained_State_Accesses.pdf#page=1&selection=186,23,203,30|serial execution dominate the overall execution time.]] (the speedup is capped by 4x compared with serial execution)
	- [[Smart_Contract_Parallel_Execution_with_Fine-Grained_State_Accesses.pdf#page=1&selection=203,32,207,15|The main reason behind this is the high contention in smart contract transaction]]
	- Garamv ¨olgyi et al. [10 ] proposed some practical coding paradigms, such as *conflict-aware token distribution and partitioned counters*, to reduce the contention of smart contracts at the application level
		- coarse-grained static analysis --> over-approximate transaction dependencies.
	- OCC
		- transactions violating deterministic serializability need to be re-executed  when the contention between transactions is high
- contributions
	1. program analysis: analyzing smart contract code to determine the precise read/write sets of each program statement and enable more find-grained state accesses.
	2. deterministic multi- version concurrency control (DMVCC)
		1. it eliminates the write-write conflicts between transactions by preserving effects of all write operations as separate versions, which is referred to as write versioning;
		2. t allows transactions to read uncommitted writes through early-write visibility feature.