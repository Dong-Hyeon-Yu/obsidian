#AsynchronousBFT #BinaryValueBroadcast #RandomCoin #PODC


Authors : 
Mostéfaoui, A., Moumen, H., & Raynal, M

Journal/Conference: 
_2014 ACM symposium on Principles of distributed computing_

source: 
[https://dl.acm.org/doi/abs/10.1145/2611462.2611468](https://dl.acm.org/doi/abs/10.1145/2611462.2611468) 

  
---

## \[Abstract\]

This paper presents a new round-based asynchronous consensus algorithm that copes with up to t<n/3 Byzantine processes, where n is the total number of processes. In addition of not using signature, not assuming a computationally-limited adversary, while being optimal with respect to the value of t, this algorithm has several noteworthy properties: the expected number of rounds to decide is four, each round is composed of two or three communication steps and involves O(n2) messages, and a message is composed of a round number plus a single bit. To attain this goal, the consensus algorithm relies on a common coin as defined by Rabin, and a new extremely simple and powerful broadcast abstraction suited to binary values. The main target when designing this algorithm was to obtain a cheap and simple algorithm. This was motivated by the fact that, among the first-class properties, simplicity --albeit sometimes under-estimated or even ignored-- is a major one.

---

## \[Contributions\]

- BV-broadcast  
      : focus on values not on process, which not considering that "this" value has been broadcast by "this" process.  
      : reduce the power of consensus in such a way that a value broadcast only Byzantine processes is never delivered to the correct processes.
- \(t<n/3\) optimal with repect to \(t\).
- expected number of rounds to decide is 4, per which 2~3 communication steps are required only. 
- If there are n parallel BA processes, expected number of rounds is \(O(logn)\)
- Message complexity is \(O(n^2)\) messages per round. Each message consist of (\(type\), \#\(round\), 1-\(bit\))

---

## \[Computation Model\]

- Network  
       (_**Asynchronous**_ : no bound for message delivery delay)  
      + (_**Reliable**_ : no loss, duplicated, modified, and created)  
      + (_**Point-to-point**_ : bi-directional channels which are able to identify sender)  
      + (_**Unreliable broadcast**_ : best-effort broadcast)   
- Failure  
      : Byzantine process can send specific messages to others selectively. But no byzantine process can impersonate another process since they are connected by channels. Also cannot affect network reliability.
- Step  
      : A (communication) step consists of receiving a message from another process, executing a local computation, and sending a message to some process.  
    

---

## [BV-broadcast]

![](https://blog.kakaocdn.net/dn/NOjKp/btr2CvEGNhO/fKFrxjrljXHMk6Ty1bwe40/img.png)

- the method makes the consensus progress when at least \(t+1\) correct processes call "\(BV\)-\(broadcast\)"\[BV-Obligation\]. (but if there are at least \(n-t\) correct processes, eventually \(BV\)-\(broadcast\) terminate. \[BV-Termination\])
- Best case: all the process \(broadcast\) a same value. \(broadcast\) occur only once per a process. (1 step)
- Worst case: there are two values \(broadcasted\) by at least \(t+1\) processes. Then \(broadcast\) occur twice per a process. (2 steps) In this case, the local variable \(bin\)-\(values_i\) has all the binary value {0, 1}.
- 1~2 steps are needed.

![](https://blog.kakaocdn.net/dn/vQP24/btr2OtE1Nes/kvBzHWm2q91lfE0xZuKpek/img.png)

security properties of Binary Value Broadcast

---

## Randomized Byzantine Consensus Algorithm

![](https://blog.kakaocdn.net/dn/bZRYzO/btr2IvXLr0c/7CZOpypWr5GXsjPBxluPmk/img.png)

There are n replicas in the network, and each replica has n parallel BA(asynchronous binary Byzantine Agreement) instances which communicate with corresponding replica.

  A process \(BA_1\) is executed, which means that all \(BA_1\) of \(Replica_k (k \in \[1...n\])\) conduct "asynchronous binary Byzantine Agreement (look below codes)" to decide whether include \(Replica_1\) in the final result or not.

  

![](https://blog.kakaocdn.net/dn/E4QfN/btr2DDiePVk/0wWBRmYfF3KUEbm9gKUznK/img.png)

- After disperse phase(\(BV\)-\(broadcast\)), \(broadcast\) the first value of \(bin\)-\(values_i\) and wait \(n-f\) response values (AUX).
- Since each process choice only the "first-arrived" value, it is not guaranteed that each replica has a same value at this point. Therefore each process exchane the first value by \(broadcast\)ing AUX messages, and then check whether there is only one value. Otherwise all replicas repeat additional rounds with initial value(\(est_i\)) set by common coin. In this case, expected the number of rounds is 4. (2 rounds as mentioned, and coin probability 50%).
- Common coin play a role to guarantee that all the replicas have the same value. This role can be replaced by additional broadcast(Achour Mostéfaoui, Hamouma Moumen, and Michel Raynal. 2015. SignatureFree Asynchronous Binary Byzantine Consensus with t < n/3, O(n2) Messages, and O(1) Expected Time. J. ACM 62, 4 (2015), 31:1–31:21.), and etc.