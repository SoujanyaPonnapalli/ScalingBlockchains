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

![order-execute](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/order-execute.png)

## Proposed Solution

Fabric introduces the execute-order-validate blockchain architecture. To implement this architecture, Fabric contains:
- *Ordering service*: Atomically broadcasts state updates to peers and establishes consensus on the order of transactions. 
- *Membership service provider*: Associates peers with identities, maintaining the permissioned nature of Fabric.
- *Peer-to-peer gossip service*: Disseminates the blocks output by ordering service to all peers.
- *Smart contracts*: Written in standard programming languages, do not have access to the ledger state and are run in a 
container environment.
- *Blockchain*: Each peer locally maintains the ledger in the form of the append-only blockchain and as a snapshot of the most 
recent state in a key-value store.

![execute-order-validate](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/execute-order-validate.png)

![Fabric](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/Fabric.png)

## Architecture Overview: 

Fabric proposes a three phase execute-order-validate architecture.
All nodes that participate in the network have an identity, as provided by a modular membership service provider (MSP).
Nodes in a Fabric network take up one of three roles:
- **Clients**: Submit transaction proposals for execution and then finally broadcast them for ordering
- **Peers**: Every peer maintains the entire state of the system. However, there are *endorsing peers (endorsers)*, which 
execute the transaction proposals. Every peer validates the transactions and update their state.
- **OSN**(Ordering Service Nodes, Orderers): Nodes responsible for establishing the total order of all the transactions.

The high level transaction flow in fabric, in the three phase execute-order-validate architecture is as follows:

**Execution**: 
- Clients sign and send the transaction proposal to one or more endorsers for execution.
- Transaction proposal contains operations to execute, parameters, chaincode identifier and other metadata
- Endorders execute the transaction on the specific chaincode, generate a read-set and a write-set, 
do not persist any updates and send back a proposal response to the clients.

**Ordering**:
- When the client collects enough endorsements on a proposal (as of now, only if all endorsements generate the same response), 
it assembles a transaction and subkits it to the ordering engine.
- Orderers establish a total order amongst all the submitted transactions
- The ordering service, batches multiple transactions into blocks and outputs a hash-chained sequence of blocks

**Validation**:
- Once the total ordering on the blocks of transactions are established, either the ordering service directly or via gossip, 
delivers the blocks to the peers.
- The validation of transactions happens in parallel. If endorsement is not satisfied, transaction is marked invalid.
- The read-write conflicts checks are performed for all transactions
- Finally the ledger is updated with the block and the local state is updated with the write-set of the transactions.

![Flow](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/Flow.png)

For more details on the trust and fault model and individual components of Fabric, please refer the [paper](https://dl.acm.org/citation.cfm?id=3190538). 

## Related Work

Tendermint, Quorum and Chain are all permissioned blockchain variants with the order-execute architecture. Ethereum on the other hand is the permissionless variant with order-execute architecture. Most of the current state-of-the-art Bitcoin, Ethereum, ZeroCash have the order-execute architecture, inheriting its limitations.

## Evaluation

They ran experiments varying block size from 0.5MB to 4MB and the results are as shown. 
For the following experiments, they adopt 2MB to be the block size. 

![blockSizeTpsLatency](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/TpsLatencyWithBlockSize.png)

The results, with 2MB blocks, are shown, for blocks containing MINT (Fig. 7a) and SPEND (Fig. 7b) operations. 
For both operations the measured throughput and latency scale in the same way with the number of vCPUs. 
They performed coarse-grained profiling of latency during our previous experiment at the peak reported throughput.

![vCPU](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/CpuThroughput.png)

![Latencies](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/Latencies.png)

For the follow-up experiment, they moved the ordering service, 10 endorsers, and the clients to the nearby 
Tokyo (TK) data center, leaving the non-endorsing peers in the HK DC. The goal in this (and the next) experiment was to
evaluate the system when the network bandwidth becomes the bottleneck. We varied the number of non-endorsing peers in HK 
from 20 to 80, maintaining direct connectivity with the ordering service (i.e., one peer per org), in addition to 10 endorsing 
peers in TK. The single-TCP netperf throughput reported between two VMs in TK and HK is 240 Mbps on average. The peak 
throughput in function of the (total) number of peers is depicted here:

![ScaleWithPeers](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/ScaleWithPeers.png)

They extend the last experiment to 5 different data centers: Tokyo (TK), Hong Kong (HK), Melbourne (ML), Sydney (SD), and 
Oslo (OS), with 20 peers in each data center, totaling 100 peers. As in the previous experiment, the ordering service, 
10 endorsers, and all clients are in TK. They run this experiment without gossip (one peer per org) and 
with gossip (10 orgs of 10 peers, 2 orgs per data center, fanout 7). The results are summarized below:

![100peers](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/HyperLedger/100Peers.png)
