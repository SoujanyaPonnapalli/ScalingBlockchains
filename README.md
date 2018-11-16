# Research on Scaling Blockchains

*Scalability is one of the major limitations of blockchains. This repository summarizes the ongoing research on increasing the scalability of blockchains and provides a high level overview on blockchains.* 

[Blockchains](https://en.wikipedia.org/wiki/Blockchain) are public, distributed, 
[decentralized](https://en.wikipedia.org/wiki/Decentralization) ledgers which record data 
that cannot be tampered with. Data is stored in blocks, where each block is built on top of a previous block, forming a 
chain of blocks (blockchain). Nodes in a distributed peer-to-peer network agree on a chain of blocks (via consensus). 
Consensus establishes a single view of the data on the blockchain with multiple, mutually untrusting nodes in the network.

Blockchains suffer with poor performance and low scalability as they form a network of **replicated state machines** and run 
**consensus** amongst all of them and also because of the **inherent sequentiality** in blockchains (Blocks are 
sequentially built on top of one another), as explained in detail in the 
[overview on blockchains.](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Overview.md)

## Repository Contents 

This repository summarizes the papers aiming at scaling blockchains. The efforts towards scaling blockchains are placed along these three axes: 

**[Architecture](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/tree/master/Architecture) (New architectures for scalable blockchains)**

  - [Hyperledger](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Architecture/HyperLedger.md): 
  A novel execute-order-validate architecture for permissioned blockchain systems.
  
**[Consensus](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/tree/master/Consensus) (Efficient and scalable alternatives to Nakamoto consensus)**

  - [Algorand](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/Algorand.md):
   Byzantine agreement amongst a few users, selected using verifiable random functions.
   
  - [Bitcoin-NG](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/BitcoinNG.md): 
  A leader is selected using Nakamoto consensus and serializes transactions until a new leader is chosen.
  
  - [Conflux](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/Conflux.md):
  Concurrent blocks are optimistically processed without discarding any as forks. 
  
**[General](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/tree/master/General) (General ideas and alternatives towards scaling blockchains)**
  
  - [GHOST](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/General/GHOST.md):
  Novel construction and reorganization of blockchain (Greedy Heaviest-Observed Sub-Tree)

## Quick Summary 

*Let's take a quick look at the major contributions towards scaling blockchains.*

A summary of the peak reported throughput (number of transactions per second or tps), corresponding transaction confirmation 
latencies and the configuration parameters.

| Paper         | Peak Throughput (tps)  | Txn Confirmation Latency | Block Size, Rate of Blocks (Details) |
| ------------- | ---------------------- | -------------------------|------------------------------|
| [Algorand](https://dl.acm.org/citation.cfm?id=3132757)              | 875 |  ~ 1 minute   | 10 MB, 10 minutes |
| [Conflux](https://arxiv.org/abs/1805.03870)                         | 6400 | 4.5 - 7.5 minutes| 4MB, 2.5 seconds |
| [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399)           | 1500 |  < 6 minutes     |  (104 nodes) |
| [HyperLedger](https://dl.acm.org/citation.cfm?id=3190538)           | 3000 | 0.3 - 0.5 seconds| 2 MB (32 vCPUs) |
| [OmniLedger](https://ieeexplore.ieee.org/abstract/document/8418625) | 13,000 | ~ 1 minute     |16 MB  (800 hosts, 25 shards, 12.5% adversary) |

A summary of the maximum number of peers or nodes reported in the paper. If the scalability is reported against the total 
number of nodes in the network, then the type of the nodes is "T". If scalability is reported against the number of nodes
participating in the consensus, then the type of the nodes is "C".

| Paper                                                    | # Nodes (Type) | Config (VMs, nodes per VM)| Geo-distribution|
| --------------------------------------------------------------------| ---------- |--------------------------------|-----|
| [Algorand](https://dl.acm.org/citation.cfm?id=3132757)              |   500,000 (T)| 1000, 500 | 20 cities    |
| [Conflux](https://arxiv.org/abs/1805.03870)                         |    20,000 (T)|  800,  25 | 20 cities    |
| [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399)           |       104 (C)|  104,   1 |  5 continents |
| [HyperLedger](https://dl.acm.org/citation.cfm?id=3190538)           |3 (C), 100 (T)| 20 per datacenter |  5 datacenters |
| [OmniLedger](https://ieeexplore.ieee.org/abstract/document/8418625) |      1800 (T)|   60,  30 | Limit bandwidth, latency|

**Note:** 
- The numbers are not presented for a direct comparison between the systems.
- Comparing different blockchain based systems is very challenging as there are numerous aspects affecting the transaction 
throughput of these systems, as summarized in this 
[blog post](https://medium.com/algorand/towards-a-unified-metric-for-performance-evaluation-of-proof-of-stake-blockchains-e29a405d73ba).
- There is a lot of interesting ongoing work on analyzing different attacks and the security of blockchains, proposing 
applications of blockchains in various fields and so on... However, these are not a part of the current study.


## Contact Info 

Please let me know if there are any papers that could be a good addition to the list by writing to me at soujanya.ponnapalli@utexas.edu.
