#AsychronousCommonSubset #PODC #RBC #BinaryAgreement


**Title:**
Asychronous Secure Computations with Optimal Resilience

**Author:** 
Ben-Or, M., Kelmer, B., & Rabin, T.

**Source:** 
[https://dl.acm.org/doi/pdf/10.1145/197917.198088](https://dl.acm.org/doi/pdf/10.1145/197917.198088)

**Abstract:**
We investigate the problem of multiparty computations in a fully connected, asynchronous network of n players, in which up to t Byzantine faults may occur. It was shown in [BCG93] that secure error-less multiparty computation is possible in this setting if and only if t < n/4. We show that when exponentially small probability of error is allowed, this task can be achieved even when the number of faults is in the range n/4 ~ t < n/3. From the lower bounds of [BCG93] for the asynchronous fail-stop model it follows that the resilience, t < n/3, of our protocol is optimal.

We describe an ([n/3] – 1)-resilient protocol that securely computes any function F. With overwhelming probability all the non-faulty players complete the execution of the protocol. Given that all the honest players terminate the protocol, they do so in time polynomial in n, in the boolean complexity of 3, and in Pog -$1, where c is the error probability.

Our protocol follows the scheme of [BGW88, RB89] for multiparty computations in synchronous networks, in which the intermediary results of a circuit for F are always kept shared among the players as a verifiable secret. As the asynchronous network makes it impossible to use a regular Verifiable Secret Sharing scheme for computations, we introduce a new secret sharing scheme called Ultimate Secret Sharing. This scheme guarantees that all the honest players will obtain their share of the secret, and it enables the players to verify that the shares are genuine.

![[Pasted image 20231017041410.png]]

![[Pasted image 20231017041420.png]]

- Reduce **ACS** to **RBC**(asynchronous reliable broadcast, Bracha, G. (1984, August). An asynchronous [(n-1)/3]-resilient consensus protocol. In _Proceedings of the third annual ACM symposium on Principles of distributed computing_) and **BA**(asynchronous binary Byzantine agreement, Canetti, R., & Rabin, T. (1993, June). Fast asynchronous Byzantine agreement with optimal resilience. In _Proceedings of the twenty-fifth annual ACM symposium on Theory of computing_).
- Each player RBC-broadcasts its x′ with encrypted, triggering other players RBC protocols. Finally the RBC protocol decides as RBC-deliver is successfully done and proposes 1 to corresponding BA process.
- After that, BA process run the code decribed in above figure, which resulting binary value (1-accepting the player, 0-excluding the player).