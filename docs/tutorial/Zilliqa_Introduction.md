# What is Zilliqa?

ZILLIQA is a high-throughput public blockchain platform designed to scale up to thousands of transactions per second. It leverages a network-level sharding mechanism
to divide the nodes in the network to process transactions in parallel, and a transaction
sharding protocol to ensure transactions are accepted as if there is no sharding.

# Key features

## Network Sharding

Zilliqa dynamically splits the network of blockchain nodes into different subgroups, called
shards, with each shard formed to process and reach consensus on a subset of
transactions. This way, disjoint subsets of transactions can be processed in parallel, and
significantly boost the transaction throughput by orders of magnitude. Eventually, such
transactions are merged into a new block that is committed to the blockchain.

Dividing the mining network into smaller shards is a two-step process. First, a dedicated set of nodes called the directory service committee (or DS committee) are elected which then shard the network and assign nodes to their shard. The directory service committee is tasked to coordinate the sharding process, as well as validate the blocks of transactions proposed by each shard and verify if they have received approval from a sufficiently large quorum within the shard.

## Secure & Efficient Consensus Algorithm

Within the directory service committee and each shard that processes and accepts
transactions, Zilliqa runs a secure and efficient consensus protocol. The protocol enables each
shard to reach an agreement on the blocks to propose. The consensus protocol is based on
the idea of byzantine fault tolerance ([BFT](http://pmg.csail.mit.edu/papers/osdi99.pdf)) with heavy optimizations of improving its efficiency by
using the idea of employing EC-Schnorr multisignature in the
PBFT protocol as developed in [CoSi](https://arxiv.org/pdf/1503.08768.pdf).

## Transaction Sharding

For better scalability, Zilliqa makes two conscious choices in the design of transaction sharding:

    1. Zilliqa provides atomic transaction commits without involving cross-shard communication that is often costly and complex.

    2. Zilliqa allows transactions to be processed asynchronously with the consensus processes of the blockchain. Zilliqa adopts a “reject-and-retry” mechanism to asynchronously process transactions as and when the majority of nodes become up-to-date.

## Computational Sharding & Sharding-Friendly Smart Contract Language

With scalability as the main goal of Zilliqa, it proposes a new smart contract language Scilla
that scales much better for a multitude of applications that range from automated auctions,
shared economy to financial modelling.
Beyond verifiability of security and correctness properties, Zilliqa’s smart contract language
will run efficiently on a blockchain architecture with sharding. Moreover, it will enable
sharding of computational resources in the blockchain network via an overlay above the
consensus process, i.e., computational sharding. Computational sharding allows users of
Zilliqa and applications running on Zilliqa to specify the sizes of consensus groups to
compute for each of the subtasks. Each consensus group will then be tasked to compute the
same subtask, and produce the results. The user specifies the condition on acceptance of
the results, e.g., all in the consensus group must produce the same results, or 3/4 of them
must produce the same results, etc.

# Reference
[positionpaper](https://docs.zilliqa.com/positionpaper.pdf)
[whitepaper](https://docs.zilliqa.com/whitepaper.pdf)