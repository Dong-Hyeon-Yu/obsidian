#ICDE #ConcurrencyControl #EVM #ConflictGraph #InterBlockConcurrency #Partition


**Title:**

SChain: Scalable Concurrency over Flexible Permissioned Blockchain

**Authors:**

Xiaodong Qi; Zhihao Chen; Haizhen Zhuo; Quanqing Xu; Chengyu Zhu; Zhao Zhang; Cheqing Jin; Aoying Zhou; Ying Yan; Hui Zhang

**Journal/Conference:**

ICDE '23

**Source:**

[https://arxiv.org/abs/2306.03058](https://arxiv.org/abs/2306.03058)

**Abstract:**
Permissioned blockchains are being widely applied to solve the trust problem in enterprise collaboration. However, most of these systems suffer from low throughput and flexibility lacking issues. In this paper, we present a blockchain system SChain with scalable concurrent execution based on a flexible architecture. SChain separates the functionality of a complete "node" into three sub-functions and assigns them to different peers within every organization. Then each organization can scale each sub-function flexibly with no need for negotiation between organizations. Based on this architecture, SChain explores scalable concurrent execution from two levels. First, SChain takes the advantage of multiple peers to execute transactions collectively, while promising they make the same results as one peer does serially. Second, SChain enables concurrent transaction execution across blocks to utilize the resources of peers fully, breaking up the block-by-block process manner, based on a pipelined workflow. The extensive evaluation results demonstrate that SChain significantly outperforms the serial execution and other competing systems-level approaches.