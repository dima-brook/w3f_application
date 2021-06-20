# Open Grant Proposal

- **Project Name:** XP.network Substrate Parachain - Elrond Cross-Chain Communication Bridge (CCCB)
- **Team Name:** XP.network
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj

## Project Overview

### Overview

XP.network is a blockchain-agnostic platform for building NFT DApps. With XP.network, even non-developers can create NFT marketplaces, galleries, lending apps, and more - without a single line of code and on any blockchain, thanks to the interoperability features of Polkadot.

In this project, we aim to bring Elrond’s users and ecosystem to Polkadot.

### CCCB UI
![img](https://github.com/xp-network/w3f_application/blob/main/Cross%20Chain%20Bridge-1.png)

### Project Details

The proposed Cross-Chain Communication Bridge(CCCB) will link a Substrate Parachain and Elrond which are two non-identical unsynchronized blockchain, allowing liquidity transfers.

#### CCCB Structure:
#### 1. A Substrate pallet implementing the following functionality:
  + Fungible liquidity freezing  for a Parachain native token and wrapped eGold.
  + Fungible liquidity release to an arbitrary account of wrapped eGold or Parachain native token.
  + Non-fungible liquidity freezing.
  + Non-fungible liquidity release to an arbitrary account.
  + Support of cross-chain RPC with an arbitrary number of arguments.
  + Bridge relay validator subscription mechanism implementation.
  + BFT consensus mechanism implementation.
  + Event emission.
#### 2. Relay validator/prover written in TypeScript. Supplied in a docker container.
#### 3. “Elrond-Minter” smart contract written in Rust deployable on Elrond blockchain.
  + Fungible liquidity freezing (eGold or wrapped Parachain native tokens).
  + Fungible liquidity release to an arbitrary account (wrapped Parachain native tokens or eGold)
  + Non-fungible liquidity freezing.
  + Non-fungible liquidity release to an arbitrary account.
  + Support of cross-chain RPC with an arbitrary number of arguments.
  + Bridge relay validator subscription.
  + BFT consensus mechanism.
  + Event emission.


### Ecosystem Fit

  + Users will be able to transfer their liquidity or NFTs from any Substrate parachain to Elrond and backwards once they attach the XP.network pallet to their parachain or parathread.
  + Substrate parachain smart contracts will be able to call smart contracts functions in Elrond with an arbitrary number of arguments via the bridge relay validators. The same will be possible from the side of Elrond.

### Use-cases

![img](https://github.com/xp-network/w3f_application/blob/main/Use-case.png)

+ Liquidity transfer form an account in a substrate Parachain to Elrond or form Elrond to a Parachain.
+ Selling or purchasing Parachain based NFTs by an account holder in Elrond or vice versa.
+ Cross chain games with RPC connected smart contracts.
+ Lending or borrowing eGold, sovereign Parachain coins or other liquidity.
+ Cross-chain NFT smart contracts with royalties.
+ Cross-chain insurances.

## Team

### Team members

- Stat Oskin - Advisor
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

**Rupansh Sekar**

- Over 1 year of experiences in Blockchain Development 2 years in Rust, 4 years in C


### Team Code Repos

[Rust Documentation](https://xp-network.github.io/poc-documentation/docs/freezer/) Method names, parameters with types, return types and description.
</br>
[PoC repository](https://github.com/xp-network/XP.network-Elrond-Migration)

### Team LinkedIn Profiles

[Stas Oskin](https://www.linkedin.com/in/stasoskin/)
[Dmitry Bryukhanov](https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/)</br>
[Rupansh Sekar](https://www.linkedin.com/in/rupansh-sekar-10941b16a/)


## Development Roadmap

### Overview

- **Total Estimated Duration:** 1 week
- **Total Effort:** 15 days
- **Total Costs:** $ 0.00


### Milestone 0 - Alpha version of CCCB

- **Estimated Duration:** 0 working days (done)
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | Documents containing product architecture as well as basic user manuals  |
| 0c. | Tests | Interactive liquidity transfer tests in local testnets deployed in a docker container  |
| 1. | CCCB architecture | CCCB architecture is developed and documented in textual descriptions and UML diagrams |
| 2. | Smart Contracts in Ink! | Functionality: </br>1. Fungible & Non-fungible token locking & releasing </br>2. RPC functionality </br>3. Relay validators subscription and BFT validation |
| 3. | Semi-decentralized relay validators | Bridge relay validators written in Typescript |
| 4. | Smart Contracts in Rust (on Elrond) | Functionality: </br>1. Fungible & Non-fungible token locking & releasing </br>2. RPC functionality </br>3. Relay validators subscription and BFT validation |



### Milestone 1 — Beta version of CCCB

- **Estimated Duration:** 21 working days (1 month)
- Working days **x** ppl. **:** 7 **x** 3
- Effort: 21 days
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | Documents containing product architecture as well as basic user manuals. Preparing tutorials with examples </br>1. How to attach an XP.network CCCB pallet to a parachain, </br>2. How to use the CCCB to interact with Elrond blockchain | 
| 0d. | Media Announcement | We will publish a media announecement about having finished the betta version of the bridge | 
| 1. | Substrate pallet | Migration of the smart contracts from Milestone 1.2 to a substrate pallet  |
| 2. | Bridge UI | The UI in React allowing codeless cross-chain transactions  |
| 3. | Unit tests | We will write automated unit testing for all of the functionality covering at least 85% of the code and running on Elrond's live testnet|
| 4. | Transaction fees solution | Solving the transaction fee in the “foreign” blockchain problem | 
| 5. | Decentralized Validators | We will make the relay validators completely decentralized, implementing the PoS consensus mechanism. | 
| 6. | Security audit | Testing the system resilience to fraudulent or erroneous validators | 
| 7. | Docker Image | We will provide a Docker image with the CCCB |



## Future Plans

+ Develop a DEX between the coins of the Substrate Parachains to Elrond and back.
+ Add support for Elrond Wallet and other crypto currency wallets.

## Additional Information

[The project repo](https://github.com/xp-network)</br>
The present bridge is built in close cooperation with the Elrond team.</br>

[Official Elrond announcement about this bridge](https://elrond.com/blog/elrond-bridge-polkadot-xpnetwork-nft-dapps/) 
