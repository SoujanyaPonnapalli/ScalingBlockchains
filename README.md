# MyBlockchainPage

<i> Scalability is one of the major limitations of Blockchains. This repository briefly summarizes Blockchains and the ongoing research on increasing the scalability of Blockchains. </i>

## Table of Contents ##

1. [Blockchains](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#blockchains)  
2. [Design and Architecture](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#design-and-architecture)  
3. [Scalability](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#scalability)  
4. [Repository Contents](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#repo-contents)
5. [Quick Summary](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#quick-summary)
6. [Contact Info](https://github.com/SoujanyaPonnapalli/MyBlockchainPage#contact-info)

# Blockchains

Blockchains are used for building peer to peer payment systems, without the involvement of a central authority. Traditionally in centralized payment systems, a trusted third party is responsible for verifying, executing and committing every transaction to prevent double spending. Blockchains are introduced in [How to Time-Stamp a Digital Document](https://www.anf.es/pdf/Haber_Stornetta.pdf) and are later used in [Bitcoin: A Peer-to-Peer Electronic Cash System](https://www.bitcoin.org/bitcoin.pdf) to prevent double spending in an untrusted, purely peer-to-peer payment system.

<i> Let's take a closer look at the design and architecture of Blockchains based application platforms. </i>

# Design and Architecture

<i> We will be looking at the architecture of [Ethereum](https://github.com/ethereum), a blockchain based application platform for building distributed decentralized applications like cryptocurrencies ([Bitcoin](https://bitcoin.org/en/), [Ethereum](https://www.ethereum.org)) or [cryptokitties](https://www.cryptokitties.co). </i>

<b>Network:</b>  
A distributed peer-to-peer network of participating nodes compose the Ethereum network. Nodes with different functionalities are connected without any hierarchy. However, nodes storing the entire blockchain and capable of extending the blockchain (by creating new blocks of transactions) are termed "full nodes". Full nodes propagate information (new transactions and/or blocks) via variants of Gossip protocol. Every full node connects with a set of peers and stores the entire state of the blockchain.

<b>State:</b>  
The state stored by Ethereum can be understood as key, value pair mappings of user's account addresses to their accounts. However, since nodes in the network do not trust one another, Ethereum implements authenticated storage. On reading a key from the authenticated storage, the corresponding value and a proof is returned. The proof can be used by the remote user to verify the value. Authenticated storage is implemented by storing the account addresses and balances in a [Merkle Patricia Trie](https://github.com/ethereum/wiki/wiki/Patricia-Tree#main-specification-merkle-patricia-trie), where every parent node in the Trie stores the combined hash of all of its children. Naturally, the root node of the Trie hashes all the user data in the state Trie. Hence, hash of the state Trie's root node (state root hash), is used as a label for the current state of Ethereum. 

<b>Blockchain:</b>  
Blockchains store the history of transactions so that every full node in the Ethereum network has access to the entire history of transactions and hence can verify new transactions without a trusted third party. Firstly, the blockchain is initialized with the genesis block. Every block builds on top of a previous block, hence forming the chain of blocks. Blocks contain a list of transactions, a hash of its parent block and the state root hash representing the current Ethereum's state. Full nodes on receiving new transactions append them to their transaction pools and transfer them to their peers. Full nodes attempt to solve a computationally intensive cryptographic puzzle for earning credits towards creating new blocks. Once a full node solves the puzzle, it batches a few new transactions from the transaction pool, verifies them against the blockchain (storing the current history), executes them and updates the Ethereum state Trie and creates a new block with the updated state root hash. These new blocks are propagated to all the full nodes in the network. Full nodes verify the proof-of-work (a proof that the block was created after solving the cryptographic puzzle), the transactions, execute them, update their local state and add the block to their blockchain. Thus, after a majority of nodes process the newly created block, it becomes a part of the blockchain. Thus on a high level, full nodes behave as replicated state machines, transitioning to new states with the arrival of new blocks.

<b>Example:</b>  
Assume that the genesis initializes two user accounts A and B with 10 eth each. Consider a new transaction where user A transfers 5 eth to user B. Full nodes propagate this new transaction to every other full node in the network. Full nodes attempt to solve the cryptographic puzzle. Once a full node solves the puzzle, it creates a new block B1 with a few new transactions. Assuming B1 has only one transaction, where user A sends 5eth to user B, full node verifies the transaction: checks if user A has 5eth and if user B has a valid account then, executes the transaction and updates the current state of Ethereum: updates user A's account to have 5eth and user B's account to have 15eth. The block B1 contains a list of transactions, a hash of its parent block (genesis) and the updated state root hash. The block B1 is then broadcasted to the other full nodes. Full nodes verify the proof-of-work of B1, verify and execute the transactions and update their local state and append B1 to genesis. Thus, after a majority of nodes build B1 on top of genesis, the blockchain now has two blocks - genesis and B1.

<b>Consensus:</b>  
Full nodes concurrently mining new blocks can end up building on top of same parent block resulting in forks. With forks, different full nodes can verify transactions against different histories, creating divergent views of the state at those full nodes. Thus [Nakamoto Consensus](https://bitcoin.org/bitcoin.pdf) was introduced where all the full nodes agree that the longest chain is the accepted history of the transactions. Once the longest chain is established and a significant number of blocks are mined on top of a block in the longest chain, the transactions in that block are committed. Also, the difficulty of the cryptographic puzzles is dynamically changed to maintain a uniform block creation rate. Since it takes significant amount of time and computational resources to create new blocks, the longest chain cannot be changed and the history cannot be erased, as long as a majority of the full nodes remain uncompromised.

Thus, the history of transactions are maintained by every participant as an immutable chain of blocks, each block containing a list of transactions. So every peer in the network can verify a new transaction, without having to rely on a trusted third party. All the peers in the network agree on the history of transactions and agree on the same history as long as a majority of the peers in the network are not compromised. To conclude, with these key ideas, Blockchains are used to design cryptographically secure, trustless, peer to peer payment system.

# Scalability

<i> Now that we understand the design and architecture of blockchain based systems, let's understand the limitations on the scalability of Blockchains. <i>

Blockchains suffer from poor performance and low scalability. While centralized payment systems process tens of thousands of transactions per second, blockchain based decentralized payment systems hardly support tens of transactions per second. Notice that every full node in the network has to verify the new blocks, transactions, re-execute the transactions, update their local states and extend the blockchain. Even with more full nodes in the network, since the work is not shared between them, the system doesn't scale. Notice that, every full node participates in the consensus, making consensus a bottleneck for scaling blockchain based decentralized systems.

With the increasing adoption of Blockchains for various applications and the increasing demand for cryptocurrencies, the scalability of Blockchains has taken the center stage of Blockchain research. There are numerous research papers along different parallels, aiming at scaling blockchains.

# Repo Contents

The efforts towards scaling blockchains are placed along three axes. Papers proposing efficient and scalable alternatives to Nakamoto consensus are summarized under the Scalable Consensus sub directory. Papers proposing new architectures for Blockchains are summarized in the Blockchain Architecture sub directory. Finally, papers proposing efficient storage and network alternatives to increase the throughput of the Blockchains are summarized under the third category.

1. Scalable Consensus  
2. Blockchain Architecture  
3. Others  

# Quick Summary

<i> Let's take a quick look at the major contributors towards scaling Blockchains. </i>

| Paper         | Throughput   | Txn Confirmation Latency |
| ------------- | ------------ | -----------------|
| [Algorand](https://dl.acm.org/citation.cfm?id=3132757)              | ~ 125×       |  ~ 1 minute      |
| [Conflux](https://arxiv.org/abs/1805.03870)                         | ~ 6000       | 4.5 - 7.5 minutes|
| [HoneyBadger](https://dl.acm.org/citation.cfm?id=2978399)           | ~ 1500       |  < 6 minutes     |
| [HyperLedger](https://dl.acm.org/citation.cfm?id=3190538)           | ~ 2500       | 0.3 - 0.5 seconds|
| [OmniLedger](https://ieeexplore.ieee.org/abstract/document/8418625) | ~ 1000       | ~ 1 minute       |

**Note:** The results are from the papers for a configuration with around 100 nodes. However, the numbers are presented for a direct comparison between the systems, but for an average throughput and latency estimates of the current state-of-the art systems.

## Other Details ##

There is a lot of interesting ongoing work on analyzing different attacks and the security of Blockchains, proposing applications of Blockchains in various fields and so on... However, these are not a part of the current study.


## Contact Info ##

Please let me know if there are any recent papers I missed, that could be a good addition to the list. Reach out to me at soujanya.ponnapalli@utexas.edu for suggestions or any queries.
