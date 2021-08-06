# Launch

Download polkadot binary here https://github.com/paritytech/polkadot/releases/download/v0.9.8/polkadot
and place it in ```./relaychain/bin```

Note: This is temporary, will later be replaced by a polkadot fork

Start the relaychain and the parachain:
```npx polkadot-launch ./polkadot-launch.json```

Monitor relaychain: https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/explorer

Monitor parachain: https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9977#/explorer


# Ecosystem

## Polkadot

### What is Polkadot

Polkadot enables scalability by **allowing specialized blockchains to communicate with each other** in a secure, trust-free environment.

Polkadot is built to **connect and secure unique blockchains**, whether they be public, permission-less networks, private consortium chains, or oracles and other Web3 technologies.

<u>It enables an internet where independent blockchains can exchange information under common security guarantees.</u>

[https://wiki.polkadot.network/docs/getting-started#what-is-polkadot](https://wiki.polkadot.network/docs/getting-started#what-is-polkadot)

### How does Polkadot work

The Polkadot network uses a [sharded model](https://en.wikipedia.org/wiki/Shard_(database_architecture)) where shards - called **Parachains**, allow transactions to be processed in parallel instead of sequentially. Each parachain in the network has a **unique state transition function** (STF).

Polkadot has a Relay Chain acting as the main chain of the system. Based on Polkadot's design, as long as a chain's logic can compile to [WebAssembly](https://wiki.polkadot.network/docs/learn-wasm) and adheres to the Relay Chain API, then it can connect to the Polkadot network as a parachain.

<img src="https://wiki.polkadot.network/assets/images/polkadot_relay_chain-c411a282aa36af0f20d04389919a6275.png" style="zoom: 33%;" />

In order to interact with chains that want to use their own finalization process (e.g. Bitcoin), Polkadot has **bridge parachains** that offer two-way compatibility, meaning that transactions can be made between different parachains.

The [Cross-Chain Messaging Protocol (XCMP)](https://wiki.polkadot.network/docs/learn-crosschain) allows parachains to send messages of any type to each other. The shared security and validation logic of the Relay Chain provide the environment for trust-free message passing that opens up true interoperability.

https://wiki.polkadot.network/docs/getting-started#how-does-polkadot-work

### Parachain

<img src="https://wiki.polkadot.network/assets/images/one_parachain-5fe48b5bfbcf7680a91b0f5a940b39c7.png" style="zoom:33%;" />

A parachain is an application-specific data structure that is globally coherent and validatable by the validators of the Relay Chain.

**Most commonly a parachain will take the form of a blockchain**, but there is no specific need for them to be actual blockchains.

https://wiki.polkadot.network/docs/learn-parachains

### Bridge Parachains

A cornerstone technology of blockchain interoperability is the blockchain bridge. **Blockchain bridges are ways for two economically sovereign and technologically diverse chains to communicate with each other.** Bridge designs come in a variety of flavors ranging from centralized and trusted to more decentralized and trustless. Polkadot favors the latter bridge designs for its ecosystem, however there is nothing that blocks a development team from building and deploying the former.

Parachains, blockchains in the Polkadot network, are **secured by Polkadot’s relay chain and thus are not able to select their own consensus mechanism**. If a blockchain wants to use its own consensus mechanism, it can **use a parachain as a “bridge” to connect to Polkadot’s network**.

![](https://miro.medium.com/max/400/1*oOBq95MJ7eSc_mVWroDSrA.gif)

A bridge chain allows an independent blockchain such as Ethereum, with its own particular consensus mechanism, to connect to the Polkadot network. As opposed to parachains, where security is pooled, a network like the Ethereum mainnet will stay independent.

![](https://miro.medium.com/max/700/0*QJ47YlScWUtYnoGD)

<u>While bridge designs are now getting to a place where they are sufficiently planned out, there has not been too many that have been used heavily in production. For this reason, you can consider this as a **work in progress**.</u> It will be updated as more information is determined and available.

https://wiki.polkadot.network/docs/learn-bridges

## Substrate

### What is substrate

[Substrate](https://substrate.dev/) is a modular framework that enables you to **create purpose-built blockchains** by composing custom or pre-built components.

The idea was to take everything learned building Ethereum and Bitcoin implementations and make creating a blockchain as easy and flexible as possible. Substrate was created in a modular way to give technical freedom but also make functionalities like accounts, balances, governance, and smart contracts as easy as plugging in a library.

### Substrate and Polkadot

Substrate has been developed to create blockchains that will easily connect to Polkadot.

To create a blockchain and connect it to Polkadot, on a technical level, you could build your own blockchain from scratch and equip it with a block validation function in WebAssembly. From scratch means that you have to implement your own node, RPC synchronization, networking, cryptography, database, storage, consensus, as well as extended features such as a light client and telemetry. This bare-bones approach is known as **Polkadot Core**.

If you don’t want to create all of those for scratch, you can start with **Substrate Core.** Substrate Core provides you with all the above functionalities, only requiring you to **code your own runtime (state transition function)**, but also offers the possibility to customize networking, block authoring, and the transaction queue functionality.

### Forkless runtime upgrades

So far, the implications of Polkadot’s interoperability on scalability and security have been highlighted. However, there is also the question of what happens in case of a fork. Once blockchains become interdependent, a fork has tremendously larger impact than in the case of a siloed network splitting in two individual siloed networks. This is true for the relay chain as well as parachains and brings us to one of Substrate’s most exciting features, **the forkless runtime upgrade**.

#### Updating the network - the usual way

The common practice for upgrading a blockchain network is to change the rules of the underlying protocol. This is done to introduce new features or alter the existing rules.

If the upgrade is not compatible with older versions, this forces nodes to update in order to continue participating in the current version of the network.

Nodes who do not update will be unable to understand the new rules and will be separated from the network, possibly forming an alternative network.

#### Updating the network - the Substrate way

Since Polkadot is built on top of Substrate, we can utilize a handy feature of Substrate to circumvent forks in the relay chain and also every other blockchain built with Substrate. How do Substrate on-chain updates work?

If a block is produced and propagated through the network, the Substrate nodes will execute that block after performing various generic checks on it. Executing a block is fulfilled by the Substrate nodes, but the impulse to do so comes from the chain runtime.

**What does executing a block imply?** Once the block is determined valid by consensus, a node will start to execute the state transitions it contains. If successful, the node will store the block. While processing the state transitions, the node will effectively access its merklised storage database, which is basically a key-value store. Substrate operates on this key-value store with the basic functions ***set_state\*** and ***get_state\***. Now Substrate can be provided with a special case for ***set_state\***, namely with the key ***:code\***. In this case, Substrate expects to be provided with a WebAssembly (Wasm) runtime. Since the blockchain runtime runs in Wasm itself, it can be presented with a new binary blob of Wasm code. This piece of code will be executed as part of the state transitions, causing the runtime to update as specified in the blob.

![img](https://miro.medium.com/max/700/0*6JfP4XZau1fEkXl2)

Every time Substrate executes a block, it checks its version of the code, being able to switch from native runtime to the Wasm runtime on-chain in case the node is not running the most recent version.

### Substrate Architecture

- **Storage**: used to persist the evolving state of a Substrate blockchain. The blockchain network allows participants to reach trustless [consensus](https://substrate.dev/docs/en/knowledgebase/advanced/consensus) about the state of storage. Substrate ships with a simple and highly efficient [key-value storage mechanism](https://substrate.dev/docs/en/knowledgebase/advanced/storage).
- **Runtime**: the logic that defines how blocks are processed, including state transition logic. In Substrate, runtime code is compiled to [Wasm](https://substrate.dev/docs/en/knowledgebase/getting-started/glossary#webassembly-wasm) and becomes part of the blockchain's storage state. This enables one of the defining features of a Substrate-based blockchain: [forkless runtime upgrades](https://substrate.dev/docs/en/knowledgebase/runtime/upgrades#forkless-runtime-upgrades). Substrate clients may also include a "native runtime" that is compiled for the same platform as the client itself (as opposed to Wasm). The component of the client that dispatches calls to the runtime is known as the [executor](https://substrate.dev/docs/en/knowledgebase/advanced/executor), whose role is to select between the native code and interpreted Wasm. Although the native runtime may offer a performance advantage, the executor will select to interpret the Wasm runtime if it implements a newer [version](https://substrate.dev/docs/en/knowledgebase/runtime/upgrades#runtime-versioning).
- **Peer-to-peer network**: the capabilities that allow the client to communicate with other network participants. Substrate uses the Rust implementation of the [`libp2p` network stack](https://libp2p.io/) to achieve this.
- **Consensus**: the logic that allows network participants to agree on the state of the blockchain. Substrate makes it possible to supply custom consensus engines and also ships with several consensus mechanisms that have been built on top of [Web3 Foundation research](https://w3f-research.readthedocs.io/en/latest/index.html).
- **RPC** (remote procedure call): the capabilities that allow blockchain users to interact with the network. Substrate provides HTTP and WebSocket RPC servers.
- **Telemetry**: client metrics that are exposed by the embedded [Prometheus](https://prometheus.io/) server.

<img src="https://substrate.dev/docs/assets/substrate-arch.png" style="zoom:33%;" />

### Substrate Runtime

The core Substrate codebase ships with [FRAME](https://substrate.dev/docs/en/knowledgebase/runtime/frame), Parity's system for Substrate runtime development that is used for chains like [Kusama](https://github.com/paritytech/polkadot/blob/master/runtime/kusama/src/lib.rs) and [Polkadot](https://github.com/paritytech/polkadot/blob/master/runtime/polkadot/src/lib.rs). FRAME defines additional runtime primitives and provides a framework that makes it easy to construct a runtime by composing modules, called [pallets](https://substrate.dev/docs/en/knowledgebase/runtime/pallets). Each pallet encapsulates domain-specific logic that is expressed as a set of a [storage items](https://substrate.dev/docs/en/knowledgebase/runtime/storage), [events](https://substrate.dev/docs/en/knowledgebase/runtime/events), [errors](https://substrate.dev/docs/en/knowledgebase/runtime/errors) and [dispatchable functions](https://substrate.dev/docs/en/knowledgebase/getting-started/glossary#dispatch). FRAME developers can [create their own pallets](https://substrate.dev/docs/en/knowledgebase/runtime/pallets) and reuse existing pallets, including [over 50 that ship with Substrate](https://substrate.dev/docs/en/knowledgebase/runtime/frame#prebuilt-pallets).

<img src="https://substrate.dev/docs/assets/frame-runtime.png" style="zoom:33%;" />

https://substrate.dev/docs/en/knowledgebase/runtime/

### Substrate Pallets

Pallets are a special kind of Rust module made up of a set of types, trait implementations and functions from which Substrate runtimes can be composed. FRAME not only provides a library of commonly used Substrate pallets but also a framework to build custom domain-specific pallets, giving runtime engineers the flexibility to define their runtime's behaviour according to their target use case. The result: each pallet has its own discrete logic which can modify the features and functionality of your blockchain's state transition functions.

For example, the [Balances pallet](https://github.com/paritytech/substrate/tree/master/frame/balances), which is included in [FRAME](https://substrate.dev/docs/en/knowledgebase/runtime/frame), defines cryptocurrency capabilities for your blockchain. More specifically, it defines:

- **Storage items** that keep track of the tokens a user owns.
- **Functions** that users can call to transfer and manage those tokens.
- **APIs** which allow other pallets to make use of those tokens and their capabilities.
- **Hooks** which allow other pallets to trigger function calls when a user's balance changes.

https://substrate.dev/docs/en/knowledgebase/runtime/pallets

### Smart contracts

Substrate provides two smart contract virtual machines which can be added to your runtime. Each come with additional tools to ease development depending on your use cases.

#### Contracts Pallet

The [**FRAME Contracts pallet**](https://substrate.dev/rustdocs/latest/pallet_contracts/index.html) provides functionality for a Substrate runtime to deploy and execute WebAssembly Smart Contracts. It has its own smart contract language, specially designed to write contracts that optimize for correctness, conciseness and efficiency. Learn more [in this section](https://substrate.dev/docs/en/knowledgebase/smart-contracts/contracts-pallet).

#### EVM Pallet

The [**FRAME EVM pallet**](https://docs.rs/pallet_evm/) provides an EVM execution environment for Substrate's Ethereum compatibility layer, known as [Frontier](https://github.com/paritytech/frontier). It allows unmodified EVM code to be executed in a Substrate-based blockchain, designed to closely emulate the functionality of executing contracts on the Ethereum mainnet within the Substrate runtime. Learn more in [this section](https://substrate.dev/docs/en/knowledgebase/smart-contracts/evm-pallet).

https://substrate.dev/docs/en/knowledgebase/smart-contracts/

### Off-chain features

There is often a need to query and/or process off-chain data before it can be included in the on-chain state. The conventional way of doing this is through oracles. Oracles are external services that typically listen to blockchain events and trigger tasks accordingly. When these tasks complete, their results are submitted back to the blockchain using transactions. While this approach works, it still has several flaws with respect to security, scalability, and infrastructure efficiency.

To make the off-chain data integration secure and more efficient, Substrate provides off-chain features:

- **Off-Chain Worker (OCW)** subsystem allows execution of long-running and possibly non- deterministic tasks (e.g. web requests, encryption/decryption and signing of data, random number generation, CPU-intensive computations, enumeration/aggregation of on-chain data, etc.) that could otherwise require longer than the block execution time.
- **Off-Chain Storage** offers storage that is local to a Substrate node that can be accessed both by off-chain workers (both read and write access) and on-chain logic (write access via off-chain indexing but not read access). This is great for different worker threads to communicate to each others and for storing user- / node-specific data that does not require consensus over the whole network.
- **Off-Chain Indexing** allows the runtime, if opted-in, to write directly to the off-chain storage independently from OCWs. This serves as a local/temporary storage for on-chain logic and complement to its on-chain state.

Off-chain features run in their own Wasm execution environment outside of the Substrate runtime. This separation of concerns makes sure that block production is not impacted by long-running off-chain tasks. However, as the off-chain features are declared in the same code as the runtime, they can easily access on-chain state for their computations.

<img src="https://substrate.dev/docs/assets/off-chain-workers-v2.png" style="zoom: 50%;" />

**Example of implementation:** [Local Storage in Off-chain Workers](https://substrate.dev/recipes/off-chain-workers/storage.html)

https://substrate.dev/docs/en/knowledgebase/learn-substrate/off-chain-features

## Useful links

- [A brief summary of everything Substrate and Polkadot](https://medium.com/polkadot-network/a-brief-summary-of-everything-substrate-and-polkadot-f1f21071499d)
- [Moonbeam](https://moonbeam.network/)
- [Edgeware](https://edgewa.re/)
- [Gossamer: A Go implementation of the Polkadot Host](https://github.com/ChainSafe/gossamer)
- [ChainBridge: A Go Modular Multi-Directional Blockchain Bridge](https://github.com/ChainSafe/ChainBridge)
- [The Polkadot Parachain Host Implementers' Guide](https://w3f.github.io/parachain-implementers-guide/index.html)
- [Substrate Recipes](https://substrate.dev/recipes/introduction.html)
- [Frontier Workshop](https://substrate.dev/frontier-workshop/#/)
- [Substrate Smart Contract Workshop](https://substrate.dev/substrate-contracts-workshop/#/0/introduction)

------

# Yellow Chain R&D

- [x] Recap all ecosystem used in Yellow Chain
- [x] Setup a simple substrate chain
- [ ] Expand substrate chain with pallets
- [ ] Implement test smart contracts (ERC20 for example)
- [ ] Choose if we setup a parachain or a custom chain using a bridge to connected to other chains (Maybe using ChainBridge)
- [ ] Explore IPFS feasibility with Substrate off-chain features
- [ ] Explore the idea of storing/running TradePoint strategies directly on Yellow Chain (using off-chain features)
- [ ] Explore Vault equivalent for storing secrets inside the chain
- [ ] Get a promotion
