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
- blockchain << NewSQL << k-v storages (etcd and TiKV)
- k-v storages don't incur the overhead of supporting ACID transactions
-  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=8&selection=200,7,203,63|ACID semantics impose less constranct on read-only transactions]]


![[Pasted image 20231103205842.png]]
- the latency when the systems are unsaturated
- 

![[Pasted image 20231103205909.png]]
- The request key follows a Zipfian distri- bution with coefficient 𝜃 = 1 on 1M records
- etcd  does not support general transactional workloads.
-  a Smallbank transaction imposes more constraints and may touch up to two records, but the record size is smaller
-  To our astonishment, the experiments show that the performance difference between blockchains and distributed databases is small. We attribute this improvement to the smaller record size of Smallbank.  Quorum’s performance is vulnerable to transactions that access large records



## 2) Replication
![[Pasted image 20231103212247.png]]
- To understand the impact of the replication model, we focus on Fabric and TiDB because they sup- port different transaction lifecycles. 
-  When Fabric is unsaturated, the order and validate phases take roughly 700ms each, while the execute phase takes below 500ms. But when the request rate exceeds the system capacity, validation phase becomes the bottleneck, as shown in Figure 7a.
- Worst still, substantial overhead in transaction processing is attributed to factors other than data processing. For example, we observe that Fabric, under the saturated scenario, spends 42% of the block validation time to verify the transaction signature
- The security overhead is the most prominent in query transac- tions, which involve no consensus in both systems. We show in Figure 7b that Fabric spends most of the query time to authenticate the clients. 

![[Pasted image 20231103210002.png]]
-  [[Blockchains vs Distributed Databases, Dichotomy and Fusion.pdf#page=9&selection=220,12,224,67|fabric is the only shared log system]]
- Contrary to our expectations on the two blockchains, we ob- serve neither a constant performance of the shared log system nor performance degradation in the consensus-based system
	- In Fabric, we find a 38% increase in the block validation latency. This is because the endorsement policy requires a transaction to be endorsed by all the nodes. Hence, more nodes lead to transactions with more signa- tures and, therefore, longer validation. Due to the sequentiality in transaction-based replication, this increase in validation time trans- lates to the decrease in throughput, as we explained in Section 5.2.1.
	-  Quorum underutilizes Raft, making its perfor- mance insensitive to the consensus group size. Specifically, Quorum first pre-executes transactions at the tip of the ledger, before batch- ing these transactions into a block for the consensus. Thus, the block proposal rate is affected by the ledger’s sequentiality
- Under the same Raft protocol, the NoSQL database, etcd, achieves higher peak performance compared to the blockchains, but the performance degrades with the number of nodes. We attribute this to the consensus protocol. 
-  The NewSQL database does not exhibit either a constant or decreasing performance trend. Instead, TiDB reaches its peak performance on 7 nodes. This is because of the interplay between the transaction processing on TiDB servers and data storage on TiKV nodes
- Finally, we conclude that the transaction- based replication model has an obvious impact on the performance of blockchains, while replication approaches have plain effects on the performance of distributed databases.

![[Pasted image 20231103210045.png]]
- Our key observation here is that blockchains and databases are comparable under a high contention workload, given the fact that TiDB drastically drops from 5461 to 173 tps when 𝜃 increases from 0 to 1
- Etcd and Quorum do not have concurrency control because they execute transactions serially. Thus, their performance is not affected by skewness
-  TiDB’s throughput drop is disproportional to its increase in abort rate. This is because each transaction coordinator must obtain a latch on a primary record, whose write outcome deter- mines the overall transaction status. But write must undergo the consensus for replication. Under a highly skewed workload, such a latching mechanism makes the transaction coordinator spend more time on contention resolution than the actual execution of the trans- action payload, resulting in a remarkable decrement of the overall throughput. 
- Hence we conclude that the workload skewness exerts a tremendous impact on storage-based replicated, concurrency- over-replication architectures



![[Pasted image 20231103210158.png]]

## 4) Storage

![[Pasted image 20231103210233.png]]
![[Pasted image 20231103210256.png]]

## 5. Sharding
![[Pasted image 20231103210323.png]]


## 6. Hybrid
![[Pasted image 20231103210345.png]]



