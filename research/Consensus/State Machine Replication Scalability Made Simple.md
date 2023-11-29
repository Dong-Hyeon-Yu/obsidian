
EuroSys 22

[https://dl.acm.org/doi/10.1145/3492321.3519579](https://dl.acm.org/doi/10.1145/3492321.3519579)

problems
- single leader TOB: single leader bottleneck
- multi leader TOB: duplicate requests
	--> Mir-BFT (mitigate duplicate requests), epoch-change msg (= view-change)

ISS: Insanely Scalable state machine replication
- parallel-leader protocol w/o epoch primary
- duplication prevention (from Mir-BFT)
- simple & modular design

### Partitioning the requests to buckets
- buckets are partitioned based on hashspace. 
- each request is located into specific buckets according to the hash of it.
- each node has its own buckets (specific hashspace)
- bucket을 consensus (TOB)에 태우고 deliver 되면 SB에 태워서 노드 간 커밋한 buckets에 대한 total order를 정함.

### Epochs and Bucket Reassignment
- p15..?

### Fault management
- bucket을 채우지 못하면 다른 노드가 대신 그 bucket을 채운 후 SB를 진행함.
- **다른 노드가 faulty한 노드를 대신할 때, 대신할 노드를 어떻게 선택? (17p)**

### scalability of single leader protocols
- **p21) pbft는 초반에 증가한 후 떨어지는데, hotstuff & raft는 점점 증가함.. 왜지?**
- **p22) ISS를 적용했을 때, latency 측면에서 불리한 점은 없나?**

### tps with one crash fault
- **p23, epoch change 때문에 순간적으로 0으로 떨어지고, tps가 그 이후에는 갑자기 솟음...?**


**q. request hash 값에 따라서 실행 순서가 달라질 수 있지 않나..?**
q. ISS overview. node 개수가 100개 라면 TOB가 100개 돌아가는 거..?
q. (10p) byzantine node 가 duplication attack을 하면? 어떻게 막음?
q. 일종의 shared mempool. 어떻게 mempool synchronize를 보장함?