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

This approach is not concensus critical, which is huge plus. This approach is also incremental in nature which means it does not rquire to recompute the whole UTXO set merkle hash everytime. 

<h4>TXO MMR commitments: </h4>

Directly from Peter Todd's reference:

A merkle tree committing to the state of all transaction outputs, both spent and unspent, can provide a method of compactly proving the current state of an output. This lets us “archive” less frequently accessed parts of the UTXO set, allowing full nodes to discard the associated data, still providing a mechanism to spend those archived outputs by proving to those nodes that the outputs are in fact unspent.

This approach uses uses a Merkle Mountain Range1 (MMR), a type of deterministic, indexable, insertion ordered merkle tree, which allows new items to be cheaply appended to the tree. There is no need for deletion opperation as compared to normal Binary Tree(Red Black Trees, AVL trees). Instead of removing the output we just update it's status to spent. The compact proof of this structure will be mountain tips. 

bandwidth overhead per txin is substantial, so a more realistic implementation is be to have a UTXO cache for recent transactions, with TXO commitments acting as a alternate for the (rare) event that an old txout needs to be spent.

Pros: 
Proofs can be generated and added to transactions by anyone having the full MMR. And because of the one-wayness commitment, it is impossible to forge such a wrong proof. 

Cons:
This would require complete layer of P2P protocol for communnicating different parts of TXO MMR. There needs to new mechanism for attaching path proofs to Mountain Tips for lite wallets. 

Naive appraoach:
A simple approach would involve creating a serialized merkle root of UTXO commitments. It might be worth answering is why not the simple way of using RBL/AVL trees for implementing balanced binary tree type structure? It supports all the operations we desire. Not that 


New security Model:
UTXO commitments give rise to a new security model. I can easily set a full node with a --assumed-txo set and I am good to interact with the bitcoin P2P network. This is similar but worse than --assumed-valid which bitcoin core introduced in 0.14. --assumed-valid required the miners and as well the developers together to cheat the user, where here the user would simply be trusting the software. 

