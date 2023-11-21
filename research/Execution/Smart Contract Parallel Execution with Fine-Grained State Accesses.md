#EarlyWriteVisibility #ConcurrencyControl #MVCC 
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
		- state access graph (SAG)
			- lazily constructed based on the contract source code, containing partial information for certain state accesse
			- With the actual transaction data, the SAG will be completed dynamically with concrete runtime values and then used to calculate the precise data dependencies
			-  For an operation depending on an unready state, DMVCC allows to retrieve the requested values from a most recent snapshot of global states, in order to determine transaction dependencies
	2. deterministic multi- version concurrency control (DMVCC)
		1. it eliminates the write-write conflicts between transactions by preserving effects of all write operations as separate versions, which is referred to as write versioning;
		2. it allows transactions to read uncommitted writes through early-write visibility feature.
	3. [[Smart_Contract_Parallel_Execution_with_Fine-Grained_State_Accesses.pdf#page=2&selection=238,0,261,54|eliminate ww dependencies using *early-write visibility*]]
		- To eliminate write-write conflicts, the writes of different transactions to a same state are preserved as separate version
		- During the execution, each transaction reads a proper version from these, which is written by the closest preceding transaction in the block.
		-  Many previous works [6 , 16 ] only allow a transaction’s writes to be read after its results are committed, to avoid causing cascading aborts
		- DMVCC makes the writes of uncommitted transaction visible to other transactions under a more fine-grained control: it does so as soon as there is no abortable statement to be execute

# Background
- full node == validator
- contract state
   ![[Pasted image 20231121145623.png]]
- We use $S^l$ to denote the $l$-th state snapshot, which is the blockchain state after executing all the transactions up to the $l$-th block
- $stateDB$ = $\{S^0, S^1, ...\}$ 
- deterministic serializability
  ![[Pasted image 20231121150100.png]]
  ![[Pasted image 20231121150112.png]]
- recent works
	- some of them assume that the accurate read/write sets of transactions are readily available, which poses various practical challenge.
		- SChian, FISCO BCOS [12 ] requires users to specify the read/write sets explicitly to support parallelization of transactions.
	- Optimistic Concurrency Control (OCC) strategy to execute transactions in parallel without read/write set
		- After the parallel execution, validators abort and re-execute the transactions that violate deterministic serializability.
	- However, according to the reported results [10], the speed-up achieved by existing approaches is far from linear on real- world Ethereum workloads
	- These approaches perform coarse-grained transaction-level concurrency controls without considering the logic of smart contracts, thus they cannot exploit the potential parallelism by analyzing the state access patterns at the statement level

# Overview
## 1. Workflow

## 2. 