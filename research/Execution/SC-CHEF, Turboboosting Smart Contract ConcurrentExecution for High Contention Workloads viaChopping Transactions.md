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
