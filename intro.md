# Spacemesh Introduction

This document and the documents it links to collectively present an overview of the Spacemesh protocol and its constituent components. It is intended for a technical audience, with an eye towards making the protocol and client architecture accessible to those who wish to read, use, and contribute to the development of the Spacemesh protocol via its various client implementations. (See [Learn More](#learn-more), below, for links to many other resources on Spacemesh, both technical and non-technical.)


## What is Spacemesh?

Spacemesh is a fair, race-free, permissionless blockchain protocol currently implemented in the open source [go-spacemesh codebase](https://github.com/spacemeshos/go-spacemesh). The protocol implements a decentralized ledger (a DAG structure, known as the _mesh_) with a native cryptocurrency (Smesh) that anyone is free to transact. What’s more, the protocol is designed in such a way that anyone with free hard drive space and a stable Internet connection can mine from home (by running the [Spacemesh App](https://github.com/spacemeshos/smapp)) and earn guaranteed rewards in the form of Smesh coin, a process known as Smeshing in [Spacemesh terminology](https://github.com/spacemeshos/testnet-guide/blob/master/dict.md).

Spacemesh research and development is led by a team of full-time contributors employed by a for-profit, venture-backed company. However, Spacemesh is also an active open source project and a community of dedicated researchers, developers, and other contributors around the world. Learn more about the Spacemesh protocol at the [project homepage](https://spacemesh.io/), and about the project and its goals in the [manifesto](https://spacemesh.io/spacemesh-manifesto/).


### Why race-free?

The Spacemesh protocol guarantees that each Smesher that follows the rules of the protocol is eligible to produce multiple blocks during each epoch. Note also that Smeshing is entirely permissionless: in other words, _anyone who runs the Spacemesh software and follows the rules of the protocol is guaranteed to be rewarded with Smesh coins at each epoch._ This is in contrast to both Proof of Work-based protocols, such as Bitcoin, where rewards are probabilistic and only professional miners and mining pools stand a realistic chance of earning them; as well as to Proof of Stake-based protocols, where one must hold a substantial amount of the token before they are eligible to participate and earn rewards.

Spacemesh is able to achieve this nice property due to certain unique characteristics of its [consensus mechanism](https://spacemesh.io/protocol/): in particular, the fact that it’s race-free, i.e., it does not require Smeshers to compete to produce blocks in a given layer, but rather allows many blocks to be produced at each layer. This results in a DAG data structure rather than a chain. See [The race to race-free (or why we chose mesh over chain)](https://spacemesh.io/race-freeness/) for more information on this design choice.


## Core Infrastructure

Spacemesh infrastructure consists of several distinct core components. These components are built and maintained separately, but work in concert to enable the broader Spacemesh network and ecosystem.


### Go-spacemesh

The largest and most complex component of the network infrastructure is the go-spacemesh full node implementation. This codebase implements the bulk of the Spacemesh core protocol and can be used to run a protocol-compatible full node on a variety of different systems and architectures, including a trustless _full node_ that independently verifies each transaction, and a mining (Smeshing, in [Spacemesh terminology](https://github.com/spacemeshos/testnet-guide/blob/master/dict.md)) node that additionally participates in consensus, produces blocks and collects rewards.

As the name implies, go-spacemesh is written in the Go programming language, and consists of a number of different modules that implement core components of the protocol such as mining, consensus, networking, P2P sync, and state. See [go-spacemesh](https://github.com/spacemeshos/go-spacemesh) for more, in particular [Go-spacemesh Architecture](https://github.com/spacemeshos/go-spacemesh#go-spacemesh-architecture).

Note that, unlike full node implementations for certain other blockchain protocols, go-spacemesh does not include wallet functionality.


### PoET server

PoET stands for “Proof of Elapsed Time.” It’s a core part of the Spacemesh consensus protocol, where it’s used to prove that a miner has allocated a chunk of hard drive space for some period of time. PoET is implemented as a web service, separate from go-spacemesh, that anyone can run and offer as a service to Smeshers. Many Smeshers can share a single PoET server, and one Smesher may opt to utilize multiple PoET servers for redundancy. Go-spacemesh [includes code](https://github.com/spacemeshos/go-spacemesh/blob/develop/activation/poet.go) to communicate with a PoET server.

For more information see PoET [LINK] and the [PoET codebase](https://github.com/spacemeshos/poet).


### Spacemesh App

The Spacemesh App is a GUI-based desktop application built for Windows, Mac, and OS X that has two main functions: it acts as a wallet, allowing a user to hold and transact [Smesh tokens](https://github.com/spacemeshos/testnet-guide/blob/master/dict.md#smesh-smh), and it allows a user to mine (known as [Smeshing](https://github.com/spacemeshos/testnet-guide/blob/master/dict.md#smesher)) by allocating a certain amount of hard drive space, constructing and submitting eligibility proofs, producing blocks, and collecting rewards.

The App runs an instance of the go-spacemesh full node under the hood. For more information see the [Spacemesh App codebase](https://github.com/spacemeshos/smapp).


### Testing

Testing is a critical part of the software development workflow across all of the independent components of the Spacemesh network. There is no single, universal test suite; rather, each component implements its own unit and integration tests. For instance, you can find the go-spacemesh tests inside the [`tests/` folder](https://github.com/spacemeshos/go-spacemesh/tree/develop/tests) of the repository.


### DevOps

The only piece of infrastructure that’s strictly required to join the Spacemesh network is a copy of the go-spacemesh client (along with the Spacemesh App if Smeshing). However, running multiple clients (such as setting up a local network, or a dev testnet), and/or ensuring redundancy among these nodes, requires additional tooling. There is tooling to run a [local testnet](https://github.com/spacemeshos/local-testnet), to [operate multiple nodes](https://github.com/spacemeshos/smops), and to [collect events](https://github.com/spacemeshos/collector) and store them in a database.


### SVM

SVM is the Spacemesh Virtual Machine, a WebAssembly-compatible smart contract engine. This project is experimental and aims to add smart contract functionality to a [future protocol release](https://github.com/spacemeshos/product/blob/master/product_plan.md). Note that, since it is not part of the testnet, SVM _is not covered further_ in the rest of these documents at present. See [the repository](https://github.com/spacemeshos/svm) and [this blog post](https://spacemesh.io/svm/) for more information about SVM.


## Consensus

Consensus in the Spacemesh protocol consists of several independent components that work together to allow each network node to independently arrive at the same canonical view of blocks and transactions (given the same or similar information). What follows is a very brief overview; see Consensus [LINK] for more information.


### Proof of Spacetime

In Spacemesh, a Proof of Spacetime (abbreviated PoST) is how a miner establishes eligibility to produce blocks and participate in consensus. These proofs have two phases: an initial phase, where a miner allocates a chunk of hard drive space to the protocol, and commits to the contents of that space; and an ongoing phase, where the miner must submit a new proof of ongoing eligibility each epoch. See Mining [LINK] for more information.


### Producing blocks

Once a miner has successfully generated and submitted an eligibility proof, the process of actually producing blocks is relatively straightforward: they collect transactions into a local transaction pool, and once per layer, assemble them into blocks and broadcast those blocks to the network. See Mining [LINK] for more information on this process.


### Tortoise and Hare

Once many miners have produced candidate blocks for a given layer, how does the network achieve consensus on the canonical set of blocks and transactions that form the layer? In other words, how does a transaction become finalized?

Spacemesh employs a two-step process to achieve finality. The first step is the Hare protocol, which each node runs at the end of every layer. It’s a Byzantine agreement protocol that allows the network to quickly achieve consensus on a canonical set of blocks. The output of the Hare allows bootstrapping of the consensus of the Tortoise, which is a slower, vote-based protocol that methodically counts the votes for and against each block, leading to eventual, final consistency. Each node uses the output of the Hare protocol to decide which previous blocks its newly-produced blocks should vote for. See Consensus [LINK] for more information on these protocols.


## Networking

The Spacemesh network consists of a peer-to-peer network of independent nodes, each running go-spacemesh (or another Spacemesh protocol client). Messages, which may consist of new transactions, old or new blocks, votes on consensus, etc., transit the network in one of only two fashions: a message may be transmitted from one node directly to a recipient node (e.g., in response to a request for a particular piece of data, such as a block), or a message may be gossiped across the entire network (e.g., a new transaction or block).

For more information, see Network [LINK].


## Data Structures

The basic building blocks of the Spacemesh protocol, and the overall, canonical mesh data structure, are transactions and blocks. Transactions are account-based, and contain a sender, a recipient, an amount, a signature, and a few other pieces of data. Blocks collate zero or more transactions into a discrete set, and contain a block height (layer number), a signature of the miner, a proof of eligibility, and a few other pieces of data. See Mining [LINK] for more information.

Other relevant data structures include the various types of proof (PoST, PoET, ATX, NiPoST, etc.) [LINK to Mining], and vote messages exchanged as part of the Hare protocol [LINK to Consensus].


## Learn More

The documents contained in this repository are intended to provide a high-level, developer-centric overview of the Spacemesh protocol and the various infrastructure that implements the protocol. For additional resources, see the following:



* [Protocol whitepaper](https://spacemesh.io/spacemesh-protocol-v1-0/), which contains a complete description of the entire protocol with accompanying proofs of correctness
* [Technical whitepaper](https://spacemesh.io/whitepaper1/), which precedes the protocol whitepaper, and is less rigorous
* [Protocol specifications repository](https://github.com/spacemeshos/protocol)
* [Spacemesh protocol overview](https://spacemesh.io/overview/), an even more high-level overview of the protocol intended for a general audience
* [The Spacemesh Consensus Protocol](https://spacemesh.io/protocol/), a three-part blog post intended for a technical audience that details many of the design decisions behind the protocol
* [Proof of Space-Time (PoST) with a mesh-based permissionless consensus protocol](https://youtu.be/jvtHFOlA1GI), a talk by Tal Moran, Spacemesh Chief Scientist, at CESC 2018, introducing the protocol


## Contribute

All of the core components of the Spacemesh network and protocol, including everything described above, is open source and permissively-licensed, and we welcome contributions from all community members. We have a set of [open bounties](https://github.com/spacemeshos/go-spacemesh/labels/funded%20%3Amoneybag%3A), and we are in the process of launching a grants program where you can apply for funding to work on a project of your choice that you feel will add value to the Spacemesh network and ecosystem. Watch [the Spacemesh homepage](https://spacemesh.io/) for an announcement.

In addition to reading these docs, familiarizing yourself with the code, and [running your own node](https://github.com/spacemeshos/go-spacemesh#getting), the best way to get started is to work on a [good-first-issue in a codebase such as go-spacemesh](https://github.com/spacemeshos/go-spacemesh/labels/good%20first%20issue). You can chat with other Spacemesh core developers and app developers [on Gitter](https://gitter.im/spacemesh-os/), or join a regular core devs call (participation information will be shared soon).