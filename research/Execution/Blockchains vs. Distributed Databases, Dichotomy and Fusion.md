#SIGMOD

sigmod '21

# Introduction

-  첫번째 블록체인은 비트코인 [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=1&selection=128,0,146,43|첫문단]]
- general-purpose transaction via smart contract. (transacational management systems) --> data transparency and security (comparable to distributed databases) [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=1&selection=154,21,176,39|두번째 문단]]
-  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=1&selection=177,0,178,18|The parallel between blockchains and distributed databases has not gone unnoticed]] 세번째 문단
- the trend of design fusion . [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=1&selection=219,19,222,6|Design principles and techniques that are traditionally used by databases are being adopted by blockchains]] (ex, CC, sharding // hybrid blockchain-database system <-- security features into database systems)
- [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=18,0,20,63|rifiable data [21, 46, 68]. One limitation of the existing works that compare blockchains and databases is that they only focus on application-level, observ- able and measurable properties, such as throughput and security]]
-  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=37,40,39,64|To overcome these limitations, we aim to provide a comprehen- sive dichotomy of blockchains and databases.]] four dimensions: (1) replication, (2) concurrency, (3) storage, (4) sharding
- contributions
	1.  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=59,0,63,42|compare blockchains and distributed databases as two different types of distributed, transactional systems.]]
	2.  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=66,1,85,54|We conduct a comprehensive performance study of four popular systems: Fabric, Quorum, TiDB and etce.]]
	3. [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=88,1,93,7| We propose a framework that explains their performance dif- ferences and estimates the performance of future hybird systems]]

# Background
## 1) Blockchain
- [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=140,0,156,1|첫문단]]
	- From a data structure perspective: a list of blocks linked by cryptographic hash pointers
	- From a systems perspective: a distributed system consisting of multiple nodes, some of which are malicious
- We only consider blockchains that support smart contracts in this paper, because earlier blockchains (without smart contracts) cannot support database transaction workloads and thus cannot be compared with distributed databases. [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=2&selection=184,14,187,46|Blockchains vs Distributed Databases, Dichotomy and Fusion, page 2]]

## 2) Distributed Databases
- database systems have been around for decades. Relational databases easy-to-use SQL and intuitive ACID transacation sementices. [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=3&selection=41,0,44,21|Blockchains vs Distributed Databases, Dichotomy and Fusion, page 3]]
- the trend of scale-out database designs because of big data processing and Moore's law [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=3&selection=44,22,55,61|Blockchains vs Distributed Databases, Dichotomy and Fusion, page 3]]
- [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=3&selection=59,0,96,40|NoSQL]]
	- support more flexible data models and weaker consistency.
	- In the sense of the CAP theorem [ 50], these NoSQL systems compromise consistency for the sake of availability.
	- [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=3&selection=70,30,89,12|variations]] : key-value store(Redis, etcd), document store (CouchDB), graph store (Neo4J), column-oriented (Cassandra), and so on.
- [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=3&selection=97,0,118,48|NewSQL]]
	-  NoSQL systems, however, does not obscure the cost in usability and the increase in application complexity
	- aim to restore the relational model and ACID semantics without sacrificing much scalability
	- CockroachDB, TiDB

# Taxonomy
## 1) Replication
> The key challenge in such a system is to ensure consistency under failures

### (1) Replciation model
- The units of replication can be transac- tions or the read/write operations.
	- blockchains replicate an ordered log of transactions (or ledger)
	  **--> does not have such trusted entity, there- fore it replicates the entire transaction so that its execution can be replayed by each participant node.**
	  -->Due to this verifiability, blockchains are often used as a data and computing platform for mutually distrusting parties.
	- Distributed databases replicate the ordered log of read and write operations on top of the storage, The nodes in the database are oblivious to the transaction logic because they see only one operation at a time. 
	  **--> One consequence of this model is that the transaction manager which coordinates the execution of a transaction must be trusted**
	  --> replicating storage operations means there can be more con- currency, because operations can be replicated in different order but with the same effect on the storage

### (2) Replication approach
