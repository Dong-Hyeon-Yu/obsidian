#ICDCS #MVCC #ConflictGraph #EVM #ConcurrencyControl #Reordering 


**Title:** 
NEZHA: Exploiting Concurrency for Transaction Processing in DAG-based Blockchains 

**Authors:** 
J. Xiao, S. Zhang, Z. Zhang, B. Li, X. Dai and H. Jin

**Journal/Conference:**
ICDCS '22

**Sources:**
[https://ieeexplore.ieee.org/document/9912285](https://ieeexplore.ieee.org/document/9912285)

**Abstract:**
A Directed Acyclic Graph (DAG)-based blockchain with its inherent parallel structure can potentially significantly improve the throughput performance over conventional blockchains. Such a performance improvement can be further enhanced through concurrent transaction processing. This, however, brings new challenges in concurrency control design in that there is an increased number of concurrent reads and writes to the same address in a DAG-based blockchain, which leads to a considerable rise of potential conflicts. Therefore, one critical problem is how to effectively and efficiently detect and order conflicting transactions. In this work, for the first time, we aim to improve system throughput and processing latency by exploring the address dependencies among different transactions. We propose NEZHA, an efficient concurrency control scheme for DAG-based blockchains. Specifically, NEZHA intelligently constructs an address-based conflict graph (ACG) while incorporating address dependencies as edges to capture all conflicting transactions. To generate a total order between transactions, we propose a hierarchical sorting (HS) algorithm to derive sorting ranks of addresses based on the ACG and sort transactions on each address. Extensive experiments demonstrate that, even under high data contention, NEZHA can increase the throughput over the conventional conflict graph scheme by up to 8 ×, while decreasing the transaction processing latency up to 10 ×.


---

## Key features

### (1) ACG
- $RW_i$ : [[Nezha_Exploiting_Concurrency_for_Transaction_Processing_in_DAG-based_Blockchains.pdf#page=5&selection=775,0,787,7|A set of transactions which access to Address_i]]
- read/write unit $T_v^R$, $T_v^W$: [[Nezha_Exploiting_Concurrency_for_Transaction_Processing_in_DAG-based_Blockchains.pdf#page=5&selection=787,9,808,15|a set of read/write operations == read/write keys]]
- [[Nezha_Exploiting_Concurrency_for_Transaction_Processing_in_DAG-based_Blockchains.pdf#page=6&selection=198,0,261,1|Address dependency is captured according to *write-read* dependency]] 
- 

### (2) Hierarchical Sorting


### (3) Reordering
- [[Nezha_Exploiting_Concurrency_for_Transaction_Processing_in_DAG-based_Blockchains.pdf#page=8&selection=936,0,938,56|ww-dependency can only be reordered]]
- 

---

## Implementation specifics


Q. How to model smart contract-based benchmark in ethereum?
  
  - Fabric의 chaincode 에서는 address를 구조체로 선언하여 관리 가능 -> world state에서 key로 관리됨.
  - EVM에서도 동일하게 world state 의 key로 관리할 수 있음.
	```solidity
	// 1)
	struct Account {
		uint balance;
		uint savings;
	}
	
	mapping (address=>Account);
	
	// 2)
	mapping (address=>uint256) balance;
	mapping (address=>uint256) savings;
	```
	- 
	- check [here](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html) how the struct variables are stored in EVM state
      ![[Pasted image 20231018195126.png]]

- ACG contruction
	```rust 
	for tx in tx_list {
		let SimulatedTransaction {tx_id, rw_set} = tx;
		
	
	}
	
```
  
- [ ] simulation 할 때 arc?
- [ ] RW units에 key-value 값을 넣는 것과 key-operation 을 넣을 때의 퍼포먼스가 달라질 수 있음... how..?
- [ ] shutdown conditional channel 개수 설정해야함.
- [ ] simulation 끝난 후 접근한 address별 r/w set 호출하도록 해야 함.
- [ ] serial execution `execution_storage::MemoryStorage.executed_tx` 의 최소 범위 지정하기. (주로 concurrent execution 때문인데..;;)


- [x] async move를 추가하면 내부 비동기 함수는 await을 사용해야함..? 무슨 차이?
- [x] metrics을 뽑아내려면 batch digest를 모두 받아와야함.
- [x] [CPU-bound jobs should be performed in a sepated thread pool](https://thenewstack.io/using-rustlangs-async-tokio-runtime-for-cpu-bound-tasks/)
- [ ] 