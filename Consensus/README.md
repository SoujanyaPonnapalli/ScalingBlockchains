# Consensus

This directory summarizes papers which propose scalable alternatives to proof-of-work or Nakamoto consensus aiming at
scaling Blockchains to handle around thousands of transactions per second.

## Table of Contents

1. [Algorand](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/Algorand.md)
  : Introduces a scalable cryptocurrency with a novel Byzantine Agreement Protocol for consensus. To achieve scalable 
  consensus, Byzantine Agreement is performed amongst a few users, selected using a novel mechanism 
  based on Verifiable Random Functions.
  
2. [Bitcoin-NG](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/BitcoinNG.md)
: A scalable blockchain, which divides the time into epochs, where each epoch has a single leader, who is responsible for 
serializing transactions unilaterally until a new leader is chosen, achieving high throughput. The leader election is 
performed using Nakamoto Consensus.

3. [Conflux](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Consensus/Conflux.md)
: Optimistically processes concurrent blocks without discarding any as forks. Conflux represents the blockchain as a direct acyclic graph (DAG) and achieves consensus on a total order of the blocks and deterministically derives a transaction total order from the block order.

<!-- 4. [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399) --> 
<!-- 4. [Inclusive Blockchains](https://fc15.ifca.ai/preproceedings/paper_101.pdf)--->

Next up: [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399)
