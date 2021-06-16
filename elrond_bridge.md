# Open Grant Proposal

- **Project Name:** XP.network Substrate Parachain - Elrond Cross-Chain Communication Bridge (CCCB).
- **Team Name:** XP.network.
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj.

## Project Overview

### Overview

XP.network is a codeless platform for building blockchain agnostic NFT DApps. XP.network allows non-developers to build their NFT marketplaces, galleries, museums and many more use-cases without coding. We will connect our users's applications to different blockchains via Polkadot.

In this project, we aim to bring Elrond’s users and ecosystem to Polkadot.

### Project Details

The main product of the project is a Cross-Chain Communication Bridge (CCCB) for two non-identical unsynchronized blockchains A (Substrate Parachain) and B (Elrond).

####The structure of the CCCB:
#####1. A Substrate pallet implementing the following functionality:
  + Fungible liquidity freezing  for a Parachain native token.
  + Non-fungible liquidity freezing.
  + Fungible liquidity release to an arbitrary account (wrapped eGold)
  + Non-fungible liquidity release to an arbitrary account.
  + Support of cross-chain RPC with an arbitrary number of arguments.
  + Bridge relay validator subscription mechanism implementation.
  + BFT consensus mechanism implementation.
####2. Relay validator/prover written in TypeScript. Supplied in a docker container.
####3. “Freezer” smart contract written in Rust deployable on Elrond blockchain.
  + Fungible liquidity freezing (eGold)
  + Non-fungible liquidity freezing.
####4. “Minter” smart contract written in Rust deployable on Elrond blockchain.
  + Fungible liquidity release to an arbitrary account (wrapped Parachain native token)
  + Non-fungible liquidity release to an arbitrary account.
  + Support of cross-chain RPC with an arbitrary number of arguments.
  + Bridge relay validator subscription.
  + BFT consensus mechanism.


Notes:
At the moment, Elrond has not implemented smart contract event emission, therefore our smart contract will store the transaction related events in its storage. Our validators will be requesting whether the data has changed in the smart contract in a loop with the 6 sec. delay (round length) for a fixed number of rounds. Once Elrond implements proper event emission (approximately in 2-3 months), we will use the native flow, since it will be faster and more efficient.


### Ecosystem Fit

  + Users will be able to transfer their liquidity or NFTs from any Substrate parachain to Elrond and backwards once they attach the XP.network pallet to their parachain or parathread.
  + Substrate parachain smart contracts will be able to call smart contracts functions in Elrond and backwards with an arbitrary number of arguments via or relay validators.

## Team

### Team members

- Dmitry Brook - CTO, Project Lead
- Virbal Kint - Expert in Move, Rust & Co-Founder
- Rupansh Sekar - Expert in C, Rust and blockchain development

### Contact

- **Contact Name:** Dmitry Briukhanov

- **Contact Email:** dima@xp.network

- [XP.network website](https://xp.network/)


### Legal Structure

- **Registered Address:** HaHagana, 15, Or Yehuda, Israel
- **Registered Legal Entity:** XP Network


### Team Experience

**Dmitry Briukhanov**

- Over 18 years of experience in Development and Management 
- Former Senior Software Developer in Best Systems, Israel
- Former Team Lead in NLK, Russia

**Verbal Kint**

- Over 5 years of experiences in Blockchain Development 8 years in Rust, 1 year in Move and 15 years of development experience overall

**Rupansh Kek**

- Over 1 year of experiences in Blockchain Development 2 years in Rust, 3 years in C


### Team Code Repos

[Rust Documentation](https://xp-network.github.io/poc-documentation/docs/freezer/) Method names, parameters with types, return types and description.
[PoC repository](https://github.com/xp-network/XP.network-Elrond-Migration)

### Team LinkedIn Profiles

[Dmitry Bryukhanov](https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/)


## Development Roadmap

### Overview

- **Total Estimated Duration:** 1 month
- **Total Effort:** 20 days
- **Total Costs:** $ 0.00


### Milestone 1 - Alpha version of CCCB

- **Estimated Duration:** 10 working days (2 weeks)
- Working days **x** ppl. **:** 5 **x** 2
- Effort: 10 days
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time | End of June |
| 0c. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 0d. | Tests | Interactive liquidity transfer tests in local testnents deployed in a docker container  |
| 0e. | Video tutorial | A video demonstrating how to deploy the smart contracts and run the interactive tests |
| 1. | CCCB architecture | CCCB architecture is developed and documented in textual descriptions and UML diagrams |
| 2. | Smart Contracts in Ink! | Functionality: 1. Fungible & Non-fungible token locking & releasing 2. RPC functionality 3. Relay validators subscription and BFT validation |
| 3. | Semi-decentralized relay validators | Bridge relay validators written in Typescript |
| 4. | Smart Contracts in Rust (on Elrond) | Functionality: 1. Fungible & Non-fungible token locking & releasing 2. RPC functionality 3. Relay validators subscription and BFT validation |



### Milestone 2 — Beta version of CCCB

- **Estimated Duration:** 10 working days (2 weeks )
- Working days **x** ppl. **:** 5 **x** 2
- Effort: 10 days
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Delivery time | End of July |
| 0c. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 0d. | Video Tutorial | Preparing tutorials with examples 1. How to attach an XP.Network CCCB pallet to a parachain, 2. How to use the CCCB to interact with Elrond blockchain | 
| 1. | Substrate pallet | Migration of the smart contracts in Ink! to a substrate pallet in Rust  |
| 2. | Unit tests | We will write automated unit tests with for the 1 & 2 milestones with min 85% code coverage and will test the Elrond's side in the live Elrond Testnet, while the test Substrate parachain will run in synq with ROCOCO |
| 3. | Transaction fees solution | Solving the transaction fee in the “foreign” blockchain problem | 
| 4. | Security audit | Testing the system resilience to fraudulent or erroneous validators | 



## Future Plans

+ We will develop a DEX between the coins of the Substrate Parachains to Elrond and back.
+ We will make the relay validators completely decentralized, implementing the PoS consensus mechanism.

## Additional Information

[The project repo:](https://github.com/xp-network)
The present bridge is built in close cooperation with the Elrond team.

[Elrond announcement](https://t.me/ElrondNetworkAnn/1121)

