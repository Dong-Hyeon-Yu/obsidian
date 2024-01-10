
VLDB '23

- blockchain database?
- motivation: cross-shard data transaction 처리 방법은 기존에 잘 연구되어 왔는데, cross-shard query transaction에 대해서는 잘 다뤄지지 않았음. 
- blockchain database sharding
	- aggregation과 balancing 두 기능을 제공해야함.
	- aggregation: 데이터가 서로 다른 shard에 저장됨
	- load balancing 문제가 발생 -> migration (data를 분산시키는 것.)
	- 이런 기능들이 on-chain 상에서 ctx 로 처리되는 것이 성능 저하의 이유임.
	  --> ctx의 off-chain execution을 가능하게 함. 특정 노드가 ctx를 전담하게 하고 그에 대한 authentication data structure를 유지. (모든 노드들은 각 테이블이 어떤 샤드에 위치하고 있는지 알고 있다는 것이 가정.)

- tx 유형
	1. data transaction
	2. query transaction: off-chain storage에 실행을 전가하고, 블럭에는 쿼리 결과에 대한 해시값만 저장하는 방식으로 수행가능.

p8.
- 클라이언트가 어떤 샤드에 어떤 테이블이 있는지 어떻게 알고, request할 노드를 선정하지?
- cross shard query transaction 이 발생했을 때, 어떻게 진행되는지?
- database의 shard와 어떤게 다른지? 

- Challenges
	1. cross-shard query
		- naive solution: shard-cooperation approach
		- solution: cross-shard query authentication
		- 
	2. inter-shard workload imbalance
		- naive solution: stop-restart migration approach
		- solution: off-chain live migration approach
			- init mode: table 에 대한 metadata를 생성하는 단계. async문제를 방지하기 위해 이 단계에서는 data processing을 중단.
			- dual mode: ctx에 대해 increasing sequence 번호를 부여 + 트랜잭션에 대해 merkle proof 제공...?
- p22.
	- query 가 왜 더 heavy한 operation인지?
	- 
