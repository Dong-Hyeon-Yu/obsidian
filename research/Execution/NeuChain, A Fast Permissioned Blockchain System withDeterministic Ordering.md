
https://www.vldb.org/pvldb/vol15/p2585-zhang.pdf

# Introduction
[[NeuChain.pdf#page=1&selection=60,13,63,29|To enhance its viability in practice, blockchain systems must support transaction rates comparable to those supported by existing database management systems, which can provide the same transactional guarantee]]
- OEV: ethereum, bitcoin
- EOV: hyperledger fabric, [[NeuChain.pdf#page=1&selection=126,46,136,25| However, this is still much slower than traditional database]]
	- [[NeuChain.pdf#page=1&selection=141,0,145,19|The reason of performance degradation is attributed to the expensive ordering phase --> . Determining the serial order can scarcely benefit from parallel computation and usually becomes per- formance bottleneck]]
- [[NeuChain.pdf#page=1&selection=160,42,174,58|OEPV]]
	-  hides the ordering cost by parallelizing the execution phase and the ordering phase. 
	-  the executed transactions that conflict with the order will be aborted
-  However, it is not necessary to launch the ordering step explicitly with consensus cost 
	- [[NeuChain.pdf#page=1&selection=189,0,191,27|Essentially, blockchain is a multi-master replicated ledger system that provides the consensus on transaction contents, transaction order, and block boundaries]]
		- execution: generate the updated state of database (i.e., rw set)
		- validation:  ensure the validity and legality of transaction contents
		- ordering:  Since any peer can accept write requests independently, the ordering step is necessary to determine a global order of transactions.
	- [[NeuChain.pdf#page=2&selection=94,0,126,14|deterministic ordering --> ordering-free EV]]
		- define the intra-block tx order
		- defin the inter-block tx order --> epoch number is assigned by trusted server(s)
		- each node can propose a subset of transactions independently, and multiple nodes are running concurrently for proposing transactions of the same epoch.
		-  With a complete set of unordered transactions as input, by deterministic ordering all peer nodes will produce the same results without coordination
		- The inconsistency of orders or contents of a block will be detected and avoided during the validation phase
- contributions
	1. Ordering-Free EV Architecture: [[NeuChain.pdf#page=2&selection=187,9,189,38|flexible in ordering intra-block transactions and further reduces the number of transaction con- flicts through transaction reordering.]]
	2. NeuChain System: permissioned blockchin / asynchronous block generation / pipelining.
	3. Mechanisms to Defend Malicious Attacks: provide comprehensive defence mechanisms to tackle Byzantine activities.
	4. evaluation:
		- The results on YCSB and SmallBank workloads show that NeuChain can achieve 47.2-64.1× throughput im- provement over Fabric and 1.6-12.2× throughput improvement over ResilientDB, Meepo, and Basil. 
		- 11.2-16.7× over OEV, 7.4-19.0× over EOV, and 3.7-4.8× over OEPV.
---
# Background

## 1. Architecture


## 2. Consensus


## 3. Concurrent execution

---
# System Design
## 1. Overview and Key Components

![[Pasted image 20231119164950.png]]

[[NeuChain.pdf#page=5&selection=37,55,46,5|The explicit ordering consensus phase is elimi- nated by employing the implicit deterministic ordering]]
	- All participant nodes accept transaction requests in parallel
	- These sets of transactions received from multiple nodes are exchanged among nodes through all-to- all communication
	-  it executes these transactions in a pre-defined deter- ministic order and generates a block.  The deterministic order can be defined according to the globally unique transaction ID that is gen- erated on each node based on a deterministic and malicious-resilient rule.

### Epoch server
- The blockchain nodes must reach consensus on the following two issues: 
	- i) which block a transaction belongs to 
	- ii) in which order the transactions execute within a block. <-- deterministic execution
-  A batch of transactions are sent to the epoch server, where a monotonically increased epoch number (based on local clock) is assigned to each transaction that corresponds to its belonging block
-  To avoid Byzantine attacks, multiple epoch servers can be deployed, and they need to make consensus when the epoch number increases.

### Client Proxy
- [[NeuChain.pdf#page=5&selection=80,0,85,35|Client proxy has two functionalities]]
	1.  A client proxy is responsible for accepting user client’s transaction requests and groups them into batches. --> applies an epoch number from the epoch server for a batch of transactions.  A client proxy may generate several transaction batches in an epoch
	2. After the epoch is over, the client proxy packages the transactions within the same epoch and broadcasts them to all other client proxies. -->  Because all client proxies need to ensure the integrity and consistency of the transactions, PBFT or Raft can be used as the broadcast protocol

### Block Server
- The block server keeps the complete ledger and is associated with a state database.
-  The block server is responsible for transaction execution, block generation and validation, and updating the state database
-  relies on a _reserve table_ to detect concurrency conflicts
-  Each block server corresponds a client proxy for receiving transactions. The block server and the client proxy can be allocated on the same physical node for the sake of sharing transaction data through a pipeline
- Only when the number of received and verified block signatures reaches a threshold, the validation succeeds. In case a BFT model with 3𝑓 + 1 nodes, this threshold value is set as 𝑓 + 1.

## Execution Flow
![[Pasted image 20231120131054.png]]


### 1. preparation phase
- transaction request --> a client proxy on a local peer node --> allocate (# of epoch, tid)
	- a client proxy --> (batch hash) --> epoch server --> (# of epoch, signature) --> the client proxy
	- each client proxy broadcasts the signed transactions of the last epoch to all other client proxies
- Each client proxy uses a consensus protocol (PBFT or Raft) to ensure atomic broadcast
	- each peer initiates an independent consensus instance for broad- casting
	- If a peer does not receive the message from a certain peer for a period of time (timeout), it will initiate a {view-change} request to other peers and make consensus on whether stop waiting for the message (due to leader failure) or replicate the message from other peers (due to link failure between it and the leader)
- After receiving the remote transactions, each client proxy veri- fies their validity and generates 𝑡𝑖𝑑s for them.
	- The client proxies generate 𝑡𝑖𝑑s independently without causing extra communication.
	- [[NeuChain.pdf#page=6&selection=70,62,87,47|we generate 𝑡𝑖𝑑 based on not only the transaction itself but also the entire batch containing other transaction]]
### execution phase
-  generate a read- write set
	- To support concurrency control, a shared reserve table that reserves write operations in <𝑘𝑒𝑦, 𝑡𝑖𝑑> format is designed
	- As multiple threads concurrently update the same row (with the same 𝑘𝑒𝑦), the reserve table will be updated according to a determin- istic rule that will be used in the deterministic commit protocol
- the successfully committed transactions with the same epoch number are used to update the state database, and both the committed and aborted transactions are used to generate a new block
	- The committed/aborted status of each individual transaction is immediately returned to users.

### validation phase
-  a user client can verify a block’s integrity and correctness based on a set of signatures provided by the local block server.
- After 𝑓 + 1 signatures of the current block are collected and the block hashes are verified to be identical, the block server marks the block as verified

## Deterministic transaction processing

- [[NeuChain.pdf#page=6&selection=216,0,241,63|blockchain is essentially a replicated transactional processing system]] -> deterministec database is also a typical multi-mater replicated data systems (synchronously replicates batches of tx to multiple replica servers.) --> . Inspired by deterministic database, NeuChain is designed to eliminate the explicit ordering phase by deterministic transaction processing
- deterministic execution
	- after the preparation phase is finished, a complete set of transactions of epoch 𝑖 from all remote client proxies are collected
	- The set of transactions might be received in different orders, but each transaction is associated with a globally unique 𝑡𝑖𝑑,
	- . With the same set of input transactions and a pre-defined deterministic order, the same output is warranted
	-  a deterministic conflict resolution rule
	  ![[Pasted image 20231126201420.png]]
		1. For each epoch, a reserve table 𝑇 𝑎𝑏𝑙𝑒 and a committed transactions set 𝑆𝑐𝑚𝑡 are initialized (Line 1-2)
		2. These transactions are executed by multiple worker threads in parallel (Line 3-4)
		3. . A worker thread executes a transaction based on last epoch’s database snapshot and produces the transaction’s read set 𝑇 .𝑅𝑆 and write set 𝑇 .𝑊 𝑆 (Line 11)
			- If it is a read-only transaction that has empty write set, it will not conflict with other transactions, so it is directly added to the committed set (Line 12-13).
			-  Otherwise, we update the reserve table’s corresponding row to be the smallest 𝑡𝑖𝑑 that has ever met (Line 14-15). In other words, only the write operation with the smallest 𝑡𝑖𝑑 is recorded for conflict resolution
			- abort cases: (1) wr (2) ww
			  ![[Pasted image 20231126202431.png]]
		- NeuChain does not allow to update a value multiple times in a block, which may lead to higher abort rate under high-contention workload. However, as NeuChain is very fast (only 30-75 ms to produce a block), this limitation will not impact user experience too much by resubmitting the aborted transactions
		- reordering
		  wr --> rw로 변경했을 때, cycle이 제거되면 가능.

# Implementation
![[Pasted image 20231126203329.png]]
## (1) Asynchronous block generation
- . In NeuChain, the block generation is the most time consuming step. 
- The next block generation can start before the previous block generation finishes.  
- Multiple block generation threads of different epochs are running concurrently if high contention persists.
## (2) Pipelening preparation and Execution
-  Specifically, as shown in Figure 5c, we pipeline the preparation phase and the transaction execution phase by feeding mini-batches for transaction execution.
- The early arrived transactions are immediately used to update the reserve table (Line 3-4 in Algorithm 1). In this way, the preparation phase and the execution phase can be overlapped
- However, there is still a synchronization point at Line 5 in Algorithm 1 to receive all remote transactions before performing conflict detection.

