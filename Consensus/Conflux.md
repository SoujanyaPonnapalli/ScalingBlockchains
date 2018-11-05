## ([Conflux](https://arxiv.org/abs/1805.03870)) Scaling Nakamoto Consensus to Thousands of Transactions per Second

Conflux is a scalable blockchain system that optimistically processes concurrent blocks without discarding any as forks. 
Conflux represents the blockchain as a direct acyclic graph (DAG) and achieves consensus on a total order of the blocks and 
deterministically derives a transaction total order from the block order. 

## Problems Identified

This paper identifies the performance inefficiencies of proof-of-work and Nakamoto consensus, where
- To prevent sybil attacks, every one solves proof-of-work problems to compete for the right of block generation
- To prevent double spending, every one agrees on the longest chain of blocks as the correct transaction history
- Newly generated blocks are appended to the longest chain, making it difficult to revert the previous transactions

Performance bottleneck is a critical problem for consensus algorithms based on proof-of-work 
- Only one participant can extend the blockchain
- Simultaneously generated blocks form forks, and are discarded once the longest chain is established
- The low block generation rates are critical to preventing attacks on the blockchain
  - Bitcoin generates 1MB block every 10 minutes resulting in a throughput of 7 transactions per second
- Due to the possibility of forks, we need to wait until several blocks are computed on top of a particular block to confirm the
transactions in it
  - Bitcoin waits for tens of blocks to be built on a block to confirm transactions in it, which take on the order of hours

## Proposed Solution

**Key Ideas:**
- Optimistically processing concurrent transactions and blocks as a DAG instead of a linear chain.
- To defer and arrive at a total ordering on the blocks and transactions, without discarding any concurrent blocks as forks 
- Allowing multiple participants to contribute to the blockchain, while not compromising on the security
- Increasing the block generation rate consequently scaling and increasing the throughput of Blockchains

**Assumptions:**
- As transactions rarely conflict in blockchains (particularly in cryptocurrencies), Conflux assumes that the transactions in
concurrent blocks do not conflict at a high rate
- Honest nodes are reasonably synchronous respecting a *network diameter d* 
(i.e., messages sent by honest nodes will be delivered with a maximum delay of d)
- Honest nodes together control more block generation power than attackers
- Attacker cannot reverse cryptographic functions
- Honest nodes combined together have stronger block generation power than the attacker

**Solution:**

**DAG based Blockchain:**

A key difference between Conflux and traditional blockchains is that the blocks and the edges form a DAG instead of a linear chain. 
Each node in Conflux runs a proof-of-work based block generator to generate valid new blocks with pending new transactions.
The nodes propagate these blocks and construct a local directed acyclic graph (DAG) of blocks, which each block pointing to 
multiple previous blocks. Due to network latencies, each node might observe a slightly different DAG
- Local DAGs contain two kinds of edges *parent-edges* (solid lines) and *reference-edges* (dashed lines)
- **Parent Edge**: Each block except Genesis has exactly one outgoing parent edge
- **Reference Edge**: Each block can have multiple outgoing reference edges corresponding to the generated before 
relationship between the blocks

![localDAG](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Conflux/localDAG.png)

**Consensus Protocol:**
- All nodes maintain these local DAGs and eventually agree on the same total order of blocks and transactions using Conflux
- **Pivot Chain:** Conflux selects a chain from the genesis block to one of the leaf blocks as the pivot chain
  - Conflux selects the pivot chain based on the GHOST rule instead of the longest chain rule
  - Nodes generating new blocks on the chain resulting by running GHOST protocol on their local DAGs
- Conflux to split all blocks in a DAG into epochs. Each epoch contains blocks that are
  - reachable from the corresponding block in the pivot chain via the combination of parent edges and reference edges
  - that are not included in previous epochs
- Conflux arrives at a total ordering on the blocks
  - Firstly, sorts the blocks based on their epochs
  - Then, sorts the blocks in each epoch based on their topological order
  - Finally, Conflux breaks ties deterministically with the help of block unique IDs
- Finally, Conflux arrives at a total order on the transactions
  - If two transactions belong to the same block, Conflux sorts the two transactions based on the appearance order in the block
  - If one transaction appears in multiple blocks, Conflux will only keep the first appearance, discarding all the redundant ones
  - If two transactions are conflicting with each other, Conflux will discard the second one
- Users estimate the risk of the pivot chain being reverted and hence decide whether the transactions can be confirmed or not

**Safety and Liveness:**
- The total order of transactions provided by Conflux is irreversible due to the irreversiblility of the pivot chain, where the
 pivot chain satisfies the same safety property as the selected chain in GHOST rule.
- Conflux also has the same liveness properties as the GHOST protocol

*For further details, please read the paper and the summary, 
[GHOST](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/General/GHOST.md)*

## Related Work

Previous research focuses on reducing the participants of the consensus to improve the performance without compromising the 
security of the blockchain.

- **Bitcoin-NG**: Bitcoin-NG periodically elects a leader and allows the leader to dictate the transaction total order for a 
period of time. It improves the throughput but not the confirmation time of transactions.

- **BFT Consensus**: Several proposals elect a small set of participants as a committee to run 
Byzantine fault tolerance (BFT) to determine the transaction total order. This could  create undesirable hierarchies 
among protocol participants and compromise the decentralization of the blockchains.

## Evaluation

Conflux was evaluated on Amazon EC2 clusters with up to 20k full nodes. 
Conflux achieves a transaction throughput of 2.88GB/h (20Mbps bandwidth limit) and 5.76GB/h (40Mbps bandwidth limit),
while confirming transactions in 4.5-13.8 minutes.
 - Throughput is 11.62x of the throughput of Bitcoin and GHOST, and is 3.84x of the throughput Algorand (20Mbps bandwidth limit)
 - The throughput is equivalent to 6400 transactions per second for typical Bitcoin transactions (40Mbps bandwidth limit). 
Their results also indicate that when running Conflux, the consensus protocol is no longer the throughput bottleneck. 
The bottleneck is instead at the processing capability of individual nodes.

## Results from the paper

The results below show that Conflux always has a block utilization ratio of 1, as all block eventually end up in the main chain.
The transaction confirmation latencies and the risk tolerance of Conflux are shown.

![BlockUtilization](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Conflux/BlockUtilization.png)
![LatencyAndRisk](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Conflux/LatencyAndRisk.png)
![Scalability](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Conflux/Scalability.png)