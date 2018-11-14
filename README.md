# Research on Scaling Blockchains

*Scalability is one of the major limitations of blockchains. This repository summarizes the ongoing research on increasing the scalability of blockchains and provides a high level overview on blockchains.* 

## Repository Contents ##

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

## Quick Summary ##

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
number of nodes in the network, then the type of the nodes is "T". If scalability is reported against the number of 
participating in the consensus, then the type of the nodes is "C".

| Paper                                                    | # Nodes (Type) | Config (VMs, nodes per VM)| Geo-distribution|
| --------------------------------------------------------------------| ---------- |--------------------------------|-----|
| [Algorand](https://dl.acm.org/citation.cfm?id=3132757)              |   500,000 (T)| 1000, 500 | 20 cities    |
| [Conflux](https://arxiv.org/abs/1805.03870)                         |    20,000 (T)|  800,  25 | 20 cities    |
| [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399)           |       104 (C)|  104,   1 |  5 continents |
| [HyperLedger](https://dl.acm.org/citation.cfm?id=3190538)           |3 (C), 100 (T)| 20 per datacenter |  5 datacenters |
| [OmniLedger](https://ieeexplore.ieee.org/abstract/document/8418625) |      1800 (T)|   60,  30 | Limit bandwidth, latency|

**Note:** 
- The results are from the papers for a configuration with around 100 nodes. However, the numbers are not presented for a 
direct comparison between the systems, but for an average throughput and latency estimates of the current state-of-the-art.
- There is a lot of interesting ongoing work on analyzing different attacks and the security of blockchains, proposing 
applications of blockchains in various fields and so on... However, these are not a part of the current study.


## Blockchains Overview ##

*Before diving right into the research on scaling blockchains, 
let's try understanding blockchains and their current design at a high level.*

1. [Blockchains](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#blockchains)  
2. [Design and Architecture](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#design-and-architecture)  
3. [Limitations and Research Trends](https://github.com/SoujanyaPonnapalli/MyBlockchainPage/blob/master/README.md#limitations-and-research-trends)
4. [Contact Info](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#contact-info)

# Blockchains

Blockchains are widely used for building peer to peer payment systems, without the involvement of a centralized authority. 
Traditionally in a centralized payment system, a trusted third party is responsible for verifying, executing and committing 
every transaction to prevent [double spending](https://en.wikipedia.org/wiki/Double-spending). Blockchains are introduced in 
[How to Time-Stamp a Digital Document](https://www.anf.es/pdf/Haber_Stornetta.pdf) and are later used in 
[Bitcoin: A Peer-to-Peer Electronic Cash System](https://www.bitcoin.org/bitcoin.pdf) ([Bitcoin](https://bitcoin.org/en/)), 
to prevent double spending in an untrusted, purely peer-to-peer payment system.

*Let's take a closer look at the design and architecture of blockchains based application platforms.*

# Design and Architecture

*We will be looking at the architecture of [Ethereum](https://github.com/ethereum), a blockchain based application platform 
for building distributed decentralized applications like cryptocurrencies ([Ethereum](https://www.ethereum.org)) or 
[cryptokitties](https://www.cryptokitties.co).*

**Network:**  
A distributed peer-to-peer network of participating nodes compose the Ethereum network. Nodes with different functionalities 
([light nodes](https://github.com/ethereum/wiki/wiki/Light-client-protocol), full nodes etc...) are connected without any 
hierarchy. However, nodes storing the entire blockchain and capable of extending the blockchain (by creating new blocks) are 
termed "full nodes". Full nodes propagate information (new transactions and/or blocks) via variants of Gossip protocol. Every 
full node connects with a set of peers and stores the entire blockchain and its state.

**State:**  
The state stored by Ethereum can be understood as key, value pair mappings of user's account addresses to their accounts. 
However, since nodes in the network do not trust one another, Ethereum implements authenticated storage. On reading a key from 
the authenticated storage, the value corresponding to the key and a proof are returned. The proof can be used by any remote 
user to verify the value. Authenticated storage is implemented by storing the account addresses and balances in a 
[Merkle Patricia Trie](https://github.com/ethereum/wiki/wiki/Patricia-Tree#main-specification-merkle-patricia-trie), 
where every parent node in the Trie stores the combined hash of all of its children. Consequently, the root node of the Trie 
hashes all the user data in the state Trie. So the hash of the state Trie's root node (state root hash), is used as a label 
for the current state of Ethereum. 

**Blockchain:**  

Blockchains store the history of transactions so that every full node in the Ethereum network has access to the entire history 
of transactions and hence can verify new transactions without the need for a trusted third party. Firstly, the blockchain is 
initialized with the genesis block. Every block builds on top of a previous block, hence forming a chain of blocks. Blocks at 
the least, contain a list of transactions, a hash of its parent block and the state root hash representing the current 
Ethereum's state. Full nodes on receiving new transactions append them to their transaction pools and propagate them to their 
peers. Full nodes attempt to solve a computationally intensive cryptographic puzzle for earning credits towards creating a new 
blocks. Once a full node solves the puzzle, it batches a few new transactions, verifies them against the blockchain (storing 
the current history), executes them and updates the Ethereum state Trie. It then creates a new block with the updated state 
root hash and propagates it to all the full nodes in the network. Full nodes verify the proof-of-work of the new block, a 
proof that the block was created after solving the cryptographic puzzle. Full nodes then verify the transactions in the block, 
execute them, update their local state and add the block to their blockchain. After a majority of nodes process the newly 
created block, it becomes a part of the blockchain.  

*Full nodes behave as replicated state machines, transitioning to new states on the arrival of new blocks.*

**Example:**  
> Assume that genesis initializes two user accounts A and B with 10 eth each (eth stands for Ether, where 1 Ether is approx 
> 192 USD). Consider a new transaction where user A transfers 5 eth to user B. Full nodes propagate this new transaction to 
> every other full node in the network. Full nodes attempt to solve the cryptographic puzzle. Once a full node solves the 
> puzzle, it creates a new block B1 with new transactions. Assuming B1 has only one transaction, where user A sends 5 eth to 
> user B, full node verifies the transaction: checks if user A has 5eth and if user B has a valid account then, executes the 
> transaction and updates the current state: updates user A's account to have 5 eth and user B's account to have 15 
> eth. The block B1 contains a list of transactions, a hash of its parent block (genesis) and the updated state root hash. The 
> block B1 is then broadcasted to all the other full nodes. Full nodes verify the proof-of-work of B1, verify and execute the 
> transactions and update their local state and append B1 to genesis. Thus, after a majority of nodes build B1 on top of 
> genesis, the blockchain now has genesis and B1.

**Consensus:**  
Full nodes concurrently creating new blocks can end up building two new blocks on top of same parent block resulting in forks. 
With forks, different full nodes can verify transactions against different histories, creating divergent views of the state at 
those full nodes. Thus [Nakamoto Consensus](https://bitcoin.org/bitcoin.pdf) was introduced where all the full nodes agree 
that the longest chain is the accepted history of the transactions. Once the longest chain is established and a significant 
number of blocks are created on top of a block in the longest chain, the transactions in that block are considered committed. 
The difficulty of the cryptographic puzzles is dynamically managed to maintain a uniform block creation rate. As it takes 
significant amount of time (fixed block creation rate) and computational resources (proof-of-work) to create new blocks, the 
longest chain cannot be changed and the history cannot be erased, as long as a majority of the full nodes remain 
uncompromised.

Thus, the history of transactions are maintained by every participant as an immutable chain of blocks, each block containing a 
list of transactions. So every peer in the network can verify a new transaction, without having to rely on a trusted third 
party. All the peers in the network agree on the history of transactions and agree on the same history as long as a majority 
of the peers in the network are not compromised. To conclude, with these key ideas, blockchains are used to design 
cryptographically secure, trustless, peer to peer payment system.

# Limitations and Research Trends 

*Now that we understand the design and architecture of blockchain based systems, let's understand the limitations on the 
scalability of blockchains.*

## Scalability ##

Blockchains suffer from poor performance and low scalability. While centralized payment systems process tens of thousands of 
transactions per second, blockchain based decentralized payment systems hardly support tens of transactions per second. Notice 
that every full node in the network has to verify the new blocks, transactions, re-execute the transactions, update their 
local states and extend the blockchain. Even with more full nodes in the network, since the work is not shared between the 
full nodes, the systems do not scale. Notice that, every full node participates in the consensus, making consensus a 
bottleneck for scaling blockchains.

With the increasing adoption of blockchains for various applications and the increasing demand for cryptocurrencies, the 
scalability of blockchains has taken the center stage of Blockchain research. There are numerous research papers along 
different parallels, aiming at scaling blockchains.

# Contact Info 

Please let me know if there are any papers that could be a good addition to the list by writing to me at soujanya.ponnapalli@utexas.edu.
