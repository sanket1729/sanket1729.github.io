---
layout: post
title:  "A survey on UTXO commitments schemes for bitcoin"
date:   2017-11-19 16:06:11 +0900
permalink: /utxo-commitments
categories: jekyll update
---
The Unspent Transaction Output (UTXO) set is the subset of Bitcoin transaction outputs that have not been spent at a given moment. Whenever a new
transaction is created, UTXOs are used to claim the funds they are holding, and new UTXOs are created. Whenever a transaction in confirmed in the blockchain, the inputs UTXO's are spent and new output UTXOs are created.

<h3>Motivation:</h3>

UTXO growth has been a issue which has recieved lots os traction in the bitcoin community recently. There are lots of unused UTXOs becuase of use of blockchain for timestamping purposes, dust outputs. These outputs are kept by every single node on the network.
The potential use cases could be include fast sync methods with known UTXO sets.
Database consistency check across multiple full nodes.

</br>
<h3>Simple Solution:</h3>
</br>
A simple approach would involve creating a serialized merkle hash of the  might be worth answering is why not the simple way of using RBL/AVL trees for implementing balanced binary tree type structure? It supports all the operations we desire 

<h3>Adding UTXO set to consensus rules:</h3>

Commiting UTXO sets in the same block creates new problems as it invalidates SPV mining. SPV mining(mining empty blocks without verifing the previous block) is also useful because it mitigates certain transaction delays in propogation of entire block. Currently, block witholding attack is made harder by "Header stealing" or sniffing the header form a competing pool. Other pools can start thier work on the new block with just the header and validate the block once they recieve it completely. In other words, the time required for transmition of the blocks and verification of the block can be used for mining. Such a mechanism is not possible if UTXO commitments of the sameblock are required to be commited in the block being currently mined.
A simple solution could be include a UTXO set commitment of a block which is k blocks prior in history.

<h3>Known Proposals:</h3>

<h4>Rolling UTXO hashes:</h4>
Author: Peter Wuille

This proposal is not strictly a commmitment to the blockchain, but a way to maintain these hashes locally in an incremental fashion. The rolling hash of UTXO set is simply an unordered set hash. In simpler words, it does not support the query of the type, "Is UTXO u1 present in the set?", but can only answer question like,
"Is UTXO set S1 same or different from S2?".

A simple rolling implementation might involve adding the commitments of each individual UTXO.
Set Hash = H(a) + H(b) + H(c) + H(d) + .....
Therefore Spending b and d to create b1 and d1 would have operations like:
Rolling Hash Set S2 = S1 - H(b) - H(d) + H(b1) + h(d1)

The suggested operation is multiplication mod prime in ECC field instead of addition which bitcoin already uses. Thus, there is no increase in security assumptions for the system.

Pros: This approach is not concensus critical, 
TXO MMR commitments:

Naive appraoach:

TXO bitfields:

New security Model:

Future Work:
