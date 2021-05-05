# Open Grant Proposal

- **Project Name:** XP Network VM Hub.
- **Team Name:** XP Network.
- **Payment Address:** .

## Project Overview

### Overview

XP Network aims to build a platform which allows to develop NFT based applications on Avalanche, Binance, Cardano, Diem blockchain, Elrond, Ethereum, Heco, Polkadot and Solana. This platform will enable artists, musicians, celebrities, game developers and other content providers to create personal online NFT marketplaces without writing code.

We are aspiring to create an automated toolbox that will generate valid code in Ink!, Move, Solidity and Rust which could be further validated and compiled to byte-code and interact with the corresponding blockchains via Polkadot Relay Chain. We will also write the currently nonexistent libraries for NFT creation in Move.

#### Integration

Connection to Polkadot will be implemented via a VM Hub Layer Substrate palette, where our auto generated code in Move will be processed and serialized in Solidity and vice versa. All the applications produced by our platform will use this pallet to interact with other blockchains and smart contracts via Polkadot. 

### Project Details

We will not further speak about the rest of the XP Network platform and will only concentrate on the VM Hub. The Project will consist of two blocks:

A. **Documentation** and tutorials.

B. The **VM Hub Layer**  is a Substrate pallet which allows different Virtual Machines to interact with each other. The Layer implements serialization and deserialization of data formats and shared storage between different VMs, making requests between them possible. For example, one could call a smart contract written in Solidity from a smart contract written in Move or Ink! and vice versa.

A user can choose between the following blockchains: Avalanche, Binance, Cardano, Diem, Elrond, Ethereum, Heco, Polkadot and Solana. Depending on the user’s blockchain choice, a smart contract programming language will be chosen. For example, if a user wants to interact with Ethereum, Solidity will be chosen as a target language.

This module consists of:

1. **Bytecode Deserializer** - it receives hexadecimal input and returns a human readable code in assembly. 
2. **Assembly Code Converter** - it  creates the following key - value pairs:
The chosen smart contract programming language.
Template number.
Data to populate the smart contract with.
3. **Bytecode Compiler** - it takes the key - value pairs generated at the previous stage as an input and generates the chosen smart contract language bytecode as its output.
4. **Polkadot parathread** - it uses the Relay Chain callback mechanism to communicate with the other parachains and parathreads using XP Network protocol. Since the existing mechanism is connectionless and stateless there’s no way to automatically be notified whether a message or a transaction succeeded. This is why every transaction is marked with a unique token. While waiting for the results of the transaction or to identify the incoming messages as related to the transaction the token is stored together with the transaction info.

A set of pre-programmed audited code templates are ready to be populated by the arbitrary data. Once a request is received, the templates are populated with the incoming data and are instantly compiled into transaction ready bytecode. Initially there will be a limited set of ready code templates for each platform. However, new templates will be added on a permanent regular basis. Eventually most possible use cases will be available for each bridged platform. Finally, the original smart contract bytecode will be translated directly into the target language bytecode, removing all limitations.

#### PoC

Before applying for the grant we have prepared GitHub repositories with the Proof of Concept.

Since the idea behind the VM Hub is converting one smart contract language bytecode to another we have elaborated the following steps:

Writing a short smart contract in Solidity.
Compiling it to bytecode with the solc compiler.
Deserialize the bytecode into opcode
Using the opcode to map the bytecode and cut it into blocks with
Commands (MSTORE, SSTORE, CALLVALUE, ISZERO, JUMPI, REVERT… )
Data (PUSH 80, PUSH [$] 000000000000000000000000000000000000000001, etc.)
 	this will become a bytecode template.
Writing a request in Move which will call. The call will specify which template we want to use and will provide the data to populate the template with new data.
The code in Move is compiled to bytecode & passed to our Substrate pallet.
The pallet deserializes the Move bytecode and extracts the instruction - which template to use and the data to populate it with.
The data from the request is used to assemble only the blocks of target bytecode expecting data.
The template is reassembled to bytecode.
The new data from the request in Move is used to populate the human readable smart contract in Solidity.
The new smart contract is compiled into bytecode with the native solc compiler.
The two resulting bytecodes are compared.
If the two bytecodes are identical the Concept is Proved.

####Expected obstacles:
For efficiency in EVM higher order bits of types narrower than 256 bits, e.g. uint24 may be ignored or cleaned shortly before writing them to memory or before comparisons. This means, before comparison or saving higher order bits must be “manually” cleaned.
In the Move language the code is divided into scripts and Modules. When scripts are compiled to the bytecode they become straightforward opcodes, however, modules can be recursively called from the scripts or from the other modules. Thus, modules deserialisation is much more complex and also requires recursiveness to retrieve the content of all the functions or data stored in different parts of the stack.


####Further development
We cannot get rid of the target language dependency, however, the source language can be abstracted to an API protocol able to call predefined smart contracts in supported languages (Move, Solidity and Rust) from other programming languages. This may initially seem to be a limitation, but the number of actually used smart contracts is not infinite. If all or nearly all known smart contract patterns are present in the template library it will give more freedoms than limitations.

We’re planning to elaborate the most popular smart contract bytecode templates in 3 languages: Move, Solidity and Rust and write APIs for compiling and sending the resulting bytecodes to the supported blockchains (see the list above).

