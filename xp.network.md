# Open Grant Proposal

- **Project Name:** XP.network VM Hub.
- **Team Name:** XP.network.
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj.

## Project Overview

### Overview

XP.network is a codeless platform for building blockchain agnostic NFT DApps. XP.network allows non-developers to build their NFT marketplaces, galleries, museums and many more use-cases without coding.

In order to synchronize communication between different [parachains](https://research.web3.foundation/en/latest/polkadot/XCMP/index.html) we want to build our own protocol which will be used by a network of XP.network pallets, that can be attached to and be used by any parachain.

Since it is currently hard to trace whether an incoming message is related to any previous transaction or request, we will elaborate a protocol that will enable such tracking by the "TOPIC ID", which is especially useful when multiple transactions are executed between two blockchains. It will be a group of pallets, each acting like a “post office” in a post office network, all implementing our XP Relay Chain protocol.

Since different blockchains may use different smart contract languages, we are developing an automated toolbox that will communicate via the Polkadot Relay Chain and will generate in the target pallet a valid code in Move, Solidity and Rust (Ink!) which is validated and compiled to byte-code to interact with the target blockchains. After the target blockchain has executed or rejected the transaction, the information about this is packed back into the reply XP Relay Chain protocol message and is sent back to the requesting pallet for passing it to the requesting blockchain.

Because there's no NFT library in Move's standard library we will have to write it as part of this project. This will allow: 
1. other blockchains to call NFT smart contracts in Move, 
2. to run NFT smart contracts in other languages from Move.

#### Integration

Any parachain, parathread or bridge can attach our pallet and use its functionality. The pallets will act as a “post office network” supporting the protocol that enables nodes to keep track of the “Topics”. They will also "translate" the intentions of one parachain, regardless of its smart contract language, to a smart contract in the language of the target parachain / parathread. We're currently working on pallets for three smart contract languages: Move, Rust and Solidity.

All the NFT based applications built by our codeless platform will be using the XP Relay Chain protocol we're building.

### Project Details

The project is comprised of 2 large interdependant deliverables:

1. The XP.network Relay Chain Protocol, which will enable parachains to communicate their smart contracts in a language agnostic "intention" format. It is designed to keep track of the "TOPIC" of negotiation.

2. Three Substrate Pallet templates that will communicate with each other implementing the XP.network protocol and will convert their original Move, Solidity & Rust smart contract bytecodes to a language agnostic "intention". This "intention" will be sent as payload in a Relay Chain callback to the target XP.network pallet attached to a designated parachain. There, the "intention" will be converted to a smart contract of the target blockchain. Once the target blockchain responds, the initiating parachain gets notified according to the XP.network protocol.


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

![img](https://github.com/xp-network/w3f_application/blob/main/XP.network%20Protocol.png)

Every parachain equipped with our pallet will know how to read such incoming messages. If the message is related to the blockchain this pallet is attached to, it will do the following:

1. Transform the commands and arguments from the "intention" to a smart contract in the target language, used by the blockchain it works for.
2. It will pass the generated bytecode to the blockchain for execution.
3. Once there is a reply from the blockchain, the reply will be packed into the return message of the present protocol and sent back to the requesting pallet via the Polkadot Relay Chain callbacks with the same id it got the request.
4. The pallet, which initiated the transaction, will receive the reply and will match the id of the reply with the id of the request.
5. It will deserialize the bytecode of the reply and will transform it to the bytecode of its smart contract language (should it be different from the counterpart’s).

A pallet implementing this protocol consists of:

1. **Bytecode Deserializer** - it receives a smart contract bytecode as input and returns a human readable opcode. 
2. **Bytecode Compiler** - it takes the key - value pairs generated at the previous stage as an input and generates the chosen smart contract language bytecode as its output.
3. **Polkadot pallet** - it uses the Relay Chain callback mechanism to communicate with the other parachains and parathreads using XP Network protocol.

A set of pre-programmed audited code templates are ready to be populated by the arbitrary data. Once a request is received, the templates are populated with the incoming data and are instantly compiled into transaction ready bytecode. Initially there will be a limited set of ready smart contract opcode templates for each platform, currently 20, each representing a different use case. However, new templates will be added on a permanent regular basis. Eventually most possible use cases will be available for each bridged platform.

#### [PoC](https://github.com/xp-network/xp-compiler)

Before applying for the grant we have prepared a GitHub repository with the Proof of Concept.

Since the idea behind the VM Hub is converting one smart contract language bytecode to another we have elaborated the following steps:

1. We wrote a short smart contract in Solidity.
2. We compiled it to bytecode with the solc compiler.
3. We deserialized the bytecode into opcode.
4. An array of opcodes became a bytecode template:
   a. Commands (MSTORE, SSTORE, CALLVALUE, ISZERO, JUMPI, REVERT… )
   b. Data (PUSH 80, PUSH [$] 000000000000000000000000000000000000000001, etc.)
5. We wrote a request in Move which will specify which template we want to use and will provide the data to populate the template with new data.
6. The code in Move is then compiled to bytecode & passed to our Substrate pallet.
7. The pallet deserializes the Move bytecode and extracts the instruction - which template to use and the data to populate it with.
8. The data from the request is used to populate the opcode parameters in the template.
9. The template is reassembled to bytecode.
10. We run the two bytecodes (from steps 2 & 9) in the Ethereum testnet.
11. If both the smart contracts run in the testnet and produce the same result - the concept is proved.

The above process is automated and can be reproduced on any machine.

#### The XP.network Hub is not:
This hub is by no means a bridge between the blockchains. It completely relies on Polkadot's existing infrastructure to communicate and secure interaction between the parathreads. However, we have plans of building bridges to a number of blockchains in further projects.

#### Expected obstacles
For efficiency in EVM higher order bits of types narrower than 256 bits, e.g. uint24 may be ignored or cleaned shortly before writing them to memory or before comparisons. This means, before comparison or saving higher order bits must be “manually” cleaned.

In the Move language the code is divided into scripts and Modules. When scripts are compiled to the bytecode they become straightforward opcodes, however, modules can be recursively called from the scripts or from the other modules. Thus, modules deserialisation is much more complex and also requires recursiveness to retrieve the content of all the functions or data stored in different parts of the stack.

#### Further development
We cannot get rid of the target language dependency, however, the source language can be abstracted to an API protocol able to call predefined smart contracts in supported languages (Move, Solidity and Rust) from other programming languages. 

We’re planning to elaborate the most popular smart contract bytecode templates in 3 languages: Move, Solidity and Rust and write APIs for compiling and sending the resulting bytecodes to the supported blockchains.

The smart contract templates are a temporary solution. Eventually we will add the flexibility to directly map the smart contracts's opcodes from one language to another without predefined templates.

### Ecosystem Fit

- The VM Hub will eventually develop to become programming language independent and will enable yet nonexistent languages to seamlessly integrate with the established blockchains via Polkadot.

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
| 0b. | Description | We will support all functional modules that have been implemented on Polkadot. |
| 0c. | Delivery time | Mid June |
| 0d. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 1. | Move bytecode deserialiser | We will develop a deserialiser of Move bytecode to Move opcode with recursive modules deserialisation |
| 2. | Bytecode compiler (for Solidity) | We will build an EVM compliant Solidity optcode to bytecode mapper |
| 3. | Intention builder | We will develop a tool for building language independent "intentions" serving as structured messages in the XP.network protocol | 
| 4. | Intentions compiler from Move | We will develop a tool for populating language independant "intentions" from Move optcode | 
| 5. | XP Relay Chain Protocol | XP Relay Chain Protocol is developed and documented |
| 6. | Testing in Solidity Testnet| 20 Solidity smart contract bytecodes generated by step 2 are tested and debugged with Ethereum testnet till our smart contracts run without errors and the required resources are minted and burned in the blockchain |



### Milestone 2 — VM Hub pallet Solidity Code to Move bytecode (MVP)

- **Estimated Duration:** 34 working days (1.5 months)
- Working days **x** ppl. **:** 34 **x** 2
- Effort: 68 days
- **Costs:** $8,500

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Description | We will support all functional modules that have been implemented on Polkadot. |
| 0c. | Delivery time | Beginning of August |
| 0d. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 1. | Solidity bytecode deserializer | We will develop a deserialiser of Solidity bytecode to Solidity opcode |
| 2. | Bytecode compiler (for Move) | We will build a MoveVM compliant Move optcode to bytecode mapper |
| 3. | Intentions compiler from Solidity | We will develop a tool for populating language independant "intentions" from Solidity optcode | 
| 4. | Move NFT Library | NFT library is developed in Move |
| 5. | Substrate Pallet (for Move & Solidity) | 2 template pallets implementing the XP Relay Chain protocol are developed and communicate with "intentions" | 
| 6. | Testing in Diem testnet| 20 Move smart contract bytecodes generated by step 2 are tested and debugged with the Diem testnet till our smart contracts run without errors and the required modules & resources are saved in the blockchain |

### Milestone 3 — VM Hub pallet Move Code & Solidity bytecode to Rust bytecode

- **Estimated Duration:** 33 working days (1.5 months)
- Working days **x** ppl. **:** 33 **x** 2
- Effort: 66 days
- **Costs:** $8,250

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Description | We will support all functional modules that have been implemented on Polkadot. |
| 0c. | Delivery time |Beginning of September |
| 0d. | Documentation | Documents containing product architecture as well as basic user manuals  |
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
| 0b. | Description | We will support all functional modules that have been implemented on Polkadot. |
| 0c. | Delivery time |Mid of October |
| 0d. | Documentation | Documents containing product architecture as well as profound user manuals  |
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
