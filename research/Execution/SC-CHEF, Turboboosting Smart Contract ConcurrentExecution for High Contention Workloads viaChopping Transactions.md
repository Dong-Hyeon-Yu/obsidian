#IntraTransactionConcurrency


https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10193853



# Introduction
- the concurrency capabilities of the smart contract execution layer have yet to be explored
- the business logic described by smart contracts is becoming more and more complex -->significant time cost of the execution of smart contract
	- for example,  separately deploying and storing business logic code and data in different smart contract accounts can simplify the cost of shared data maintenance --> , the behavior of smart contracts frequently calling other contracts is becoming very common in the blockchain’s ecosystem.
	- , it is critical to explore how to accelerate the execution of the increasingly complex smart contracts with high contention.
- There exists some study on accelerating the execution speed
	- owever, these studies were mainly designed on the basis of variants of optimistic concurrency control (OCC) and two-phase lock (2PL) 
	  --> severely affected by varied contention workloads in practice
	  --> they only considered each smart contract transaction as a single atomic unit for the concurrent scheduling
-  we propose SC- CHEF, a new concurrency control system that boosts concurrent execution of smart contracts by decomposing transaction
	1. each transaction will be first decomposed into mul- tiple Tx-pieces
		- can be executed independently according to the calling relationship of its corresponding smart contract
		- still respecting the original logic dependency
	2. Tx-pieces will be allocated to corresponding concurrent threads according to their corresponding smart contract account addresses
	3. Each thread will organize its assigned Tx-pieces into a dependency graph in the form of a directed acyclic graph, and each node of the graph will be traversed and executed when the serializable condition is met.
	- ex) ![[Pasted image 20231120204144.png]]

- contributions
	1. SC-C HEF, a new concurrency control sys- tem based on the transaction decomposition
	2. We propose to decompose the original transaction into multiple subtransactions that can be executed indepen- dently, while still respecting the original logic dependency
	3. implement it in the private Ethereum platform.  SC-CHEF outperforms the classic OCC and 2PL by 60% and 70% on average, respectively, under high contention workloads.

# background
## smart contract concurrency issue
- All concur- rency control systems that can be applied to blockchain systems must meet these two characteristic
	1. Serializability: 
		- If multiple transactions access or modify common data objects at the same time, the execution order between these conflict transactions must be determined.
		- if the execution result is equivalent to a certain serial execution result, the schedule is serializable
		- --> restricts the execution order of multiple conflicting transactions
	2. recoverability
		-  a recoverable scheduling system must ensure that these aborted modifications will not be read by other committed transactions
		- --> restricts the visibility of write operations between concurrent transactions
	- OCC
		- transactions write to their temporary local caches. 
		- These writes will be persisted to the actual shared data objects only after the validation
	- 2PC
		- each transaction applies for exclu- sive locks for the written objects and holds them until the end of the transaction
		- During this period, other transactions cannot read or write the locked objects
-  High data contention means that a small number of hot smart contracts are operated by a large number of transactions, and these conflicting operations must be serialized to ensure serializability.
- Previous work was mainly designed on the basis of variants of OCC and 2PL, which are not only essentially severely affected by varied contention workloads, but also simply regarded each smart contract transaction as a single atomic unit for the concur- rent scheduling
	- a smart contract transaction is becoming more and more complex
	- the code and states of other smart contracts are often called and accessed by this running smart contract


# SC-Chef
## 1. Overview
- ![[Pasted image 20231120210255 1.png]]
1. During the transaction execution phase, each node takes a batch of ordered transactions as input to SC-C HEF.
2. ransactions are decomposed into multiple subtransac- tions based on the read and write sets declared in advance
3. These subtransactions are mapped to the corresponding partition threads based on the data they access
4. or each partition thread, whenever it is assigned a new subtransaction, this thread updates its local dependency graph based on the read and write relationships between the subtransactions
5. After all partition threads have completed the construc- tion of their respective dependency graphs, each partition thread starts executing the subtransactions in the order of the topology order of its local dependency graph.
6. inally, a new block is generated by integrating the states of all the partitions.

## 2. Transaction Decomposing
- ![[Pasted image 20231120212929 1.png]]
- Some techniques of static analysis, such as control flow graph [22], can be applied to transaction decomposition --> [[SC-Chef_Turboboosting_Smart_Contract_Concurrent_Execution_for_High_Contention_Workloads_via_Chopping_Transactions.pdf#page=4&selection=105,51,116,45|inefficient]]
	- smart contracts will eventually be compiled into bytecode that can be executed in the EVM
	- Since EVM is a virtual machine based on the stack structure, the execution of bytecode similar to assembly language is highly dependent on its current context
	-  Therefore, decomposing transactions at smart contract’s statement level will not enable subtransactions to be executed independently.
-  all smart contracts are allocated to multiple mutually exclusive partitions according to contract addresses, where each partition corresponds to a thread that is responsible for the execution of smart contracts within the partition
	- Invocations between smart contracts within a trans- action can be mapped to interactions between thread
	- The key idea of transaction decomposition is to map the invocation relationship between contracts to the logical dependency between subtransactions
	- ![[Pasted image 20231120213315 1.png]]
	- 
