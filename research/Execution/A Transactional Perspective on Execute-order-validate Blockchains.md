
# Introduction

[[A Transactional Perspective on Execute-order-validate Blockchains.pdf#page=2&selection=74,0,78,11|th respect to the endorsement policy and serializability. The new EOV architecture limits the execution details of a transaction to the endorsing peers to enhance confidential- ity and exploit concurrency. But such concurrency comes at the cost of aborting transactions that do not abide serial- izability. ]]

- [[A Transactional Perspective on Execute-order-validate Blockchains.pdf#page=2&selection=91,0,92,5|There are two notable directions attempting to solve this issue.]]
	1. The first is to improve upon Fabric’s architecture to enhance its attainable throughput
	2. The second direction is to abstract out the transaction lifecycle to reduce abort rate.

- Our work corresponds to the second direction, as a major attempt to databasify blockchains.
	- our proposal consists of a novel reordering technique that eliminates unnecessary abort due to in-ledger conflicts, with the serializability guarantee es- tablished on our theoretical insights. 