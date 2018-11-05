# [Algorand](https://dl.acm.org/citation.cfm?id=3132757):  Scaling Byzantine Agreements for Cryptocurrencies. 

Algorand introduces a scalable cryptocurrency with transaction confirmation latencies on the order of a minute, using a new 
Byzantine Agreement Protocol for consensus. To achieve scalable consensus, Byzantine Agreement is performed amongst
a few users, selected using a novel mechanism based Verifiable Random Functions.

## Problems Identified

The problems identified and tackled by Algorand are the following
- High transaction confirmation lantencies in cryptocurrencies like Bitcoin on the order of hours
- Bitcoin and other cryptocurrencies using proof-of-work inherit its limitations

Using proof-of-work and Nakamoto Consensus has the following drawbacks. Proof-of-work
- Allows the possibility of forks, where different blockchains have the same length, neither superseding the other
- Mitigates forks by using reasonably low block creation rates (Bitcoin: ~10 minutes) and high transaction confirmation
latencies (Bitcoin: ~1 hour)
- Proposes waiting until several blocks are built on a block (Bitcoin: around 6 blocks) to confirm transactions in it
- Trades off longer times for computing a new block with a possibility of wasted work for correctness and high 
confidence that a transaction has been confirmed
- Makes security a challenge: an adversary that isolates a user’s network can convince it to use a particular fork
 
Algorand decreases the possibility of forks, ensuring that users never have divergent views of confirmed transactions, 
even if some of the users are malicious and the network is temporarily partitioned.

## Proposed Solution

Algorand relys on Byzantine agreement for consensus, to eliminate the possibility of forks and to reduce the transaction 
confirmation latencies. Algorand uses a scalable Byzantine agreement protocol **BA⋆**, that uses verifiable random functions
(VRFs) to randomly select users that particpate in consensus, in a private, non-interactive way.

**Assumptions**

Algorand makes the following assumptions:
- The fraction of money held by honest users is greater than 2/3. 
- An adversary cannot corrupt a large number of users that hold a significant fraction of the money.
- *(To make progress)* >95% of the users can send messages that will be received >95% of the users within a defined time bound: strong synchrony (regardless of the strong synchrony assumptions)
and network partitions are not possible.
- *(To ensure that all the users agree on the same set of transactions)* Network can be asynchronous: entirely controlled by 
the adversary, for a bounded period of time, at most 1 day or 1 week, but is strongly synchronous for at least a few hours or 
a day after the asynchrony period.
- *(To make progress)* Assumes loosely synchronized clocks across all users, to recover from an asynchrony period

**Solution Overview**

- Algorand grows the blockchain in asynchronous rounds. In a round, users are selected at random (using cryptographic sortitions), to propose blocks and are assigned priorities.
- Each selected user computes a block of pending transactions and propagates the block along with a proof of their priority
- Algorand uses **BA⋆**, to reach consensus on one block from these proposed, pending blocks.
- Every user invokes **BA⋆** with the highest priority block they received
- **BA⋆** executes in repeated steps. 
  - Each step begins with users executing cryptographic sortitions and checking if they have been selected as 
  committee members for that step. 
  - Committee members then broadcast a message which includes their proof of selection.
  -  Execution of **BA⋆** happens in two phases.
      - **BA⋆** reduces the problem of agreeing on a block to agreement on one of two options. 
      - **BA⋆** reaches agreement on one option, either agreeing on a proposed block or on an empty block.
  - These steps repeat until, in some step of **BA⋆**, enough users in the committee reach consensus.
- **BA⋆** can produce two kinds of consensus:
  - *Final consensus:* Any other user that reaches final or tentative consensus in the same round must agree on the same block
  - *Tentaive consensus:* Other users may have reached tentative consensus on a different block
  (as long as no user reached final consensus).
