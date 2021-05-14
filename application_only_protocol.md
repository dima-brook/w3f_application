# Open Grant Proposal

- **Project Name:** XP.network VM Hub.
- **Team Name:** XP.network.
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj.

## Project Overview

### Overview

XP.network is a codeless platform for building blockchain agnostic NFT DApps. XP.network allows non-developers to build their NFT marketplaces, galleries, museums and many more use-cases without coding.

In order to synchronize communication between different [parachains](https://research.web3.foundation/en/latest/polkadot/XCMP/index.html) we want to build our own protocol which will be used by a network of XP.network pallets, that can be attached to and be used by any parachain.

Since it is currently hard to trace whether an incoming message is related to any previous transaction or request, we will elaborate a protocol that will enable such tracking by the "TOPIC ID", which is especially useful when multiple transactions are executed between two blockchains. It will be a group of pallets, each acting like a “post office” in a post office network, all implementing our XP Relay Chain protocol.

#### Integration

Any parachain, parathread or bridge can attach our pallet and use its functionality. The pallets will act as a “post office network” supporting the protocol that enables nodes to keep track of the “Topics”. They will also "translate" the intentions of one parachain, regardless of its smart contract language, to a smart contract in the language of the target parachain / parathread.

### Project Details

The project is comprised of 2 large interdependant deliverables:

1. The XP.network Relay Chain Protocol, which will enable parachains to communicate their smart contracts in a language agnostic "intention" format. It is designed to keep track of the "TOPIC" of negotiation.

2. A Substrate Pallet implementing the XP.network protocol The message will be packed as payload in a Relay Chain callback to the target XP.network pallet attached to a designated parachain. Once the target blockchain responds, the initiating parachain gets notified according to the XP.network protocol.


**XP Relay Chain Protocol** will be supported by a number of pallets, each acting as a “post office” for its parathread. A typical message will include:
```terminal
{
ID:                 id,               //required to identify that the other blockchain’s reply is related to this request,
CallbackFunction:   funcName,         // a designated Polkadot Relay Chain callback function,
CallbackArguments:  [ ... ],          // required for the above function,
To:                 dest,             // indicates the destination parachain / parathread,
Payload:            blob              // A binary representation of the "intention"
}
```

The runtime [storage](https://substrate.dev/rustdocs/v3.0.0/frame_support/storage/trait.StorageValue.html#required-methods) & the message inside the binary payload will be structured as follows:

![img](https://github.com/xp-network/w3f_application/blob/main/XP.network.protocol.png)

Flags:</br>
**INT** - Integrity violation </br>
**IER** - Ininitiating pallet error </br>
**NER** - Network error </br>
**DER** - Destination pallet error </br>
**ACK** - Acknowledged </br>
**OK** - Successfull transaction (TX) in the target parachain </br>
**REJ** - Rejection of the TX in the target parachain </br>
**END** - End of transaction, the blob can be erased from memory </br>

The **XP.network Handshake protocol** will roughly look like this:

![img](https://github.com/xp-network/w3f_application/blob/main/XP.network%20Protocol-2.png)

Every parachain equipped with our pallet will know how to read such incoming messages. If the message is related to the blockchain this pallet is attached to, it will do the following:

1. Confirm to the initiating party that the message has been received.
2. Check the integrity of the payload
3. Listen to the events in the target blockchain.
4. Inform the initiating party of the success or error while executing the transaction in the target blockchain.
5. Instruct the counterpart to free the runtime storage once the transaction is over.

A pallet implementing this protocol consists of:

1. **Payload deserialiser** - it reads the contents of the binary file and populates the fields of the Message struct.
2. **Payload serialiser** - it packs the values of the Message struct into a binary representation.
3. **Polkadot pallet** - it uses the Relay Chain callback mechanism to communicate with the other parachains and parathreads using XP Network protocol.

#### The XP.network Hub is not:
This hub is by no means a bridge between the blockchains. It completely relies on Polkadot's existing infrastructure to communicate and secure interaction between the parathreads. However, we have plans of building bridges to a number of blockchains in further projects.

#### Further development

We may add more flags to reflect more states of the negotiated transaction, for example, we can specify the reasons of rejection in the target blockchain, for example, due to lack of gas.

### Ecosystem Fit

- The XP.network protocol allows parachains to communicate in a connectionless but ordered and reliable way.
- All it takes is to attach the XP.network pallet to a parachain.

## Team

### Team members

- Dmitry Brook - CTO, Project Lead.
- Virbal Kint - Expert in Move, Rust & Co-Founder

### Contact

- **Contact Name:** Dmitry Briukhanov

- **Contact Email:** dima@xp.network.com

- [XP.network website](https://xp.network/)

### Legal Structure

- **Registered Address:** HaHAgana, 15, Or Yehuda, Israel
- **Registered Legal Entity:** XP Network

### Team Experience

**Dmitry Briukhanov**

- Over 18 years of experience in Development and Management 
- Former Senior Software Developer in Best Systems, Israel
- Former Team Lead in NLK, Russia

**Verbal Kint**

- Over 5 years of experiences in Blockchain Development 8 years in Rust, 1 year in Move and 15 years of development experience overall


### Team Code Repos (PoC)
[PoC Documentation](https://xp-network.github.io/poc-documentation/) Method names, parameters with types, return types and description.

[Move Compiler](https://github.com/xp-network/move-compiler) Generates human readable code in Move.

[Solidity Compiler](https://github.com/xp-network/solidity-compiler) Generates human redable and byte code in Solidity.

[EVM to Assembler](https://github.com/xp-network/evm-asm) Converts Solidity bytecode to opcode.

[XP Compiler](https://github.com/xp-network/xp-compiler) PoC project. Accepts a comand in Move, or JSON and compiles bytecode in Solidity, tests it in the bytecode in Ropsten (Ethereum testnet).


### Team LinkedIn Profiles

[Dmitry Bryukhanov](https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/)

## Development Roadmap

### Overview

- **Total Estimated Duration:** 6 months.
- **Total Effort:** 240 days.
- **Total Costs:** $ 30,000

### Milestone 1 - VM Hub pallet Move bytecode to Solidity bytecode (pre-MVP)

- **Estimated Duration:** 20 working days (1 month)
- Working days **x** ppl. **:** 20 **x** 2
- Effort: 40 days
- **Costs:** $5000

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time | Mid June |
| 0c. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 1. | XP.network Protocol | XP.network protocol is developed and documented |
| 2. | XP.network Message serialiser | The binary Message serialiser is built |
| 3. | XP.network Message deserialiser | The binary Message deserialiser is built |


### Milestone 2 — VM Hub pallet Solidity Code to Move bytecode (MVP)

- **Estimated Duration:** 34 working days (1.5 months)
- Working days **x** ppl. **:** 34 **x** 2
- Effort: 68 days
- **Costs:** $8,500

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time | Beginning of August |
| 0c. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 1. | XP.network pallet | A Substrate pallet implementing the XP.network protocol is built |
| 2. | Relay Cain integration | The Polkadot Relay Chain callbacks  |

### Milestone 3 — VM Hub pallet Move Code & Solidity bytecode to Rust bytecode

- **Estimated Duration:** 33 working days (1.5 months)
- Working days **x** ppl. **:** 33 **x** 2
- Effort: 66 days
- **Costs:** $8,250

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time |Beginning of September |
| 0c. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 1. | Intentions compiler from Rust | Developing opcode parser for Rust and generating “intentions” from the parsed opcode |
| 2. | Intentions callers | 20 Rust example smart contracts to be called with the “intentions” originating in Move | 
| 3. | Intentions callers | 20 Rust example smart contracts to be called with the “intentions” originating in Solidity | 
| 4. | Intentions callers | 20 Move example smart contracts to be called with the “intentions” originating in Rust | 
| 5. | Intentions callers | 20 Solidity example smart contracts to be called with the “intentions” originating in Rust | 
| 6. | Bytecode compiler (for Rust) | 20 Rust example smart contracts are mapped to Rust bytecode | 
| 7. | Substrate Pallet (for Rust) | 1 template pallet implementing the XP Relay Chain protocol is developed |
| 8. | Testing in Polkadot Westnet| Testing and debugging 20 example smart contracts inside Rust bytecode in Polkadot Westnet till the transaction executes and the result is added to the blockchain | 

### Milestone 4 — Smart Contract templates in Move, Solidity, Rust, testing and launch

- **Estimated Duration:** 33 working days (1.5 months)
- Working days **x** ppl. **:** 33 **x** 2
- Effort: 66 days
- **Costs:** $8,250

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time |Mid of October |
| 0c. | Documentation | Documents containing product architecture as well as profound user manuals  |
| 1. | Testing in Westend | XP Relay Chain Protocol is tested between tree pallets (Move, Rust & Solidity) messaging protocol till they interact according to the protocol assuming a bad actor does interrupt the normal flow or errors occur in one of the pallets |
| 2. | Integrating with Polkadot | Testing and debugging the XP Network Protocol in live Polkadot environment |
| 3. | Compliance Validator | Developing automated tests for: 1. pallets (Move, Rust, Solidity), 2. XP.network protocol interactions between pallets: Move -> Solidity, Solidity -> Move, Move -> Rust, Rust -> Move, Solidity-> Rust, Rust -> Solidity, min 60% code coverage |
| 4. | Documentation | Writing and publishing final documentation with all the amendments 1. XP.Network protocol, 2. XP.network pallets (for Move, Rust & Solidity)|
| 5. | Tutorials | Preparing and publishing tutorials with examples and exercises 1. How to use XP.network Protocol, 2. How to attach an XP.Network pallet to a parachain (for Move, Rust & Solidity)| 

## Future Plans

**Community Plan**

Hiring 3-5 more developers

Joining Polkadot related events

Publishing articles in Telegram, medium.com and other channels


**Development Plan**

The template bytecode chunks will become more atomic to add flexibility to the bytecode compilation process.

Eventually, we hope to enable our pallet to become completely dynamic, being able to process yet unseen non predefined smart contract patterns and convert them into valid bytecodes of a chosen smart contract language.

Writing pallets with the XP Relay Chain protocols for the languages other than Solidity, Move and Rust.

Development of bridges to the Avalanche, Binance, Cardano, Diem? (should regulations allow), Elrond, Heco and Solana blockchains.

## Additional Information 

[The project repo:](https://github.com/xp-network)
