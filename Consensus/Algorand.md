# [Algorand](https://dl.acm.org/citation.cfm?id=3132757):  Scaling Byzantine Agreements for Cryptocurrencies. 

Algorand introduces a scalable cryptocurrency with transaction confirmation latencies on the order of a minute, using a new 
Byzantine Agreement Protocol for consensus. To achieve scalable consensus, Byzantine Agreement is performed amongst only
a few participants, selected using a novel mechanism based Verifiable Random Functions.

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


## Related Work

Algorand is compared with the other cyrptocurrencies which use *Byzantine Fault Tolerance Protocols (BFT)* for consensus.
BFT protocols have limited scalability, require determining a fixed set of servers upfront, opening up the protocols to 
[sybil attacks](https://coincentral.com/sybil-attack-blockchain/).

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

## Highlights
