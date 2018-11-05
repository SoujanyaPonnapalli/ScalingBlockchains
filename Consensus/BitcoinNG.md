# [Bitcoin-NG](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eyal.pdf): A Scalable Blockchain Protocol

Bitcoin-NG is a scalable blockchain, which maintains the same trust assumptions and achieves significantly 
higher throughput and lower latency than Bitcoin.
Bitcoin-NG divides the time into epochs, where each epoch has a single leader, who is responsible for serializing transactions 
unilaterally until a new leader is chosen, achieving high throughput.

## Problems Identified

Blockchain protocols face a significant scalability barrier. They exhibit low throughput (1 to 3.5 transactions per second),
high latencies for transactions to be added into the blockchain (10 minutes) and even higher transaction confirmation 
latencies (several hours) (considering proof-of-work). The transaction throughput is capped by the choice of:
- **Block size:** Increasing block size improves throughput, but the results in bigger blocks taking longer to propagate
- **Block interval:** Reducing the block interval reduces latency, but leads to instability where the system is in disagreement and 
the blockchain is subject to reorganization.

The paper identifies that leader election takes place in Bitcoin implicitly. In Bitcoin, the leader is in charge of serializing 
history, making the entire duration of time between leader elections a long system freeze.

Bitcoin-NG divides time into epochs, where each epoch has a single leader, who is responsible for serializing transactions 
unilaterally until a new leader is chosen, marking the end of the former’s epoch. So, leader election in Bitcoin-NG is forward 
looking unlike in Bitcoin.

## Solution

This paper introduces Bitcoin-NG and also proposes a set of metrics to evaluate Nakamoto consensus protocols.

**Bitcoin-NG:**
- Scalable blockchain protocol that serializes transactions like Bitcoin, but allows for better latency, throughput and bandwidth 
without sacrificing other properties
- The protocol divides time into epochs. In each epoch, a single leader generates blocks and serializes operations
- The protocol introduces two types of blocks: *key blocks* and *microblocks*
- **KeyBlocks** for leader election
  - Key block contains the reference to the previous block (either a key block or a microblock, usually the latter)
  - The blocks are computed using proof-of-work and build on top of the heaviest subtree
  - Once a node generates a keyblock it becomes the leader until it hears from the next leader
- **MicroBlocks:**
  - A leader is allowed to generate microblocks at a defined rate <= predefined deterministic maximum rate
  - The maximum rate can be much higher than the average interval between key blocks resulting in an increased throughput
  - This bound is to prevent malicious leaders from swamping the network with microblocks. So, blocks generated at a higher rate than the maximum allowed rate are considered invalid.
  - As microblocks donot contain the proof-of-work, they are light weight and can be generated quickly.
- Bitcoin-NG, allows keyblocks at a specific rate, allowing leaders to be elected at regular intervals, who add microblocks
- As the new leader might not have received all the microblocks from the previous leader, there could be short microblock forks
- Such forks are seen by every user and the forked blocks are pruned. So, a user that sees a microblock should wait for the propagation time of the network before considering it in the chain, to make sure it is not pruned by a new key block.

**Challenges**
- Leaders are compensated for their effort. Each ledger entry has a fee, which is shared by the leader that places the entry
in a microblock, and the subsequent leader that generates the next key block. 
- Current leader earns 40% of the fee, and the subsequent leader earns 60% of the fee to ensure that the subsequent leader doesn't 
prune all the blocks computed by the previous leader
- A malicious leader could split the system, publishing different information to different users. To demotivate such behaviors, dedicated ledger entries called poison transactions are introduced
  - They invalidate the revenue of the malicious leader by including a proof-of-fraud in the poisoned block
  - The poison transaction has to be placed on the blockchain within the maturity window of the malicious leader’s key block, and 
  before the revenue is spent by the malicious leader. 

**Other Details:**
  - Microblocks carry no weight to prevent selfish mining, where mined blocks are not published
  - A malicious leader can perform a DoS attack by placing no transactions in microblocks, similar to mining empty blocks on Bitcoin
  - **KeyBlock forks:** If multiple key blocks are generated after the same prefix of key blocks, the knowledge of the fork is 
  propagated through the network, and all transactions that appear only on one branch are uncertain until one branch gains a lead.

**Evaluation Metrics:**

They propose the following metrics to evaluate Nakamoto consensus protocols:
- **Consensus Delay:** The time taken for the system to reach an agreement
- **Fairness:** Two fairness ratios, which ideally should be 1.0 are to be evaluated
  - The ratio of transactions not coming from the largest miner with respect to all transactions
  - The ratio of mining power not owned by the largest miner with respect to all mining power.
- **Mining Power Utilization:** The ratio between the mining power that secures the system and the total mining power. Min- ing power wasted on work that does not appear on the blockchain accounts for the difference.
- **Subjective Time to Prune:** The maximum time between a user seeing a transaction and learning that it is pruned
- **Time to Win:** Difference between the first time a node believes a never-to-be-pruned-transition has occurred and the last time a (different) node disagrees, believing an alternative transition has occurred. If it is zero, there are no disagreements.

## Related Work

Please read the paper for the related work discussion on the following topics:
- GHOST
- Inclusive Blockchains
- Faster Bitcoin
- Off-chain solutions.

## Results from paper

Bitcoin and Bitcoin-NG with 1000-node experiments running in real time on an emulated network. For complete details of the
experimental setup and evaluations please read the paper.

![blockPropagation](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Bitcoin-NG/propagationLatency.png)
![latency](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Bitcoin-NG/latency.png)
![throughput](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/Bitcoin-NG/throughput.png)
