# Open Grant Proposal

- **Project Name:** XP.network Protocol.
- **Team Name:** XP.network.
- **Payment Address:** BTC: bc1qdpx2e6lejre536ph0csskas888ua3pz5p4pkuj.

## Project Overview

### Overview

XP.network is a codeless platform for building blockchain agnostic NFT DApps. XP.network allows non-developers to build their NFT marketplaces, galleries, museums and many more use-cases without coding. We will connect our users's applications to different blockchains via Polkadot.

In order to synchronize communication between different [parachains](https://research.web3.foundation/en/latest/polkadot/XCMP/index.html) we want to build our own protocol which will be used by an XP.network pallet, that can be attached to and be used by any parachain.

The XP.network protocol allows parachains to communicate in a connectionless but ordered and reliable way. The protocol allows to store the State of a negotiated transaction (TX) and enables the functionality required to inform the User or the original parachain accordingly.

At the moment, XCMP is a work in progress and SPREE only exists in documentation and will only be developed after the parachains become available. With all the advantages of the two above mentioned protocols, neither of them offers the precision XP.network Protocol does.

1. Our protocol allows parachains to mark and store the current state of every transaction in a blob with a unique TopicID. Even though the future implementations of XCMP will allow to create channels between two parachains, TopicIDs will still be useful when multiple transactions are being executed between two parachains simultaneously. Another XCMP feature - ordered delivery - does not provide the same precision as the TopicID does. The TopicID does not rely on the order of messages. It relies on the unique identifier which is the safest pointer. 

2. Once the state of a transaction changes or an error occurs, the requesting parachain gets notified. Some of such messages help keep the end user properly informed about the state of events with his/her transaction, others inform the pallets that the transaction has terminated and the memory must be freed from the blob, which might be expensive to store, especially when it is no longer needed. 

3. Another feature uses the fact that a pallet, implementing our protocol, is a part of its parent parachain. Therefore, it has no additional overhead in tracking whether the transaction succeeded or failed. It listens to events related to the transaction and notifies the requesting parachian of the result and provides the data of the outcome when it becomes available without being additionally requested about this information. 


#### Integration

Any parachain, parathread or bridge can attach our pallet and use its functionality. The pallets will act as a “post office network” supporting the protocol that enables nodes to keep track of the “TopicIDs”. Each "TopicID" represents one negotiated transaction (TX) between two parachains (Lx, Ly).

### Project Details

The project is comprised of 2 interdependent deliverables:

1. The XP.network Protocol, which will enable parachains to communicate keeping track of the current state of every transaction.

2. A Substrate Pallet implementing the XP.network protocol.


**XP Protocol** will be supported by a number of pallets, each acting as a “post office” for its parathread. A typical message will include:
```terminal
{
ID:                 id,               //required to identify that the other blockchain’s reply is related to this request,
To:                 dest,             // indicates the destination parachain / parathread,
Payload:            blob              // A binary representation of the "intention"
}
```

The runtime [storage](https://substrate.dev/rustdocs/v3.0.0/frame_support/storage/trait.StorageValue.html#required-methods) & the message inside the binary payload will be structured as follows:

![img](https://github.com/xp-network/w3f_application/blob/main/xp.network_blob.png)

Even the complete message adds only 64 bits to the original TX binary code. 16 bits for the TopicID, 16 bits for the flags and 32 bits for the length of the TX binary. In order to reduce the overhead:
1. When notifying about errors or about submission of the TX bytecode for execution, only the first 32 bits are attached to the message: 16 bits of the TopicID and 16 bits for flags. They will be used to update the blob, where the state of a TX is stored during the number of seconds when a TX is executed in a target parachain. The extrinsics bytecode is not moved when it is not required.
2. Should, sometime in the future, even such tiny notifications create a noticeable overhead, we will: </br>
	a. join them into batches to reduce the number of messages  </br>
	b. remove the AKN notification, which is required till SPREE becomes available to confirm that the message has been received with the same TX bytecode as the sender's.

Our protocol addresses two of the raised [issues](https://github.com/paritytech/polkadot/issues?q=is%3Aissue+xcm+is%3Aopen)
+ [Adding events for UMP message execution results](https://github.com/paritytech/polkadot/issues/2720)
Instead of broadcasting events spamming the network, we message only the target pallet. Reducing excessive computation in the blockchain.

+ [XCM Retry Queue #2702](https://github.com/paritytech/polkadot/issues/2702) suggests several use-cases, where our protocol can come handy:

1. When a nonsense message or erroneous code is sent, a DER (destination error flag) is raised, and if a parachain produces a more wordy explanation, it would be delivered in the TXData section of the blob. Since such TX should not be retried in the future the END flag will also be raised instructing the sender pallet to remove the transaction state blob from the storage.

2. When an unknown token has been transferred to a parachain and the target parachain failed to process the request, an error flag will be raised. 
An END flag will not be raised, therefore the state blob will not be removed. This will prevent the assets from being irreversibly lost and will leave the opportunity for the developers to add the token and allow the transaction to be successfully processed at a later time.

3. In case there's not enough gas in the target parachain the process will be the same as above in point 2.

XP.network uses XCM protocol as its transport layer and complies with the XCM protocol's four ['A's](https://github.com/paritytech/xcm-format):

1. <i>Asynchronous</i>: our protocol does not block the sender in any way.

2. <i>Absolute</i>: XP.network protocol informs the sending parachain that the message has been delivered by setting the AKN flag and sending back the message with the same TopicID. The order becomes insignificant, due to the fact, that the TopicID is more precise than the order.

3. <i>Asymmetric</i>: The messages do not have a result. The outcome of the message processing is always delivered in a separate message.

4. <i>Agnostic</i>: XP.network protocol makes no assumptions about the Consensus System of either parachains.

The **XP.network Decision Tree**, regulating the efficiency of the data flow between the two pallets, will roughly look like this:

![img](https://github.com/xp-network/w3f_application/blob/main/XP.network%20Protocol-2.png)

The above scheme is a work in progress and subject to change.

Apart from standard setup, a pallet implementing XP.network Protocol consists of:

1. **Message Listener** - it listens to the incoming messages and passes them to the Decision Tree.
2. **Message Deserialiser** - it reads the contents of the binary file and populates the fields of the Message struct.
3. **Message Serialiser** - it packs the values of the Message struct into a binary representation.
4. **Message Sender** - it uses the XCMP to communicate with the other parachains and parathreads using XP Network protocol.
5. **Runtime Storage** - it stores the binaries with the current state of the corresponding transaction. Each blob can be accessed like so: ```sender[TopicID]```.
6. **Decision Tree** - it controls the efficiency of the data flow between the pallets.

#### The XP.network Protocol is not:

This protocol is by no means a bridge between the blockchains. However, we have plans of building bridges to a number of blockchains in further projects.

#### Further development

We may add more flags to reflect more states of the negotiated transaction, for example, we can specify the reasons of rejection in the target blockchain, for example, due to lack of gas.

### Ecosystem Fit

- The XP.network protocol allows parachains to communicate in a connectionless but ordered and reliable way. The protocol allows to store the State of the negotiated TX and enables the functionality required to inform the User accordingly.

- All it takes is to attach the XP.network pallet to a parachain.

## Team

### Team members

- Dmitry Brook - CTO, Project Lead.
- Virbal Kint - Expert in Move, Rust & Co-Founder

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


### Team Code Repos (PoC)
[PoC Documentation](https://xp-network.github.io/poc-documentation/) Method names, parameters with types, return types and description.

[XCMP Pallet example](https://github.com/xp-network/xcmp_pallet-poc/tree/master/xmessage)

[Protocol Blob Serialisation example](https://github.com/xp-network/serde_xp_protocol)


### Team LinkedIn Profiles

[Dmitry Bryukhanov](https://www.linkedin.com/in/dmitry-briukhanov-60b2ab45/)

## Development Roadmap

### Overview

- **Total Estimated Duration:** 1.5 months.
- **Total Effort:** 36 working days.
- **Total Costs:** $ 12,000

### Milestone 1 - PoC of XP.network Protocol

- **Estimated Duration:** 12 working days (2.5 weeks)
- Working days **x** ppl. **:** 6 **x** 2
- Effort: 12 w/days
- **Costs:** $0,00


| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 / GPLv3 / MIT / Unlicense |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can (for example) spin up one of our Substrate nodes and send test transactions, which will show how the new functionality works. |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an **article**/workshop that explains 1. How to use XP.network Protocol, 2. How to attach an XP.Network pallet to a parachain 
| 0f. | PSP | Drafting, Calling for feedback, Acceptance, Integration |
| 1. | XP.network Protocol | XP.network protocol is developed and documented in textual descriptions and UML diagrams |
| 2. | Message serializer | The binary Message serializer is built with SCALE |
| 3. | Message deserializer | The binary Message deserializer is built with SCALE |
| 4. | Runtime Storage integration | Implementing the message blob CRUD functionality in the runtime storage |
| 5. | Testing & debugging | Developing automated tests for the XP.network protocol BLOB serialization / Deserialization, storage, updating, deletion with min 85% code coverage |
| 6. | Testing in Kusama| Testing and debugging in Kusama till 10 example transactions execute and the results are added to the blockchain | 

### Milestone 2 — Alpha version of XP.network Protocol

- **Estimated Duration:** 12 working days (2.5 weeks)
- Working days **x** ppl. **:** 6 **x** 2
- Effort: 12 w/days
- **Costs:** $4,000


| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 / GPLv3 / MIT / Unlicense |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can (for example) spin up one of our Substrate nodes and send test transactions, which will show how the new functionality works. |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an **article**/workshop that explains 1. How to use XP.network Protocol, 2. How to attach an XP.Network pallet to a parachain 
| 0f. | PSP | Drafting, Calling for feedback, Acceptance, Integration |
| 1. | Message Listener | We will develop a module listening to the messages from the XCMP |
| 2. | Message Sender | We will develop a module sending the messages via XCMP |
| 3. | XCMP Integration | We will establish communication between two pallets implementing the XP.network Protocol in Kusama |
| 4. | Testing & debugging | Developing automated tests for the XP.network protocol XCMP message processing between two pallets  with min 85% code coverage |
| 5. | Testing in Kusama| Testing and debugging in Kusama till 10 example transactions execute and the results are added to the blockchain | 


### Milestone 3 — Production version of XP.network Protocol

- **Estimated Duration:** 12 working days (2.5 weeks)
- Working days **x** ppl. **:** 6 **x** 2
- Effort: 12 w/days
- **Costs:** $8,000


| Number | Deliverable | Specification |
| ------------- | ------------- | ------------- |
| 0a. | License | Apache 2.0 / GPLv3 / MIT / Unlicense |
| 0b. | Documentation | We will provide both **inline documentation** of the code and a basic **tutorial** that explains how a user can (for example) spin up one of our Substrate nodes and send test transactions, which will show how the new functionality works. |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Docker | We will provide a Dockerfile(s) that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an **article**/workshop that explains 1. How to use XP.network Protocol, 2. How to attach an XP.Network pallet to a parachain 
| 0f. | PSP | Drafting, Calling for feedback, Acceptance, Integration |
| 1. | Decision Tree | We will develop the efficient data flow controllers and finite state machine logics |
| 2. | XCMP Integration | We will establish communication between two pallets implementing the XP.network Protocol in Kusama |
| 3. | Testing & debugging | Developing automated tests for the XP.network protocol interactions between pallets with min 85% code coverage |
| 4. | Testing in Kusama| Testing and debugging in Kusama till 20 example transactions execute and the results are added to the blockchain | 


## Future Plans

**Community Plan**

Hiring 3-5 more developers

Joining Polkadot related events

Publishing articles in Telegram, medium.com and other channels

**Development Plan**

When the new version of XCMP is developed we will migrate our protocol there. Once SPREE becomes available we will move our protocol there, since it will provide more security in its own storage, that a parachain cannot forge or alter.

## Additional Information 

[The project repo:](https://github.com/xp-network)

