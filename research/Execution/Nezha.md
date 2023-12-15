#ICDCS #MVCC #ConflictGraph #EVM #ConcurrencyControl #Reordering 
#SpeculativeWriteVisibility 


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
	* address 내에서의 정렬은 쉬운 문제. 
	- RW units 간 순서를 reorder하지 않음. 
	- 따라서 배열 형태로  표현해도 문제 없을 듯.
  
- [ ] simulation 할 때 arc?
- [ ] RW units에 key-value 값을 넣는 것과 key-operation 을 넣을 때의 퍼포먼스가 달라질 수 있음... how..?
- [ ] shutdown conditional channel 개수 설정해야함.
- [ ] simulation 끝난 후 접근한 address별 r/w set 호출하도록 해야 함.
- [ ] serial execution `execution_storage::MemoryStorage.executed_tx` 의 최소 범위 지정하기. (주로 concurrent execution 때문인데..;;)


- [x] async move를 추가하면 내부 비동기 함수는 await을 사용해야함..? 무슨 차이?
- [x] metrics을 뽑아내려면 batch digest를 모두 받아와야함.
- [x] [CPU-bound jobs should be performed in a sepated thread pool](https://thenewstack.io/using-rustlangs-async-tokio-runtime-for-cpu-bound-tasks/)

- [x] 앵커블럭 제외한 다른 블럭 네자 (block concurrency 조절해보기)
	1) 블럭 하나씩
	2) 블럭 뭉탱이 (앵커는 하나씩)

- [x] committed subdag의 평균 크기
- [x] subdag 크기 로그를 consensus handler 에서..?
- [x] execution이 bottleneck이 시작되는 구간부터 block concurrency level 을 측정
	- bottlenect 구간에는 batchsize가 특정 값 (약 240KB) 를 지나면서 진입. 
	- batchsize를 240KB 이상의 값으로 고정 / send rate 고정 --> block concurrency level 조절
	- batchsize: 300KB / 'concurrency_level': \[1, 2, 3, 4, 5, 6, 8, 10, 12, 14]
- [x] smallback workload abort 분석.
- [ ] parallelism metric 개발.
- [x] nezha parallel simulation 할 때 arc 로 snapshot 넘겨주는 게 좋을 듯..? 
	- no. scoped thread 이용하면 그냥 reference로 넘겨줄 수 있음.

- [ ] concurrency hashmap 비교해보기
	- lock을 사용하는 hashmap은 상위 계층의 hashmap 에 사용될 수 없음.
	- 상위 계층은 lock-free hashmap & 읽기 성능이 좋은 것.
	- 리프 계층은 쓰기 성능이 좋은 것.
	- [x] flurry --> lock-free 
	- [x] dashmap
	- [x] sharded 사용못함 clone / copy 없음
	- [x] leapfrog 사용못함 clone / copy 없음
	- [x] skiplist --> lock-free 사용못함 clone / copy 없음
- [x] concurrent commit (rust unsafe)
- [x] experiments with multiple block concurrency levels

2023-12-01
- [x] serial commit / parallel commit latency breakdown
- [ ] blockchain network size가 커질 때, nezha concurrency level을 어떻게 조절해야할지
- [x] parallelism metric 정하기
	- height가 높으면 좋음.
	- depth가 낮으면 좋음.
	- parallelism = $average(\mathbf{H})$  
- [x] parallel commit chuck size 조절해보기 ->chunk size 가 1일 때 성능이 가장 좋음.
- [ ] crossbeam cachePadding 적용해보기.

2023-12-08
- [x] 실험 concurrency manager 의 concurrency level 확인해보기
- [ ] committed subdag 를 anchor node 기준으로 잘라서 처리하기 --> nezha integration test
- [ ] block stm 성능 측정해보기
- [ ] nezha concurrency level을 어떻게 조절해야할지
- [x] parallelism = $(average(\mathbf{H}), deviation(\mathbf{H}))$
