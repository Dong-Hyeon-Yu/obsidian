
# Introduction
## (1) motivation
  - New multi-socket, many-core hardware architectures with tens or hundreds of cores are becoming commonplace in the market today
  - traditional transactional techniques fail to scale on these emerging hardware architecture becauese they rely on extensive coordination among threads
  - many of the existing deterministic approaches do not fundamentally redesign their algorithms for the many-core architec- ture, 

## (2) Challenges
  - the increased contention (due to increased parallelism) among many competing cores for shared resources
     e.g. failure to acquire highly contended locks (pessimistic) / failure to validate contented tuples (optimistic)
  - The role of con- currency control mechanisms in traditional databases is to deter- mine the interleaving order of operations among concurrent trans- actions over shared data
  -  But there is no fundamental reason to rely on concurrency control logic during the actual execution, 
  - the two tasks of establishing the order for accessing shared data and actually executing the transaction's logic are completely independent.
  - -->these tasks can potentially be performed in different phases of execution by independent threads. (ORTHRUS, LADS 등은 comm. overhead across threads 발생.)
- *is it possible to have concurrent execution over shared data without having any concurrency control?*

## (3) Emergence of Deterministic Data Store

  - Early proposals for deterministic execution --> data replication
  - The second wave --> deterministic execution in distributed environments, and lock-based approaches for concurrency control
	  - H-Store exclusively tailored for partitionable work- loads (e.g. [ 19 ]) as it essentially relies on partition-level locks and runs transactions serially within each partition
	  - Calvin all locks are acquired (in a pre-determined order to avoid dead- locks) before a transaction start
	  - Calvin and QueCC dovetails, the former sequences transactions pre-execution to essentially (almost) eliminate agreement protocol while the latter introduces a novel predetermined prioritization and queue-oriented execution model to essentially (almost) eliminate the concurrency protocol.
  - Serializability
	  - A deterministic transaction processing engine needs to ensure that (a) the order of conflicting operations, and (b) the commitment ordering of transactions follow the same order that is determined prior to execution
	  - 단점: 단 하나의 schedule만 가능함. 클라이언트-서버 간 tx 모델이 단순해야함. (왔다갔다 대화형은 힘듦.) / 장점: throughput을 최대화할 수 있음, recovery execution이 간단함.

## (4) Contributions
  - we present a rich formalism to model our re-thinking of how transactions are processed in QueCC. Our formalism does not suffer from the traditional data dependency conflicts among transactions because they are seamlessly eliminated by our execution model (Section 2). 
  - we propose an efficient deterministic, queue-oriented trans- action execution model for highly parallel architectures, that is amenable to efficient pipelining and offers a flexible and adaptable thread-to-queue assignment to minimize coordi- nation (Section 3). 
  - we design a novel two-phase, priority-based, queue-oriented planning and execution model that eliminates the need for concurrency control (Section 4). 
  - we prototype our proposed concurrency architecture within ExpoDB [ 13, 14 ], a comprehensive concurrency control testbed, which includes eight modern concurrency techniques, to demonstrate QueCC effectiveness compared to state-of-the- art approaches based on well-established benchmarks such as TPC-C and YCSB (Section 5).

# 2. Formalism

### Data model 

- data model: key-value storage
	- each record in the database is logically defined as a pair (k,v)
	-  Internally, key ==  physical record identifiers (RID), i.e., the physical address in either memory or disk.

### Transaction model

