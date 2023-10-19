#IeeeSP #DynamicBFT 

**Author:**
Sisi Duan, HaiBin Zhang  

**Source:**
[https://ieeexplore.ieee.org/document/9833787/authors#authors](https://ieeexplore.ieee.org/document/9833787/authors#authors)  

**Abstract:**
This paper studies dynamic BFT, where replicas can join and leave the system dynamically, a primitive that is nowadays increasingly needed. We provide a formal treatment for dynamic BFT protocols, endowing them with a flexible syntax and various security definitions.We demonstrate the challenges of extending static BFT to dynamic BFT. Then we design and implement Dyno, a highly efficient dynamic BFT protocol under the partial synchrony model. We show that Dyno can seamlessly handle membership changes without incurring performance degradation.

--- 

### Challenges

- a correct leader might not obtain enough view change messages.
- the security properties of static BFT might not hold.

## Definitions (9p)

- `correct replicas in configuration c` 
  --> no guarantee that they are correct in $c'>c$ 
- `c-correct` 
	1. a replica $p$ correct in $c$
	2.  (1) no correct replicas in c install any configurations greater than c 
	   or (2) some correct replicas in $c$ install $c+1$ --> belong to the membership of $c+1$
- `c-faulty`
	1. incorrect replica ??
	2. some replica removed fro the groupt before $c+1$ ??
- `g-correct`
- `$g_c$-correct` 
## Property specification (10p)

- agreement / total order / liveness / integrity
- _**Same configuration delivery**_: if a correct replica $p_i$ delivers m in $c_i$ --> $p_j$ delivers m in $c_i$ 
- _**Consistent delivery**_: 클라이언트에게 message가 $c$에서 최종적으로 응답 되었을 때, 그 응답은 $c$의 state와 consistent하다.

## Dyno

### Temporary membership

### (1) join request (from new replica)
1. obtain the latest configuration (oracle)
2. broadcast a join request
3. wait until the join request is delivered
4. state transfer with existing replicas
5. install new configuration

### (2) leave request 
1. broadcast a join request
2. wait until the join request is delivered
3. install new configuration

### (3) normal request 
1. broadcast a join request
2. wait until the join request is delivered
3. install new configuration

### (4) view-change request 
trigger는 기존 PBFT의 조건과 같음. primary로부터 응답이 없을 때.
1. broadcast a view change message with currenct configuration number $c$
2. upon receiving
	(1) 
	(2)
	(3)
3. new leader

Q. (12p) agreement
Q. configuration history ? oracle 시에 한명한테만 받는데, 이걸 위조할 수 있으면 위험. (19p)
Q. (22p) main construction?
Q. (24p) b가 뭐지?
Q. (25p) throughput 을 왜 다른 것과 비교하지?
Q. State transfer?


[Dynamic Byzantine Reliable Broadcast (OPODIS '20')](https://arxiv.org/abs/2001.06271)
BRB is weaker than consensus
static membership is assumed.
