
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


