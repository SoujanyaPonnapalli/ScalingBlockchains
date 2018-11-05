# ([GHOST](https://eprint.iacr.org/2013/881.pdf)) Secure High-Rate Transaction Processing in Bitcoin

- This paper shows that at high throughput rates, substantially weaker attackers are able to reverse payments that were considered accepted by the recipients. 
- It proposes GHOST, a modification to the way Bitcoin nodes construct and reorganize the block chain.

## Problems Identified

- Scalability limitations of Blockchains
- High transaction throughput can be achieved in Blockchains by either
  - Increasing the block size or
  - Increasing the block creation rate

This paper investigates the implications of high transaction throughput on the security of Blockchains against different attacks.
It suggests an alternative to the longest-chain rule called GHOST, that changes the conflict-resolution procedure for the block 
chain, which allows operating at high transaction throughput, scaling Blockchains, without compromising security.

## Analysis on Attacks

A naive attempt at increasing the throughput can be made by simply increasing bloc size or block creation rate will result in 
increased number of forks, reducing the security of the system. This results in the possibility of attackers performing
effective attacks with less computational power once the throughput is increased.

- **Larger Blocks:** As the block size is increased, they naturally take longer to propagate through the network, creating more forks
- **Accelerated Block Creation:** Blocks will often be created by nodes that are not fully up to date and will not extend the longest 
chain. The attacker on the other hand, also creates blocks faster, but does not suffer from a loss of efficiency.
- Increased forks, makes it easier for the attacker to create a longer chain

![ghost](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/GHOST/ghost.png)

## Solution

- **GHOST:** The Greedy Heaviest-Observed Sub-Tree
- According to the GHOST algorithm, every node follows a path from the root of the tree (the genesis block) and chooses at each fork, 
the block leading to the heaviest subtree.
-  GHOST is resilient to 50% attacks, even if the network suffers from extreme delays and the attacker does not.
- Allows higher block creation rates and larger block sizes
- Every block is eventually either adopted or abandoned
- The probability that a block will be off-chain sometime (T) after it was a part of the main chain becomes zero as T->infinity
- The rate of block and transaction confirmations are discussed in the full version of the paper.

## Results from the paper

![tpsSecurity](https://github.com/SoujanyaPonnapalli/ScalingBlockchains/blob/master/Images/GHOST/tpsSecurity.png)