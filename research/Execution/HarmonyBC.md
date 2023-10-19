#DeterministicDatabase #PODS #ConcurrencyControl #InterBlockConcurrency #Reordering #ConflictGraph #DangerousStructure

**Title:**

When Private Blockchain Meets Deterministic Database

**Authors:**

Ziliang Lai, Chris Liu, Eric Lo

**Journal/Conference:**

ACM  PODS ‘23

**Source:**

[https://dl.acm.org/doi/10.1145/3588952](https://dl.acm.org/doi/10.1145/3588952)

Abstract:
Private blockchain as a replicated transactional system shares many commonalities with distributed database. However, the intimacy between private blockchain and deterministic database has never been studied. In essence, private blockchain and deterministic database both ensure replica consistency by determinism. In this paper, we present a comprehensive analysis to uncover the connections between private blockchain and deterministic database. While private blockchains have started to pursue deterministic transaction executions recently, deterministic databases have already studied deterministic concurrency control protocols for almost a decade. This motivates us to propose Harmony, a novel deterministic concurrency control protocol designed for blockchain use. We use Harmony to build a new relational blockchain, namely HarmonyBC, which features low abort rates, hotspot resiliency, and inter-block parallelism, all of which are especially important to disk-oriented blockchain. Empirical results on Smallbank, YCSB, and TPC-C show that HarmonyBC offers 2.0x to 3.5x throughput better than the state-of-the-art private blockchains.