### Ecosystem Fit

- All the NFT based applications built by XP Network Platform will interact with the designated blockchains via our VM Hub Substrate pallet.

- The VM Hub will eventually develop to become programming language independent and will enable yet nonexistent languages to seamlessly integrate with the established blockchains via Polkadot.

## Team

### Team members

- Dmitry Brook - CTO, Project Lead.
- Virbal Kint - Expert in Move, Rust & Co-Founder

### Contact

- **Contact Name:** Dmitry Briukhanov
- **Contact Email:** dima@xp.network.com
- [XP Network website] (https://xp.network.com)

### Legal Structure

- **Registered Address:** HaHAgana, 15, Or Yehuda, Israel
- **Registered Legal Entity:** XP Network

### Team Experience

**Dmitry Briukhanov**

- Over 8 years of experiences in Development and Management 
- Former Senior Project Manager in Best Systems

**Verbal Kint**

- Over 3 years of experiences in Blockchain Development in Rust and Move


### Team Code Repos
[XP Network repo] (https://github.com/xp-network/ )
[Move Compiler] (https://github.com/xp-network/move-compiler )
[Solidity Compiler] (https://github.com/xp-network/solidity-compiler )
[EVM to Assembler] (https://github.com/xp-network/evm-asm )
[VM Hub Pallet] (https://github.com/xp-network/vm_hub_pallet )
[XP Compiler] (https://github.com/xp-network/xp-compiler )

### Team LinkedIn Profiles

[Dmitry Bryukhanov] (https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/ )

## Development Roadmap

### Overview

- **Total Estimated Duration:** 6 months.
- **Total Effort:** 240 days.
- **Total Costs:** $30,000

### Milestone 1 - VM Hub pallet Move Code to Solidity bytecode (MVP)

- **Estimated Duration:** 20 working days (1 month)
- Working days **x** ppl. **:** 20 **x** 2
- Effort: 40 days
- **Costs:** $5,000

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0. | Documentation | Documents containing product architecture as well as usage manuals  |
| 1. | Bytecode deserialiser | Developing the Move and Solidity bytecode deserializers to opcode |
| 2. | Assembler parser | Developing opcode parser for Move and generating “intentions” |
| 3. | Intentions callers | Writing functions to be called with the “intentions” Move - Solidity | 
| 4. | Bytecode compiler | Development of the Solidity opcode mapper to bytecode | 
| 5. | Testing in Solidity Testnet| Running the generated bytecode in Ethereum testnet | 


### Milestone 2 — VM Hub pallet Solidity Code to Move bytecode

- **Estimated Duration:** 34 working days (1.5 months)
- Working days **x** ppl. **:** 34 **x** 2
- Effort: 68 days
- **Costs:** $8,500

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0. | Assembler Parser | Developing the parser for the Solidity opcode |
| 1. | Intentions callers | Writing functions to be called with the “intentions” Solidity - Move | 
| 2. | Bytecode compiler | Development of the Move opcode mapper to Move bytecode |
| 4. | Move NFT Libraries | Development of NFT libraries in Move |
| 5. | Testing in Diem Testnet| Running the generated Move bytecode in the Diem testnet | 

### Milestone 3 — VM Hub pallet Move Code & Solidity bytecode to Rust bytecode

- **Estimated Duration:** 33 working days (1.5 months)
- Working days **x** ppl. **:** 33 **x** 2
- Effort: 66 days
- **Costs:** $8,250

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 1. | Bytecode deserialiser | Developing the Rust bytecode deserializer to assembler |
| 2. | Assembler parser | Developing opcode parser for Rust and generating “intentions” |
| 3. | Intentions callers | Writing functions to be called with the “intentions” Move, Solidity - Rust and vice versa | 
| 4. | Bytecode compiler | Development of the Rust opcode mapper to bytecode | 
| 5. | Testing Polkadot Westnet| Running the generated bytecode in Polkadot Westnet | 

### Milestone 4 — Smart Contract templates in Move, Solidity, Rust, testing and launch

- **Estimated Duration:** 33 working days (1.5 months)
- Working days **x** ppl. **:** 33 **x** 2
- Effort: 66 days
- **Costs:** $8,250

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0. | Smart Contract templates | Writing optcode smart contract templates for known use cases in Move, Solidity, Rust |
| 1. | Integrating with Polkadot | Testing the PA in live Polkadot environment |
| 2. | Compliance Validator | Adding automated test for the above integrated modules |
| 3. | Documentation | Writing and publishing final documentation with all the amendments |
| 4. | Tutorials | Preparing and publishing tutorials with examples and exercises | 
| 5. | Product Launch | Publishing the XP Network VM Hub for public use |

## Future Plans

Community Plan

Hiring 3-5 more developers
Joining Polkadot related events
Publishing articles in Telegram, medium.com and other channels

Development Plan
The template bytecode chunks will become more atomic to add flexibility to the bytecode compilation process.
Eventually, we hope to enable our pallet to become completely dynamic, being able to process yet unseen non predefined smart contract patterns and convert them into valid bytecodes of a chosen smart contract language.
Development of bridges to the Avalanche, Binance, Cardano, Diem? (should regulations allow), Elrond, Heco and Solana blockchains.

## Additional Information

[The project repo:] (https://github.com/xp-network )
