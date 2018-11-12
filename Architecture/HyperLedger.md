## [Hyperledger Fabric](https://dl.acm.org/citation.cfm?id=3190538): A Distributed Operating System for Permissioned Blockchains

Fabric: A blockchain system supporting modular consensus protocols, allowing the system to be tailored to specific use cases and trust models. Proposes a novel execute-order-validate architecture for permissioned blockchain systems, scaling them to support ~3500 tps. Supports applications written in standard programming languages.

## Problems Identified

Existing blockchains have **order-execute** architecture and implement **active replication**: a consensus protocol 
orders the transactions and then propagates them to all peers; and each peer executes the transactions sequentially 
following state machine replication. 

In the state-of-the-art permissionless blockchains with order-execute architectures:
- Consensus is hard-coded
- Trust model cannot be adapted according to the requirements of smart contracts
- Smart contracts have to be written in a fixed domain specific language
- Transactions are sequentially executed leading to performance degradation
- Smart contracts are executed on all peers without any confidentiality
- To prevent non-deterministic code, smart contracts are written in domain specific languages, limiting their adoption

## Proposed Solution

Fabric introduces the execute-order-validate blockchain architecture. To implement this architecture, Fabric contains:
- *Ordering service*: Atomically broadcasts state updates to peers and establishes consensus on the order of transactions. 
- *Membership service provider*: Associates peers with identities, maintaining the permissioned nature of Fabric.
- *Peer-to-peer gossip service*: Disseminates the blocks output by ordering service to all peers.
- *Smart contracts*: Written in standard programming languages, do not have access to the ledger state and are run in a 
container environment.
- *Blockchain*: Each peer locally maintains the ledger in the form of the append-only blockchain and as a snapshot of the most 
recent state in a key-value store.

For more details on the trust and fault model and individual components of Fabric, please refer the [paper](https://dl.acm.org/citation.cfm?id=3190538). 

## Related Work

## Evaluation