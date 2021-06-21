# Open Grant Proposal

- **Project Name:** XP.network Substrate Parachain - Elrond Cross-Chain Communication Bridge (CCCB)
- **Team Name:** XP.network
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj

## Project Overview

### Overview

XP.network is a blockchain-agnostic platform for building NFT DApps. With XP.network, even non-developers can create NFT marketplaces, galleries, lending apps, and more - without a single line of code and on any blockchain, thanks to the interoperability features of Polkadot.

In this project, we aim to bring Elrond’s users and ecosystem to Polkadot.

### CCCB UI

The bridge will be accompanied with intuitive UI:

![img](https://github.com/xp-network/w3f_application/blob/main/Cross%20Chain%20Bridge-1.png)

### Ecosystem Fit

  + Users will be able to transfer their liquidity or NFTs from any Substrate parachain to Elrond and backwards once they attach the XP.network pallet to their parachain or parathread.
  + Substrate parachain smart contracts will be able to call smart contracts functions in Elrond with an arbitrary number of arguments via the bridge relay validators. The same will be possible from the side of Elrond.

### Project Details

The proposed Cross-Chain Communication Bridge(CCCB) will link a Substrate Parachain and Elrond which are two non-identical unsynchronized blockchain, allowing liquidity transfers.

#### CCCB Structure:
#### 1. A Substrate pallet implementing the following functionality:
  + Fungible liquidity freezing </br>
    Sovereign parachain tokens as well wrapped eGold are locked in the pallet to avoid duplication during the transfer.
  + Fungible liquidity release </br>
    A transfered number of wrapped eGold or Parachain native tokens can be released to an arbitrary account in the target parachain.
  + Non-fungible liquidity freezing </br>
    NFTs are locked in the pallet to avoid duplication.
  + Non-fungible liquidity release to an arbitrary account </br>
    Wrapped NFTs alongside with their data can be released to a designated account in the target parachain.
  + Support of cross-chain RPC with an arbitrary number of arguments </br>
    A remote procedure call can be executed via the pallet. The call will contain the following parameters:
    ```rust
    {
      sc: string,                       // the address of the target smart contract
      func: string,                     // target function name
      args: Vec<Vec<uint8>>             // An arbitrary number of arguments of arbitrary types
    }
    ```
  + Bridge relay validator subscription mechanism implementation </br>
    This mechanism allows to dynamically add new validators in a decentralized way after the system launch.
  + BFT consensus mechanism implementation </br>
    A blockchain embedded smart contract checks whether 2/3 * n + 1 validator have signed the transaction, where **n** is the total number of validators.
  + Event emission </br>
    To signal the validators that one of the bridge related events has occured the pallet emits events with the accompanying data.
#### 2. Relay validator/prover written in TypeScript. Supplied in a docker container.
  Relay validators are very thin. They consist of the private and public keya and two local nodes one for listening/submitting to Elrond another for listening/submitting to a parachain with the attahced bridge pallet. 
#### 3. “Elrond-Minter” smart contract written in Rust deployable on Elrond blockchain.
  + Fungible liquidity freezing </br>
    eGold or wrapped Parachain native tokens are locked to avoid duplication
  + Fungible liquidity release to an arbitrary account </br>
    Wrapped Parachain native tokens or eGold are released to an arbitrary target address in Elrond.
  + Non-fungible liquidity freezing </br>
    NFTs are locked in the smart contract to avoid duplication.
  + Non-fungible liquidity release to an arbitrary account </br>
  + Support of cross-chain RPC with an arbitrary number of arguments </br>
  A remote procedure call can be executed via the pallet. The call will contain the following parameters:
    ```rust
    {
      sc: string,                       // the address of the target smart contract
      func: string,                     // target function name
      args: Vec<Vec<uint8>>             // An arbitrary number of arguments of arbitrary types
    }
    ```
  + Bridge relay validator subscription </br>
    This mechanism allows to dynamically add new validators in a decentralized way after the system launch.
  + BFT consensus mechanism </br>
    A blockchain embedded smart contract checks whether 2/3 * n + 1 validator have signed the transaction, where **n** is the total number of validators.
  + Event emission </br>
    To signal the validators that one of the bridge related events has occured the smart contract emits events with the accompanying data.

### Use-cases

![img](https://github.com/xp-network/w3f_application/blob/main/Use-case.png)

+ Liquidity transfer from an account in a substrate Parachain to Elrond or form Elrond to a Parachain.
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

**Stas Oskin**

- Over 18 years of experience in Development and Management
- Co-founder in Pontem
- Former Biz-Dev in dfinance
- Former Core Dev and Biz Dev in WINGS Stiftung
- Former CTO in Eyecam
- Former CTO in NeoCam
- Former CTO in NeoVSP
- Former Co-Founder in Wordberry
- Former CTO in BeeComm

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
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can use the Alpha version of the bridge and its structure |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 1. | CCCB architecture | CCCB architecture is developed and documented in textual descriptions and UML diagrams |
| 2. | Smart Contracts in Ink! | Functionality: </br>1. Fungible & Non-fungible token locking & releasing </br>2. RPC functionality </br>3. Relay validators subscription and BFT validation |
| 3. | Semi-decentralized relay validators | 21 Bridge relay validators written in Typescript and ready for deployment on a remoute server |
| 4. | Smart Contracts in Rust (on Elrond) | Functionality: </br>1. Fungible token locking & releasing </br>2. Limited RPC functionality ```args:Vec<uint8>```</br>3. Relay validators subscription and BFT validation |



### Milestone 1 — Beta version of CCCB

- **Estimated Duration:** 21 working days (1 month)
- Working days **x** ppl. **:** 7 **x** 3
- Effort: 21 days
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains </br>1. How to attach an XP.network CCCB pallet to a parachain, </br>2. How to use the CCCB to interact with Elrond blockchain |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 0e. | Media Announcement | We will publish a media announecement about having finished the betta version of the bridge | 
| 1. | Substrate pallet | Migration of the smart contracts from Milestone 0.2 to a substrate pallet, eliminating the RPC function argumant limitation  |
| 2. | NFT support | We will add NFT transfer functionality from a parachain to Elrond or vice versa |
| 3. | Bridge UI | The UI in React allowing codeless cross-chain transactions  |
| 4. | Unit tests | We will write automated unit testing for all of the functionality covering at least 85% of the code and running on Elrond's live testnet|
| 5. | Transaction fees solution | Solving the transaction fee in the “foreign” blockchain problem | 
| 6. | Decentralized Validators | We will make the relay validators completely decentralized, implementing the PoA consensus mechanism, where alongside with NPoS reputation is taken into account when selecting the validators. | 
| 7. | Security audit | Testing the system resilience to fraudulent or erroneous validators | 



## Future Plans

+ Develop a DEX between the coins of the Substrate Parachains to Elrond and back.
+ Add support for Elrond Wallet and other crypto currency wallets.

## Additional Information

[The project repo](https://github.com/xp-network)</br>
The present bridge is built in close cooperation with the Elrond team.</br>

[Official Elrond announcement about this bridge](https://elrond.com/blog/elrond-bridge-polkadot-xpnetwork-nft-dapps/) 
