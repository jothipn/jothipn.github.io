# Merkle Trees

## Introduction
Let us assume that we are building a distributed data store. We store data in data blocks. There will be tens of thousands of data blocks, if not more. For availability reasons, we need to replicate the blocks across several nodes. While we try to keep the nodes in sync, it is possible that, at times, the nodes go out of sync. How do we quickly identify that such inconsistencies between replicas? Alternatively, consider a peer to peer network. A huge dataset is split into smaller chunks and nodes exchange chunks across themselves and reconstruct the dataset from the chunks that it receives from its peers. How do we ensure that the integrity of the original dataset? How do we ensure that some malicious node has not slipped in a modified chunk?

The answer to both these questions and many more are [Merkle Trees.][def] Merkle trees are hash trees that allows secure verification of contents of large data structures. Merkle trees are widely used across several applications, including hash based cryptography, Bitcoin/Ethereum and more. The well known [Cassandra](https://cassandra.apache.org/) uses Merkle trees [to identify data inconsitencies between replicas][def1].

## Implementation
At its heart, a Merkle tree is a tree of hashes. Leaf nodes contain hashes of data blocks. Parent nodes and root nodes store hashes of the children. To compare if the contents of two replicas are in sync, we start comparing the root hashes. If they are identical, the replicas are in sync. If not, we compare the children of root to see where they differ. We keep going down the tree to the actual leaf node to identify the out-of-sync replicas. So, identification of a given data block typically involves log(n) verficiations of hashes, starting from the root. The [Wikipedia article][def] explains this further.

The implementation of Merkle Trees are fairly straight forward. Here is a [Java implementaion](https://github.com/richpl/merkletree). As outlined above, the "Leaf" class has an array of data blocks. The "MerkleTree" class has the digest, either of the data nodes or of the children. 

As mentioned [here](def1), Cassandra's Merkle tree implemntation uses a perfect binary tree. Here, all leaves are at the same depth. So, to support 2<sup>127</sup> tokens, a Merkle treee of depth 127 is required. This is expensive to build as well as the memory costs are high. Cassandra does an optimization where it restricts the depth to 15. It splits the keys into 32768 (2<sup>15</sup>) ranges and does an effecient comparison.

## Summary
Whenever you next face a solution where you want to securely verify the integrity of distributed data, you should definitely consider Merkle trees as one of the options.


[def1]: http://distributeddatastore.blogspot.com/2013/07/cassandra-using-merkle-trees-to-detect.html
[def]: https://en.wikipedia.org/wiki/Merkle_tree