- Tentative consensus is possible in only in two scenarios:
  - If a small portion of the network is controlled by the adversary, it may with small probability cause **BA⋆** to 
  reach tentative consensus on a block. However, in a few rounds Algorand will reach final consensus on a successor block, 
  and thus confirm the earlier blocks and transactions.
  - If the entire network is controlled by the adversary, multiple forks can be formed, splitting the users into different 
  groups preventing **BA⋆** to reach consensus in the further rounds. To recover liveness, Algorand periodically invokes BA⋆ 
  to reach consensus on which fork should be used going forward. As the adversary cannot control the entire network 
  indefinitely, Algorand eventually reaching consensus on one fork and hence makes progress.

*For further details please read the paper*

**Challenges**

Algorand faces three challenges. 
- Algorand must avoid [sybil attacks](https://coincentral.com/sybil-attack-blockchain/), where an adversary creates many pseudonyms to influence the Byzantine agreement protocol. 
- Second, BA⋆ must scale far higher than the scale at which the state-of-the-art Byzantine agreement protocols operate. 
- Finally, Algorand must be resilient to DOS attacks, and should operate even if an adversary disconnects some of the users

Algorand tackles these challenges using different techniques. It proposes **weighted users** to curb sybil attacks, 
consensus by **selected committee** for achieving high scalability, **cryptographic sortitions** for avoiding adversaries 
to target committee members and **pariticpant replacement** to avoid adversaries targetting committee members 
once they send their messages.

## Related Work

Algorand is compared with the other cyrptocurrencies which use *Byzantine Fault Tolerance Protocols (BFT)* for consensus.
BFT protocols have limited scalability, require determining a fixed set of servers upfront, opening up the protocols to 
sybil attacks.

- **HoneyBadger**: Honey Badger uses BFT by designating a set of servers to be in charge of consensus on a set of approved 
transactions. The downsides of the design are:
  - We loose decentralization as there are a fixed set of servers chosen when the system is first configured.
  - Targeted attacks, that either compromise the servers or disconnect them from the network, are possible.

- **Bitcoin-NG:** Bitcoin-NG uses Nakamoto consensus to elect a leader, and then have that leader publish blocks,
resulting in an order of magnitude of improvement in latency of confirming transactions over Bitcoin. 
  - [Hybrid consensus] uses Nakamoto consensus to periodically select a group of participants for consensus
  - Hyrbid consensus opens up the possibility of forks, due to the use of Nakamoto consensus for paticipant selection.

- **Stellar:** Stellar uses Byzantine consensus, where each user can trust quorums of other users, forming a trust hierarchy. 
  - Consistency is ensured as long as all transactions share at least one transitively trusted quorum of users, 
  and sufficiently many of these users are honest.
  - Users have to make complex trust decisions when configuring their client software.

- **Proof-of-stake:** In many proof-of-stake cryptocurrencies, a malicious leader (who assembles a new block) 
can create a fork in the network, but if caught (e.g., since two versions of the new block are signed with his key), 
the leader loses his money. 
  - Proof-of-stake avoids the overhead of proof-of-work and therefore allows reducing transaction conirmation time. 
  - Some proof-of-stake cryptocurrencies require a master key to periodically sign the correct branch to mitigate forks, raising
  significant trust concerns.

## Evaluation

- Algorand is evaluated on 1000 Amazon EC2 virtual machines, distributed across 20 major cities around the world. 
- Bandwidth of each user is capped at 20Mbps and the money is equally distributed amongst the users.
- Algorand achieves around 125x the throughput of Bitcoin, which is around 875 transactions per second.

## Results from the paper

The following images show the time it takes for Algorand to complete an entire round, and include the minimum, median, maximum, 25th, and 75th percentile times across all users.
![Latency with 5,000 to 50,000 users](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Algorand/Latency5K-50K.png)
![Latency with 50,000 to 500,000 users](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Algorand/Latency50K-500K.png)
![Latency with adversaries](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Algorand/LatencyAdversary.png)
![Latency with block size](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Algorand/LatencyBlockSize.png)

