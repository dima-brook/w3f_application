# Open Grant Proposal

- **Project Name:** Substrate Parachain - Elrond Cross-chain Bridge (SPEC-B)
- **Team Name:** XP.network
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj

## Project Overview

### Overview

XP.network is a blockchain-agnostic platform for building NFT DApps. With XP.network, even non-developers can create NFT marketplaces, galleries, lending apps, and more - without a single line of code and on any blockchain, thanks to the interoperability features of Polkadot.

In this project, we aim to bring Elrond users and the ecosystem to Polkadot.

### SPEC-B UI

The bridge is accompanied by intuitive UI:

![img](https://github.com/xp-network/w3f_application/blob/main/Cross%20Chain%20Bridge-1.png)


### Project Details

The proposed Substrate Parachain - Elrond Cross-chain Bridge (SPEC-B) will link the two non-identical unsynchronized blockchains, allowing liquidity transfers.

### Use-cases

![img](https://github.com/xp-network/w3f_application/blob/main/XP-Elrond%20bridge-1.png)

+ Liquidity transfer from an account in a substrate Parachain to Elrond or from Elrond to a Parachain.
+ Selling or purchasing Parachain based NFTs by an account holder in Elrond or vice versa.
+ Cross-chain games with RPC-connected smart contracts.
+ Lending or borrowing eGold, sovereign Parachain coins, or other liquidity.
+ Cross-chain NFT smart contracts with royalties.
+ Cross-chain insurances.

#### SPEC-B Structure:
#### 1. A Substrate pallet implementing the following functionality:
  + Fungible token transfer
    ```rust
    /// Transfer to elrond chain event
    /// validators must subscribe to this
    pub struct Transfer {
        action_id: u128,
        to: String,
        value: Balance
    }
    ```
  + Fungible liquidity freezing </br>
    Sovereign parachain tokens as well as wrapped eGold are locked in the pallet to avoid duplication during the transfer.
    ```rust
    ///TODO: implement in Milestone 1
    ```
  + Fungible liquidity release </br>
    A transferred number of wrapped eGold or Parachain native tokens can be released to an arbitrary account in the target parachain.
    ```rust
        /// unfreeze tokens and send them to an address
        /// only validators can call this
        pub fn pop(&mut self, action_id: String, to: AccountId, value: Balance) {
            self.verify_action(action_id, Action::Unfreeze { to, value })
        }
  
  + Non-fungible liquidity freezing </br>
    NFTs are locked in the pallet to avoid duplication.
    ```rust
    ///TODO: implement in Milestone 1
    ```
  + Non-fungible liquidity release to an arbitrary account </br>
    Wrapped NFTs alongside their data can be released to a designated account in the target parachain.
    ```rust
    ///TODO: implement in Milestone 1
    ```
  + Support of cross-chain RPC with an arbitrary number of arguments </br>
    A remote procedure call can be executed via the pallet. The call will contain the following parameters:
    ```rust
      pub struct ScCall {
        action_id:  u128,         // Unique action identifier
        to:         String,       // smart contract address in a parachain
        endpoint:   String,       // smart contract function name
        args:       Vec<Vec<u8>>  // Target smart contract function arguments
    }
    ```
  + Bridge relay validator subscription mechanism implementation </br>
    This mechanism allows dynamically to add new validators in a decentralized way after the system launch.
    ```rust
        /// Subscribe to events & become a validator
        pub fn subscribe(&mut self) {
            ...
        }
    ```
  + BFT consensus mechanism implementation </br>
    A blockchain embedded smart contract checks whether 2/3 * n + 1 validators have signed the transaction, where **n** is the total number of validators.
    ```rust
    if validated == (2*validator_cnt/3)+1 {
                self.exec_action(act);
            }
    ```
  + Event emission </br>
    To signal the validators that one of the bridge-related events has occurred the pallet emits events with the accompanying data.
  ```rust
	/// Emit an SCCall event
	/// TODO: Replace the Ink! implementation with Rust in Milestone 1
	pub fn send_sc_call(&mut self, target_contract: String, endpoint: String, args: Vec<Vec<u8>>) {
	    bech32::decode(&target_contract).expect("Invalid address!");
	    ...
	    self.env().emit_event( ScCall {
		action_id: self.last_action,
		to: target_contract,
		endpoint,
		args
	    } )
	}
  ```
 + Runtime Storage defined by our module:</br>
 
	Locally scoped storage, accessible only from within the pallet will contain the following data:
 ```rust
// Subject to implementation change:
HashMap<AccountId, ()>,		// The set of the validators
HashMap<String, ActionInfo>,	// The hash map of the actions
last_action: u128
 ```
 
#### 2. Relay validator/prover written in TypeScript. Supplied in a docker container.
  Relay validators are very thin. They consist of the private and public keys and two local nodes one for listening/submitting to Elrond another for listening/submitting to a parachain with the attached bridge pallet. 
  
  In the Alpha version of the bridge the validators are centralized, meaning our team completely controls all the validators.
  
  Since version B of the bridge we will implement a decentralized setting.

  Since both Substrate parachain and Elrond finalize blocks every 6 seconds we cannot use the PoW model, which, alongside unjustifiably heavy computation, requires significant time. Hence, we're left with the other models, such as:

**Proof-of-Stake (PoS)**
Principle: the network trusts the validator, who puts his own resources as a pledge for the ability to create blocks: the larger the share, the higher the probability that the network will allow the creation of a block.

Performance: high.

DLT environment: public / private blockchain.

Completion: probabilistic.

Med = TX<sub>fee</sub> / Val<sub>num</sub>

Minimum amount: XPNET worth to be locked as a stake equals the annual Smin = 5 * 365 * e / d, where:</br>
**Smin** is the minimum required stake</br>
**e** is the earned amount since the beginning</br>
**d** is the number of epochs when the earnings were made since the beginning, but less or equal than 365</br>
For example, a user has become a validator 35 epochs ago and has earned 1589 XPNET since then.
The minimum stake for such a user will be: Smin = 5 * 365 * 1589 / 35 = 82855 XPNET.


The grace period for locking the minimum amount for a validator will be 7 epochs since the notification.

PoS Distribution in code:
![img](https://github.com/xp-network/w3f_application/blob/main/PoS%20structure.png)


**Proof-of-Importance (PoI)**
Principle: like PoS, but with additional properties that affect your ranking.

Performance: high.

DLT environment: public.

Completion: probabilistic.

... TODO: add the architecture here (latest 23.06)


Even though, there other protocols, such as Proof-of-Location (PoL), Proof-of-Elapsed Time (PoET), Proof-of-Authority (PoA), Proof-of-Burn (PoB), Proof-of-Capacity (PoC) / Proof-of-Space (PoS), Proof-of-Stake-Time (PoST), Proof-of-Brain (PoB), Proof-ofPhysical-Address (PoPA) / Proof-of-Bank-Account (PoBA), Proof-of-Concept (PoC) and some more, we consider PoS to be more mature and easy to implement. However, in the future we will add reputation and ranking to PoS to make the system more robust and resilient to attacks.

  
#### 3. “Elrond-Minter” smart contract written in Rust deployable on Elrond blockchain.
  + Transfer Liquidity
  ```rust
  Action::SendXP { to, amount, data } => {
	let token = self.token().get();
	self.send().esdt_local_mint(&token, &amount);
	Ok(PerformActionResult::SendXP(SendToken {
		api: self.send(),
		to,
		token: token.into(),
		amount,
		data
	}))
}
  ```
  + Fungible liquidity freezing </br>
    eGold or wrapped Parachain native tokens are locked to avoid duplication
  + Fungible liquidity release to an arbitrary account </br>
    Wrapped Parachain native tokens or eGold are released to an arbitrary target address in Elrond.
    ```rust
	#[payable("*")]
	#[endpoint(withdraw)]
	fn withdraw(&self, #[payment] value: Self::BigUint, #[payment_token] token: TokenIdentifier, to: String)  -> SCResult<Self::BigUint> {
		require!(value > 0, "Value must be > 0");
		require!(token == self.token().get(), "Invalid token!");

		self.send().esdt_local_burn(&token, &value);

		let ident = self.event_ident().update(|event| { 
			event.add_assign(Self::BigUint::from(1u64));
			event.clone()
		});
		self.event_mapper().insert(ident.clone(), EventInfo::new(Event::Unfreeze { to, value }));

		Ok(ident)
	}
    ```
  + Non-fungible liquidity freezing </br>
    NFTs are locked in the smart contract to avoid duplication.
    ```rust
    ///TODO: implement in Milestone 1
    ```
  + Non-fungible liquidity release to an arbitrary account </br>
  + ```rust
    ///TODO: implement in Milestone 1
    ```
  + Support of cross-chain RPC with an arbitrary number of arguments </br>
  A remote procedure call, made from a Substrate pallet, will be executed by a smart contract deployed on Elrond. The call will contain the following parameters:
    ```rust
    Action::SCCall {
		to,
		amount,
		endpoint,
		args
	} => {
		let mut contract_call_raw =
			ContractCall::<Self::SendApi, ()>::new(self.send(), to, endpoint)
				.with_token_transfer(TokenIdentifier::egld(), amount);
		for arg in args {
			contract_call_raw.push_argument_raw_bytes(arg.as_slice());
		}
		Ok(PerformActionResult::AsyncCall(
			contract_call_raw.async_call(),
		))
	}
    ```
  + Bridge relay validator subscription </br>
    This mechanism allows dynamically to add new validators in a decentralized way after the system launch.
    ```rust
	  /// Initiates board member addition process.
	  /// Can also be used to promote a proposer to board member.
	  #[endpoint(proposeAddValidator)]
	  fn propose_add_validator(&self, uuid: Self::BigUint, board_member_address: Address) -> SCResult<PerformActionResult<Self::SendApi>> {
		  self.validate_action(uuid, Action::AddValidator(board_member_address))
	  }
    ```
  + BFT consensus mechanism </br>
    A blockchain embedded smart contract waits for the BFT threshold to execute a required transaction.
    ```rust
            if validated == (2*validator_cnt/3)+1 {
                self.exec_action(act);
            }
    ```
  + Event emission </br>
    To signal the validators that one of the bridge-related events has occurred the smart contract emits events with the accompanying data.
    ```rust
    /// Event emission will be implemented by Elrond in ~ 2 months
    ```
### Ecosystem Fit

  + Substrate parachain users will be able to trade, exchange or transfer their tokens to and from Elrond with minimal effort and transaction fees. It will open a new market for the Polkadot community and will make the tokens of both the systems more liquid.
  + Substrate parachain smart contracts will be able to call smart contracts functions in Elrond with an arbitrary number of arguments via the bridge relay validators. The same will be possible from the side of Elrond.
  + Polkadot already has bridges to [Bitcoin](https://beta.polkabtc.io/), [Ethereum](https://snowbridge.snowfork.com/), Cosmos, [EOS](https://github.com/bifrost-finance/bifrost), [Ethereum Classic](https://github.com/ChainSafe/ChainBridge), etc., but there's no bridge to Elrond. This project will fill the gap.


## Team

### Team members

- Dmitry Brook - CTO, Project Lead
- Rupansh Sekar - Expert in C, Rust, and blockchain development
- Verbal Kint - Expert in Move, Rust & Co-Founder

### Contact

- **Contact Name:** Dmitry Briukhanov

- **Contact Email:** dima@xp.network

- [XP.network website](https://xp.network/)

### Advisors

- [Stas Oskin](https://www.linkedin.com/in/stasoskin/)</br>

### Legal Structure

- **Registered Address:** HaHagana, 15, Or Yehuda, Israel
- **Registered Legal Entity:** XP Network


### Team Experience

**Dmitry Briukhanov**

- Over 18 years of experience in Development and Management 
- Former Senior Software Developer in Best Systems, Israel
- Former Team Lead in NLK, Russia


**Rupansh Sekar**

- Over 1 year of experiences in Blockchain Development 2 years in Rust, 4 years in C


**Verbal Kint**

- Over 5 years of experiences in Blockchain Development 8 years in Rust, 1 year in Move, and 15 years of development experience overall



### Team Code Repos

[Team repository](https://github.com/xp-network/)</br>
[Project Documents](https://xp-network.github.io/poc-documentation/) Method names, parameters with types, return types, and description.
</br>
[PoC repository](https://github.com/xp-network/XP.network-Elrond-Migration)</br>

### GitHub Repos

[Dima Brook](https://github.com/DimaBryuhanov)</br>
[Rupansh Sekar](https://github.com/rupansh)</br>
[Verbal Kint](https://github.com/VKint)</br>


### Team LinkedIn Profiles

[Dmitry Bryukhanov](https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/)</br>
[Rupansh Sekar](https://www.linkedin.com/in/rupansh-sekar-10941b16a/)


## Development Roadmap

### Overview

- **Total Estimated Duration:** 1 month
- **Total Effort:** 52 working days
- **Total Costs:** $ 10,000.00


### Milestone 0 - Pre-Alpha version of SPEC-B

- **Estimated Duration:** 27 working days (done)
- **Costs:** $0.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can use the Alpha version of the bridge, detailed documentation in a GitBook |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 1. | SPEC-B architecture | SPEC-B architecture is developed and documented in textual descriptions and UML diagrams |
| 2. | Smart Contracts in Ink! | Functionality: </br>1. Fungible token locking & releasing </br>2. Limited RPC functionality ```args: Vec<uint8>```</br>3. Relay validators subscription and BFT validation |
| 3. | Centralized relay validators | 21 Bridge relay validators written in Typescript and ready for deployment on a remote server |
| 4. | Smart Contracts in Rust (on Elrond) | Functionality: </br>1. Fungible token locking & releasing </br>2. RPC functionality ```args:Vec<Vec<uint8>>```</br>3. Relay validators subscription and BFT validation |



### Milestone 1 — Alpha version of SPEC-B

- **Estimated Duration:** 1 month
- **FTE** 1-2
- Effort: 32 days
- **Costs:** $5,000.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains </br>1. How to attach an XP.network SPEC-B pallet to a parachain, </br>2. How to use the SPEC-B to interact with Elrond blockchain, detailed documentation in a GitBook |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 1. | Substrate pallet | Migration of the smart contracts from Milestone 0.2 to a substrate pallet, eliminating the RPC function arguments limitation  |
| 2. | NFT support | We will add NFT transfer functionality from a parachain to Elrond or vice versa |
| 3. | Bridge UI | The UI in React allowing codeless cross-chain transactions  |
| 4. | Unit tests | We will write automated unit testing for all of the functionality covering at least 85% of the code and running on Elrond's live testnet|
| 5. | Transaction fees solution | Solving the transaction fee in the “foreign” blockchain problem | 
| 6. | PoA consensus implementation | We will implement the PoA consensus mechanism and will address a number of professional validators such as [Stakefish](https://stake.fish/en/),  [AUDITone](https://audit.one/), [Ankr](https://www.ankr.com/), [Anonstake](https://anonstake.com/), [Blockdaemon](https://blockdaemon.com/about/), [Chorus](https://chorus.one/), [Chainflow](https://chainflow.io/), [Delight Labs](https://github.com/DELIGHT-LABS), [Dokia Capital](https://dokia.capital/), [#Hashed](https://www.hashed.com/), [HashQuark](https://www.hashquark.io/#/), [Figment](https://figment.io/), [Stakin](https://stakin.com/home), [StakeWith.us](https://www.stakewith.us/), [Staked](https://staked.us/), [WolfEdge Capital](https://www.wolfedge.capital/) and several others with a request to join as validators.| 
| 7. | Security & efficiency audit | Testing the system resilience to fraudulent or erroneous validators and making architectural decisions about the optimal numbers of relay validators, stakes, transaction speed, and transaction fees |
| 8. | Using a parachain as a bridge | We will implement the functionality which will allow to use a parahcian equipped with our bridge to be used by another parachain connected via Polkadot and will make it operational in ROCCOCO, Kusama and the Polkadot mainnet |




### Milestone 2 — Beta version of SPEC-B

- **Estimated Duration:** 1 month
- **FTE** 1-2
- Effort: 32 days
- **Costs:** $5,000.00

| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains </br>1. How to attach an XP.network SPEC-B pallet to a parachain, </br>2. How to use the SPEC-B to interact with Elrond blockchain, detailed documentation in a GitBook |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 1. | Testing under load | Testing the bridge with increasing load from 10 to 100 transactions per block |
| 2. | Top 100 tokens support | We will add the top 100 token support out of the box on both sides of the bridge |
| 3. | Addin yet unsupported tokens | We will add the automated new token support functionality |
| 4. | Unbonding the validators | We will implement the architecture for the relay validators unbonding or chilling |
| 5. | Transaction Fee Calculator | We will implement a transaction fee calculator with the UI showing how much a transaction will cost on both sides Parachain & Elrond |
| 6. | Elrond wallet integration | We will integrate the Elrond Wallet in our bridge |
| 7. | PoS consensus availability | We will add the option of PoS consensus mechanizm for the bridge relay validators. |
| 8. | Automation of the consensus mechanizm change | We will automate the relay validators' consensus mechanizm change PoA <-> PoS. |

## Future Plans

+ We will build similar bridges to HECO, Solano etc.
+ We will keep the same validators to make it financially attractive for the validators which will keep the bridge secure and decentralized.

## Additional Information
, 
[The project repo](https://github.com/xp-network)</br>
The present bridge is built in close cooperation with the Elrond team.</br>

[Official Elrond announcement about this bridge](https://elrond.com/blog/elrond-bridge-polkadot-xpnetwork-nft-dapps/) 