- **Transactions can be modeled as a DAG (Directed Acyclic Graphs) of “sub-transactions” called transaction fragments** 
	-  **Each fragment performs a sequence of operations on a set of records (each inter- nally associated with a RID).**
		- A transaction fragment $f_i$ is defined as a pair $(S_{op} , C)$, where $S_{op}$ is a finite sequence of operations either $READ$ or $WRITE$ on records identified with $RID$s that are mapped to the same contiguous $RID$ range, and $C$ is a finite set of constraints that must be satisfied post the fragment execution.
	- **Fragments that belong to the same transaction can have two kinds of dependencies, and such dependencies are based on the transaction’s logic.** [[QueCC.pdf#page=3&selection=41,0,268,13|(logic-induced dependencies)]]
		 (1) An intra-transaction data dependency exist between two transaction fragments $f_i$ and $f_j$ , denoted as $f_i \overset{d}\rightarrow f_j$ , if and only if both fragments belong to the same transaction and the logic of $f_j$ requires data computed by the logic of $f_i$.
		 	
		 (2) An intra-transaction commit dependency exist between two trans- action fragments fi and fj , denoted as fi c → fj , if and only if both fragments belong to the same transaction and fi is abortable.
		 	
		 A transaction fragment fi is abortable if and only if fi .C , ϕ
	- A transaction $t_i$ is defined as a directed acyclic graph (DAG) $G_{t_i} := (V_{t_i} , E_{t_i})$, where Vti is finite set of transaction fragments ${ f_1, f_2, ... , f_k }$, and $E_{t_i} = \{(f_p , f_q )| f_p \overset{d}\rightarrow f_q \lor f_p \overset{c}\rightarrow f_q \}$ 
- **third type of dependencies that may exist between transaction fragments of different transactions** [[QueCC.pdf#page=3&selection=398,0,476,1|(execution-induced dependencies)]]
	- An inter-transaction commit dependency exist between two transac- tion fragments fi and fj is denoted as $f_i \overset{s}\rightarrow f_j$ , if and only if both fragments belong to different transactions and fj speculatively reads uncommitted data written by fi
	- inter-transaction commit dependencies may cause cascading aborts among transactions --> “early write visibility”, which is proposed by Faleiro et al.[10].
	- modeling conflicts with inter-tx dependencies is no longer possible in QueCC because these conflicts are seamlessly resolved and eliminated by the determin- istic, priority-based, queue-oriented execution model of QueCC.

## 3. PRIORITY-BASED, QUEUE-ORIENTED TRANSACTION PROCESSING
![[Pasted image 20231030135931.png]]

- Transaction batches are processed in two deterministic phase.
	1. planning phase)  multiple planner threads with predetermined distinct priorit consume transactions from their respective client transaction queue in parallel and create prioritized execution queues. 
		-  prioritize execution queues --> (1) parallel planing ( the ordering of transactions planned by different planner threads is preserved.) // (2) enables execution threads to decide the order of executing fragments, which leads to correct serializable execution.
		-  The planner thread acts as a local sequencer with a predetermined priority, and it spreads operations of each transaction (e.g., reads and writes) into a set of queues based on the sequence order.
		-  The goal of the planner is to distribute operations (e.g., READ/WRITE) into a set of almost equal-sized queues
		-  Queues for each planner can be merged or split arbitrarily to satisfy balanced size queues
		- However, queues across planners can only be combined together following *execution-priority invariance* 
		   For each record, operations that belong to higher priority queues (created by a higher priority planner) must always be executed before executing any lower priority operations.
	2. execution phase) 
		-  The execution queues are handed over to a set of execution threads based on their priorities.
		-  An execution thread can arbitrarily select any outstanding queues within a batch and exe- cute its operations without any coordination with others executors
		-  The only criterion that must be satisfied is the execution-priority in- variance, implying that if a lower priority queue overlaps with any higher priority queues (i.e., containing overlapping records), then before executing a lower priority queue, the operations in all higher priority queues must be executed first
		-  Execution threads operate directly on the in- memory store ( 5 ). Once all the execution queues are processed, it signals the completion of the batch, and transactions in the batch are committed

## 4. Control-Free Architectural Design

### (1) Deterministic Planning Phase

> how to efficiently produce execution plans and distribute them across execution threads in a balanced manner? 
> How to efficiently deliver the plans to execution threads?

#### Priority Group
-  To represent priority inheritance of EQs, we associate all EQs planned by a planner with a priority group (PG). Each batch is organized into priority groups of EQs with each group inheriting the priority of its planne
	- Given a set of transactions in a batch, T = {t1, t2, . . . , tn }, and a set of planner threads {pt1, pt2, . . . . ptk }, the planning phase will produce a set of k priority groups {pд1, pд2, . . . pдk }, where each pдi is a partition of T and is produced by planner thread pti .
-  EQs are the main data structure used to represents the workload of transaction fragments
	-  Planners fill EQs with transac- tion fragments augmented with some additional meta-data during the planning and assign EQs to execution threads on batch delivery.
	-  Planners may physically split or logically merge EQs in order to balance the load given to execution queues


#### Range-based Planning

> how each planner produces the priority-based EQs associated with its PG?

- each planner starts by partitioning the whole RID space into a number of ranges equal to the number of execution threads. (The range partitioning can be learned, adapted, and tuned across batches) 키 값으로 round robin 해서 load balancing하는 느낌.
-  each range is associated with an EQ, and partitioning a range implies splitting their associated EQs.
-  Range partitioning from earlier batches is reused for future ones, which amortize the cost of range partitioning across multiple batches, and reduces the planning time for the subsequent batches
-  When EQs become full during plan- ning, they are split into additional queues. [[QueCC.pdf#page=5&selection=162,0,174,24|split algorithm]]
- EQ에 배치만큼 모이면 execution threads로 보낼 수 있게 됨. [[QueCC.pdf#page=5&selection=175,0,194,45|QueCC, page 5]]

#### Operation Planning

- When planning a READ or an UPDATE operation, a planner will simply do an index lookup to find the RID value for the record and its pointer
	-  Based on the RID value, it determines the EQ responsible for the transaction fragment. 
	- It will check if the EQ is full and perform a split if needed. 
	- Finally, it inserts the transaction fragment into the EQ.
- DELETE operations are handled the same way as UPDATE operations from planning perspective
- For the INSERT operations, a planner assigns a new RID value to the new record and places the fragment into the respective EQ

### (2) Deterministic Execution Phase

 - without any need for controlling its access to records.
-  Fragments are executed in the same order they are planned within a single EQ.
-  Execution threads try to execute the whole EQ before moving to the next EQ
-  intra- transaction data dependency to another fragment that resides in another EQ.
	-  Once the intermediate values are computed by the corresponding fragments, they are stored in the transaction’s meta-data accessible by all transaction fragments
	-  Data dependencies may trigger EQ switching before the whole EQ is consumed. In particular, an EQ switch occurs if intermediate values required by the fragment in hand are not available.
	-  ex) $Tx=\{f_i, f_j\},\, f_i=\{a=read(k_i)\},\, f_j=\{b=a+1;\, write(k_j, b)\}$
	- Our EQ switch mechanism is very lightweight and requires only a single private counter per EQ to keep track of how many fragments of the EQ are consumed

#### Execution Priority Invariance
![[Pasted image 20231030150240.png]]
- Each execution thread (ET) is assigned one or more EQ in each PG.
- Since EQs are planned independently by each planner, the following degenerate case may occur
	- two planner threads $PT_0, PT_1$ two execution threads $ET_0, ET_1$ 
	- A total of four EQs are planned in the batch. Each EQ is denoted as $EQ_{ij}$, 
	  ($i$-planner threads // $j$ - execution thread)
	- for each EQ, there is an associated RID range $r_{ij}$ 
	- A violation of the *`execution priority invariance`*:
		1. $ET_0$ start executing $EQ_{10}$
		2. $ET_1$ has not completed the execution of $EQ_{01}$
		3. a fragment in $EQ_{01}$ updates a record, while a fragment in $EQ_{01}$ reads the same record (this implies that $r_{10}$ overlaps with $r_{01}$).
	- [[QueCC.pdf#page=6&selection=208,3,216,61|how to ensure such invariance]]

#### Commit Dependency Tracking

- execution threads need to track inter-transaction commit dependencies.
- When a transaction fragment speculatively reads uncommitted data written by a fragment that belongs to another transaction in the batch, a commit dependency is formed between the two transactions
- This dependency must be checked during commitment (or as soon as all prior transactions are fully executed) to ensure that the earlier transaction has committed. 
- If the earlier transaction is aborted, the later transaction must abort
-  This depen- dency information is stored in the transaction context
-  To capture such dependencies, QueCC main- tains the transaction id of the last transaction that updated a record in per-record meta-data
	-  During execution, the transaction ID is checked and if it refers to a transaction that belongs to the current batch, a commit dependency counter for the current transaction is incremented and a pointer to the current transaction’s context is added to the context of the other transaction
	- During the com- mit stage, when a transaction is committing, the counters for all dependent transactions is decremented. When the commit depen- dency counter is equal to zero, the transaction is allowed to commit
	- Once all execution threads are done with their assigned work, the batch goes through a commit stage. This can be done in parallel by multiple threads.

### (3) QueCC Implementation Details

#### Plan Delivery
![[Pasted image 20231030150240.png]]
- use a simple lock-free delivery mechanism using atomic operations
- utilize a shared data structure called BatchQueue, which is basically a circular buffer that contains slots for each batch
	- Each batch slot contains pointers to partitions of priority groups which are set in a latch-free manner using atomic CAS operation
	- Priority group partitions are assigned to execution threads.
- Delivering priority group partitions to the execution layer must be efficient and lightweight. --> latch-free mechanism
	1.  Execution threads spin on priority group partition slots while they are not set (i.e., their values is zero)
	2.  Once the priority groups are ready to be delivered, planner threads merge EQs into priority group parti- tions such that the workload is balanced, and each priority group partition is assigned to one execution thread.
	-  EQs can be assigned dynamically by adapting to the workload or deterministically
	-  To achieve balanced workload among execution threads --> simple greedy
		-  keeps track of how many transaction fragments are assigned to each execution thread
		- It iterates over the remaining unassigned EQs until all EQs are assigned
		- In each iteration, it assigns an EQ to the worker with the lowest load
- 

#### RID Management



