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
1.  primary-backup
	- dedicates a replica as the primary which synchronizes its states with backup replicas.
	- dopted by many databases
2.  state-machine replication
	- maintains an ordered log of operations/transactions on each replica.
	-  Many systems use consensus protocols, such as Paxos [ 57], Raft [ 66 ], and PBFT [35 ], for the replicas to agree on the ordered log. 
	   consensus protocols achieve the automatic primary failover, by introducing the view change
	- other systems rely on external services that provide a dis- tributed shared log abstraction, such as Kafka [ 13] and Corfu [26 ]. Operations/transactions are appended to the log, and the replicas, as clients of the log, apply them independently
- performance: primary-backup >> shared log >> consensus
	- Primary-backup protocols are simpler, and can perform better than state-machine replication, when the states are small and there are no failures.
	- Systems based on shared log are expected to perform better than the ones based on consensus when there are no failures. This is because shared log decouples ordering from state replication, therefore it can be optimized to have high throughput,
	   Furthermore, while the throughput of a consensus protocol decreases with more replicas, the throughput of a shared log system is expected to remain con- stant until the number of log consumers exceeds the capacity of the log producers
### (3) Failure model
- failure model
	- CFT: only fail by crashing, need to tolerate hardware and software failures.
	  (synchronous) f+1 / (asynchronous) 2f+1
	- BFT:  fail arbitrarily, need to tolerate any software and hardware failures, as well as any malicious behavior.
	  (synchronous) 2f+1 / (asynchronous) 3f+1
- network assumption
	- orthogonal dimension to the node failure model.
	- asynchronous: when the net- work delay is unbounded
	  FLP theorem: rules out any deterministic consensus protocol that can achieve both safety and liveness
- database : CFT // permissioned : both

## 2) Concurrency
- Concurrency refers to the extent to which transactions are executed at the same time
- Most blockchains support only serial execution, while distributed databases employ sophisticated concurrency control mechanisms to extract as much concurrency as possible
- database: there is a wide range of isolation levels [ 25, 37 ] which make different tradeoffs between correctness and performance. Most production-grade databases today offer more than one isolation level

## 3) Storage
### (1) storage model
- Storage can be built upon (1) the latest states only, amenable for mutation, or upon (2) all historical information, amenable for appending
- (1) --> database // both --> blockchain (state and ledger)

### (2) Index
-  Dis- tributed databases are more concerned by performance
- To compute the content-unique digest, blockchains employ an authenticated data structure, such as the Merkle tree index, to provide integrity protection on top of the state storage
## 4) Sharding
- data is partitioned into multiple shards
- sharding has only recently been introduced to blockchains to harness concur- rency across shards
### (1) Shard formation
- 
- 

### (2) Atomicity



# Experimental setup
### 1) Systmes
- Quorum (OX, raft&IBFT, fork of geth) , Hyperledger Fabric (XOV)
- TiDB (NewSQL), 
	- Placement Driver: coordinate cluster management
	- TiKV: replicated key-value storage
	- TiDB-server: parse and sechdule SQL queries in a stateless manner
	- only support snapshot isolation
- etcd (NoSQL)
	- focuses on the tradeoff between availability and consistency.
	- employs a single consensus instance to sequence all the requests
	- Without sharding, etcd fully replicates the data on each node.

### 2) Setup
- run all systems in full replication mode where each node has a complete copy of the state
- configure Quorum and Fabric to use Raft 
-  YCSB and Smallbank work- loads 
-  in-house cluster consisting of 96 nodes connected via 1Gb Ethernet.
	- Each node is equipped with Intel Xeon E5-1650 CPU, 32GB RAM, and 2TB hard disk.

# Result and Analysis

## 1) Peak performance
![[Pasted image 20231103205832.png]]
![[Pasted image 20231103205842.png]]![[Pasted image 20231103205909.png]]




## 2) Replication

![[Pasted image 20231103205922.png]]

![[Pasted image 20231103210002.png]]
![[Pasted image 20231103210045.png]]

![[Pasted image 20231103210132.png]]

![[Pasted image 20231103210158.png]]

## 4) Storage

![[Pasted image 20231103210233.png]]
![[Pasted image 20231103210256.png]]

## 5. Sharding
![[Pasted image 20231103210323.png]]


## 6. Hybrid
![[Pasted image 20231103210345.png]]



