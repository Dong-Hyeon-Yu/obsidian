
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


