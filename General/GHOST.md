# ([GHOST](https://eprint.iacr.org/2013/881.pdf)) Secure High-Rate Transaction Processing in Bitcoin

This paper investigates the implications of having a higher transaction throughput on Bitcoin’s security against double 
spending attacks. It shows that at high throughput, substantially weaker attackers are able to reverse payments that were 
considered accepted by recipients. They propose GHOST, a modification to the way Bitcoin nodes construct and reorganize 
the block chain. GHOST has been adopted and a variant of it has been implemented in Ethereum.
