---
layout: page
title: DePOR System architecture
permalink: /depor/architecture/
---


## Introduction to DePOR system architecture

### 1.DePOR in brief
Like Bitcoin and Ethereum , DePOR hopes to be just a network protocol at the beginning and the main public network running this protocol. DePOR tends to be a free and open project, the agreement is made on a Creative Commons license, and the code is hosted under the FLOSS license. This project is developed in an open state and receives useful donations from various quarters. A micro-comment submission system ( RFCs ), but unlike the Python improvement agenda, will provide a way for the public to collaborate and participate in protocol modifications and upgrades.

Our initial implementation of the DePOR protocol will be called the Parity DePOR Platform , which will include the complete implementation of the protocol and API interfaces. Like other Parity 's blockchain implementations, PDP will be designed as a general-purpose blockchain technology stack, not limited to public networks, private networks, or alliance networks.

However, this paper is still in the context of the public network. The functions we foresee under the public network are a subset of a complete concept (such as a private or alliance network). In addition, in this scenario, all aspects of DePOR can be clearly described and discussed. This also means that readers need to know that in non-public (authorized) scenarios, some specific mechanisms (such as interaction with other public networks) are not directly related to DePOR.

DePOR is a scalable heterogeneous multi-chain system. This means that unlike previous single blockchain implementations that focused on different levels of potential application functions, DePOR itself was designed to not provide any inherent functional applications. DePOR provides a relay-chain , on which a large number of verifiable, globally dependent dynamic data structures can exist. We call these parallel structured blockchains parachains , although they are not required to be a chain.

In other words, DePOR will be designed as a collection of independent chains (for example, including ETH , ETC , IPFS , BTC ), except for two very important points:

- Merged security

- Trustless cross-chain transaction

These two points are also the reason why we call DePOR scalable. In principle, a problem is completely solved on DePOR : it can be expanded outwards and there will be a very large number of parachains . Although each parachain is managed in parallel through different network modes in all aspects, this system has scalability capabilities.

DePOR provides an architecture as simple as possible, putting most of the complexity on middleware. This is a deliberate decision, in order to try to reduce the risk of development, so that the necessary software can be developed in a short time, but also have confidence in security and robustness.

### 2.DePOR participants
There are four basic roles in maintaining the DePOR network: Collector , blocker , offer , and cheker . In DePOR a possible implementation, the last role is likely to be split into two: Basic cheker and available guarantor guarantor.

**![img 4-1 here]**

#### 2.1 Collator
Collector is a group that helps cheker create effective parachain blocks. They will run a full node of a particular parachain, which also means that they have all the necessary information to package new blocks and execute transactions, just like miners in the current PoW blockchain. Under normal circumstances, they will collect and execute transactions, create an "unsealed" block, and submit it together with a zero-knowledge proof to one or more currently responsible for proposing the parachain area cheker of the block .

About Collector, Nominator,cheker precise relationship may also be modified. At first, we hope that Collector and cheker can work closely together, because there may be only some (or even one) parachains with very small transactions. The initial client implementation will include an RPC interface to support a Parachain Collector node to unconditionally provide a valid parachain block to a (relay chain) cheker node. As the cost of maintaining all fully synchronized parachains is getting higher and higher, we have designed additional structures to help separate independent, economically driven, and other participants.

In the end, we hope to see the Collector group competitively collect information for more handling fees. For a period of time, in order to continue to increase share revenue rewards, these Collectors may only serve a specific cheker group. Or the freelance Collector can simply create a market that provides effective parachain blocks instead of getting competitive share rewards that are paid immediately. Similarly, the decentralized offer group will also allow multiple secured participants to coordinate and share the responsibilities of the cheker . This ability ensures the openness of participation and contributes to a more decentralized system.

#### 2.2 Nominator
Nominator is an equity group, they entrust the security deposit to the cheker . They have no more roles, except to express by investing capital risky: they trust a particular cheker (or group) to maintain the entire network on their behalf. According to their deposit ratio, they will also receive rewards and deductions in the same proportion as the total deposit of cheker .

Like the Collector below , offer is similar to the current miners of the PoW network.

#### 2.3 Validator
Cheker has the highest authority to help package new blocks in the DePOR network. cheker needs to mortgage enough deposit, because we allow other offers with funds to recommend one or more chekers that can represent them , so part of the deposit of cheker is not owned by them, but belongs to the offer .

A cheker must run a relay chain client on a highly available and high-bandwidth machine. On each block, the node must be prepared to receive a new block on the submitted parachain. This process involves accepting, verifying, and republishing candidate blocks. The appointment of cheker is deterministic, but it is actually difficult to predict. Because chekers cannot be expected to have fully synchronized data for all parachains, they hope to assign the work of proposing a new block of parachains to a third party, which is Collector .

Once the different cheker groups have definitively approved the new blocks of their own parachain, they must begin to approve the blocks of the relay chain itself. This includes updating the status of the transaction queue (that is, transferring from the queue of one parachain to the queue of another parachain), processing the transaction set of approved relay chains, approving the final block, and absorbing the final parachain. change.

Under the consensus algorithm we choose, we will punish a cheker for not fulfilling their duties . In the beginning, if it is not an intentional error, their reward will be withheld, but if it is a repeated error, their deposit will be deducted (by burning), such as double-signing or colluding to provide an illegal block. The malicious behavior of the proof will cause them to lose all their deposits (burn a small part, and most of them will be rewarded to the information provider and the honest cheker ).

To a certain extent, cheker is similar to the current PoW blockchain mining pool.

#### 2.4 Blocker
Unlike the other two parties, blocker is not directly related to the block packaging process. They are independent "bounty hunters", and what motivates them is a one-time large reward.

To be precise, because of the existence of blocker , we can reduce the occurrence of malicious behavior. Even if the hope occurs, it is only because the private key is accidentally leaked, rather than deliberate malicious attempts. The starting point for this name is to consider the frequency of their expected benefits and the size of the final reward.

As long as blocker promptly reports and proves that at least one secured party has illegal activities, they will be rewarded. Illegal actions include signing two different blocks with the same parent block, or approving an invalid block on the parachain. In order to prevent the transition reward caused by the disclosure of the private key to blocker , the basic reward that blocker reports about the illegal message signature of a single cheker starts from the smallest. This reward will gradually increase as other blockers report more illegal signatures. According to our basic security assumptions: at least two-thirds of chekers are honest, and the asymptote will be set at 66%.

blocker is similar to the full nodes of the current blockchain system to some extent. They require relatively few resources, and there is no need to promise stable online time and large bandwidth. blocker is so different that they only need to submit a small deposit. This deposit is used to prevent sybil attacks that waste cheker computing time and computing resources. It can be withdrawn immediately, probably not more than the equivalent of a few dollars, but if an improperly-behaving cheker is detected, a great reward may be obtained.

### 3.Design summary
#### 3.1 Consensus
On the relay chain, DePOR uses a modern asynchronous Byzantine Fault Tolerance (BFT) algorithm to reach a mutual consensus on valid blocks. The algorithm is inspired by the simple Tendermint and HoneyBadgerBFT. The latter can provide an efficient fault-tolerant algorithm as long as most chekers are honest under an architecture with arbitrary network defects .

Maybe a PoA network is sufficient, but DePOR is a network that can be deployed in a fully open and open scenario, and does not need to trust any special organization and authority to maintain it, so we need a kind of management cheker groups and motivate them to follow the law. We choose to use a consensus algorithm based on PoS.

#### 3.2 Related proof of stake
The full name of RPoS is Related Proof of Staking, and DePOR uses RPoS as its consensus mechanism. The number of verification nodes is public and limited, and the number is determined by community governance. Whether it can become a verification node is determined by the amount of pledged by the creator and the pledged by the supporters. In the RPoS mechanism, each elected cheker has the same say in the consensus.

**3.2.1 Participating role**
- Creator: Creator who wants to run for verification node

- Supporter : There is a relationship within 6 intervals with the offer

**3.2.2 Verification rules**

The chekers are initiated by the offers , and the initial stage is limited to 108, and the number of chekers will be gradually expanded in the later stage based on community votes. Each cheker must pledge more than 72000 DPO: the creator pledges 36000 DPO, the supporter pledges 36000 DPO in total, and each supporter pledges a fixed amount of 1000 DPO.

It should be noted that the creator must activate the 6DOS network to initiate the creation. When other conditions are met, the cheker can be activated to obtain DePOR mining revenue.

The basic monthly rate of return of RPoS is 4.8%, that is, the annualized rate of return is 57.6%. We believe that this rate of return is a high level of incentives in all main chain mining. In addition, for every additional supporter, the annual interest rate increases by 0.12%.

#### 3.3 Parachain and Collector

Each parachain will provide the same security guarantee to the relay chain: the block header of the parachain will be included in the relay chain block, and some confirmation information will be followed to ensure that no chain reconfiguration or Double-spending. Similar to the security guarantees of Bitcoin side chains and joint mining, DePOR also strongly guarantees the validity of parachain state transactions. According to cryptographic algorithms, chekers are randomly divided into many groups. A parachain corresponds to one group, and even the group of each block may be different. This setting means that the relay chain should be at least as short as the block generation time of the parachain. This article does not discuss the specific decision method for grouping. It may be around a commit-reveal framework similar to RanDAO, or combined with the cryptographic hash value of the previous block of the parachain.

Such a cheker group needs to provide the candidate blocks of the parachain and ensure that they are valid (otherwise the deposit will be lost). Validity revolves around two important points: First, it is endogenously valid, and all state transitions are executed fairly, including the external data cited (such as transactions). Second, the participants need to be able to easily access any external data of the candidate block, such as external transactions, etc., and then download these data and manually execute the candidate block. chekers can submit empty blocks (null) that do not contain any external transaction data. If they do, they will bear the risk of reducing rewards. They and Collector work on a gossip protocol of the parachain. Collector collects transactions into blocks and provides a non-interactive zero-knowledge proof (noninteractive zero-knowledge) to prove that the parent block of the child block is Effective (charge any handling fees for the job).

The method of preventing spam data is left to the parachain protocol itself: the relay chain essentially does not stipulate "computing resource measurement" and "transaction fee". In essence, it is not mandatory for parachains to stipulate related agreements (although equity holders are unlikely to be willing to accept a parachain that does not provide a reasonable mechanism). It is clearly stated that it will not all resemble the fee rules of Ethereum, but also the blockchain fee model of Bitcoin, or any other garbage prevention model that has not been proposed.

DePOR's relay chain itself will likely have an account and state model similar to Ethereum, which may be a derivative version of EVM. Because the relay chain nodes will need to do a lot of other calculations, and will minimize transaction throughput by increasing the handling fee, our model will also include block size restrictions.

#### 3.4 Cross-chain communication
The most critical part of DePOR is cross-chain communication , which is a necessary prerequisite for building a super-chain complex. Because there can be some kind of information channel between parachains , we say that DePOR is a scalable multi-chain system. In DePOR , communication can be very simple: when a transaction is executed in a parachain (according to the logic of that chain), a transaction can be forwarded to the second parachain or relay chain. At present, the external transactions of the blockchain in the production environment can only be completely asynchronous, and they do not have the native ability to return any information to its source.

**![img 4-2 here]**

In order to ensure the smallest implementation complexity, the smallest risk, and the smallest parachain architecture constraints, these cross-chain transactions are no different from the current standard external transactions. These transactions will have a source field to identify the identity of the parachain, and an address that can be of any length. The fees for cross-chain transactions are not like the current Bitcoin or Ethereum systems, but must be managed through the negotiation logic of the source parachain and the destination parachain. An improvement proposal proposed in the Serenity version of Ethereum will be a simple way to manage this cross-chain resource payment, although we assume that others will propose more advanced methods.

The problem of cross-chain transactions can be solved with a simple queue mechanism. This queue uses a Merkle tree to ensure the authenticity of the data. The task of the relay chain is to transfer the transaction from the queue of the source parachain to the queue of the destination parachain. The forwarded transaction will be referenced on the relay chain instead of the transaction of the relay chain itself. In order to prevent one parachain from sending spam transactions to another parachain, it is stipulated that the queue of the target parachain cannot be too large when sending each transaction after the previous block ends. If the queue is too large after the block is processed, the destination parachain will be regarded as saturated, and transactions will not be routed to it in the next few blocks until the queue drops below the critical value. These queues are managed on the relay chain, allowing the parachains to mutually determine their saturation levels. If the transaction is sent to the stalled target chain, it can report the failure synchronously (because there is no return path, if the second transaction fails for the same reason, it may not send a reply to the source caller. You need to use some other recovery methods).

### 4. Protocol
#### 4.1 Relay chain operation
The relay chain will be similar to Ethereum and is also state-based. It contains a mapping relationship between account information and state storage. The information mainly includes balance and transaction counters (to prevent replay). The goal of putting the account system here is to record how much equity each identity controls in the system. But there are some notable differences:

Contracts cannot be deployed through transactions; this is to make the relay chain as lack of functionality as possible and does not support public deployment of contracts.

There is no resource counter (gas); because some functions that the public can call are fixed, the principle of the gas recording system does not apply. Therefore, in all functions, a more general fee standard will be used, so that dynamic codes can be executed more efficiently, and the transaction format will be simpler.

There will be some default contracts with special functions that manage the automatic execution of transactions and the output of network messages.

The relay chain will have an EVM-based virtual machine, but many modifications will be made to simplify it to the greatest extent. It will have some built-in contracts (similar to those Ethereum contracts with addresses between 1-4) to run specific functions of the platform, including consensus contracts, cheker contracts, and parachain contracts.

If you don’t use EVM, you may choose Web-Assembly (Wasm); in this case, all structures are still similar, but these built-in contracts based on Wasm use a language with common functions instead of those with EVM Many restricted immature languages.

It may also learn from other concepts derived from the current Ethereum. For example, some changes have been proposed in the Serenity version, such as simplifying the transaction receipt format in order to execute transactions without state conflicts in parallel in a block.

It is possible that DePOR will deploy a pure (pure) blockchain system similar to Serenity, which does not contain any basic protocol of the chain. But we feel that this will bring more complexity and development uncertainty, so it is not worthwhile to implement such a more efficient and brief great agreement at this stage.

In order to manage the consensus mechanism, many small pieces of functions are needed: cheker set, cheker mechanism, parachain , etc. These can all be put in an overall agreement. However, in order to achieve modularity, we will describe these as the contract of the relay chain. This means that they are all objects managed by the consensus mechanism of the relay chain (similar to an object-oriented language), but they are not necessarily EVM-like bytecodes, nor may they be addressable through the account system.

#### 4.2 Equity contract
This contract manages the cheker collection:

- Which accounts are cheker ;

- Which ones can become cheker in the short term ;

- Which accounts have pledged equity in order to nominate cheker

- Each person’s attributes, including balance, acceptable deposit ratio, address list, and session identity

It allows an account to register when it wants to become a cheker (some requirements need to be met), nominate a user, and log out when it wants to withdraw from the cheker role. It also contains some functions for verification and agreement.

#### 4.3 Registration of Parachain
This module is used to record each parachain in the system. It is a relatively simple database-like structure that manages the static and dynamic information of each chain.

The static information includes the index of the chain (an integer) and the identification of the verification protocol. The protocol identifier is used to distinguish different parachains . Only in this way can the cheker run the correct verification algorithm and then submit the legal candidate block. An initial proof-of-concept version will focus on how to put a new verification algorithm in the client, so that every time a new type of blockchain is added, a hard fork is required. However, under the condition of ensuring strict and efficient, it is still possible to let the cheker know the new verification algorithm without a hard fork . A possible implementation method is to use a certain, locally compiled, platform-independent language to describe the verification algorithm of the parachain, such as WebAssembly. In order to verify the feasibility of this method, we have to do more investigations, after all, if we can avoid hard forks, there will still be great advantages.

Dynamic information involves transaction routing systems, such as global consensus on the entry queues of parachains (discussed in the next section).

A referendum must be passed to register a new parachain. This could have been directly managed internally, but an external referendum contract would be better, because this contract can also be used for governance in more other scenarios. Regarding the specific parameters of the parachain voting registration system (such as the quorum, the proportion of the majority), formal proofs will be used to make a "main constitution" system that is not frequently updated. Of course, the initial stage may only use traditional methods. The specific formula is beyond the scope of this article. For example, it is approved by a majority of 2/3, and only 1/3 of the tokens in the whole system are voted.

There are also operations to suspend and delete parachains. We hope to never suspend a parachain, but this design is to be able to deal with some emergency situations of parachain. The most obvious situation is that the cheker runs multiple client implementations of the parachain, which may make it impossible to reach a consensus on a block. We also encourage cheker to use multiple client implementations so that such things can be detected as early as possible to prevent the deposit from being deducted.

Because the suspension of the operation is an emergency measure, the cheker dynamic voting method will be used instead of a referendum. For the restart operation, it may be directly voted through the cheker , or it may be completed through a referendum.

Deletion of parachains can only be carried out through a referendum, and a loose and smooth exit transition period should be provided, allowing them to become an independent blockchain or part of other consensus systems. This period may be several months, and it is best to be set by Parachain according to its own needs.

#### 4.4 Package Relay Chain Block
The process of block packaging is essentially a process of consensus and a process of making basic data meaningful. In a PoW chain, there is a synonym for packaging called mining. In this solution, it involves collecting cheker 's signatures for the validity, availability, and consistency of blocks. These blocks include relay chain blocks and all parachain blocks it contains.

The underlying BFT consensus algorithm is also not the current scope of work. We do not describe it, but use a primitive to describe a state machine driven by consensus. In the end we hope to be inspired by some existing consensus algorithms: Tangaora (Raft's BFT variant), Tendermint, and HoneyBadgerBFT. Consensus algorithms need to reach consensus on multiple parachains concurrently. Assuming that once a consensus is reached, we can irrefutably record who participated in it. In the agreement, we can also reduce the people who acted improperly to a group, which includes only malicious participants, so that collateral damage can be reduced during punishment.

These proofs in the form of signature declarations, the state tree root of the relay chain, and the transaction tree root are stored together in the block header of the relay chain.

The packaging process of the relay chain block and the parachain block is in the same consensus generation mechanism. The two types of blocks together constitute the content of the relay chain: the parachain is not "submitted" by their group in isolation Collected again. Although this makes the process of the relay chain more complicated, it also allows us to complete the consensus of the entire system in one stage, minimize delays, and support more complex data availability, which will be very important in the routing process. it works.

A simple table (two-dimensional) can be used to model the consensus machine of each participant. Each participant has a series of information from other participants in the form of signatures, describing the candidate blocks of each parachain and the candidate block of the relay chain. This information has two parts of data:

Availability: For the submitted transaction of this block in the egress queue, does the cheker have enough information to correctly verify the candidate block of the parachain in the next block? They can vote 1 (know) or 0 (unsure). When they voted for 1, they promised to vote in the same way in subsequent voting. Subsequent voting and this non-correspondence will result in punishment.

Validity: Are the blocks of the parachain valid? Does it contain all the external data (such as transactions) referenced? This is related to the cheker 's vote on parachains. They can vote 1 (valid), -1 (invalid) or 0 (uncertain). As long as they voted non-zero, they promised to vote in the same way in subsequent voting. Subsequent voting and this non-correspondence will result in punishment.

All chekers must vote; subject to the above rules, you can also resubmit the vote. The consensus process can be modeled like many standard BFT consensus algorithms, and each parachain is parallel. In addition to a small probability that a small number of malicious participants will be assigned to the same parachain group, the consensus algorithm can still support the network as a whole, and the worst case is only one or more parachain empty blocks. And the deadlock situation (there is another round to make a penalty)

The basic rules for judging whether an independent block is valid (allow all chekers as a whole to reach a consensus, and then these parallel chain blocks become consistent data references on the relay chain):

At least two-thirds of chekers must vote "yes", and no one must vote "no".

More than one-third of chekers are required to vote "yes" on the availability of egress queue messages.

For validity, if there is at least one "yes" and at least one "no" vote, a special condition is activated, and the entire cheker must vote to determine whether there are malicious participants or whether an accidental fork has occurred . In addition to valid and invalid, the third type of vote is also supported, which is equivalent to voting "yes" and "no" at the same time, indicating that this node has conflicting opinions. This may be due to the divergence of multiple client implementations run by the node owner, and also indicates that there may be unclear points in the parachain protocol.

When all cheker votes have been recorded, and it is found that the winning opinion is less than a certain number of votes (the detailed parameters may be up to half, or less), then it can be assumed that the parachain has an unexpected hard fork, this parallel The consensus of the chain will be automatically suspended. Otherwise, we assume that malicious behavior has occurred and punish those chekers who voted "yes" for the losing opinion .

The conclusion is that only enough signature votes can reach consensus, and then the blocks of the relay chain are packaged and the next block is packaged.

#### 4.5 Improvement of relay chain block packaging
The method of packing blocks ensures the normal operation of the system, because the key information of each parachain must be guaranteed by more than one-third of the chekers , so it cannot scale well. This means that as more parachains increase, the work of each cheker will increase.

In an open consensus network, how to ensure the availability of data is still a problem to be solved, but there are still some methods to alleviate the performance bottleneck of cheker nodes. A simple solution is: In fact, chekers are only responsible for verifying the availability of data, so they do not need to actually store, communicate, and copy data by themselves. The second solution is data isolation. This solution is likely to be related to how Collector organizes data. The network can have certain interest or income incentives for Collector to ensure that the data provided to cheker is available.

However, this solution may bring some flexibility, but it still does not solve the fundamental problem. Because adding more parachains usually requires adding chekers , the consumption of network resources (mainly bandwidth) increases at the rate of the square of the total number of chains, which is not sustainable in the long run.

In the end, we may think about the fundamental limitation of ensuring the security of the consensus network. The growth rate of the network's demand for bandwidth is the number of chekers multiplied by the total number of messages. We cannot trust those consensus networks that separate data and store them in different nodes, because this separates data from operations.

#### 4.6  Cross-chain transaction routing
Cross-chain transaction routing is the core function of the relay chain and its cheker. The main logic is managed here: how a submitted transaction (or "submit" in short) is forcibly routed from the exit of one source parachain to another destination parachain without any Trust people.

We chose the above words very carefully; in the source parachain, we do not need to explicitly restrict the submitted transaction. The only constraint in our model is: Parachains must try their best to package according to all export capabilities, and these submissions are the result of their block execution.

We organize these submissions in a first-in-first-out (FIFO) queue. The number of queues used as a routing base may be around 16. This number represents the parachain performance that we can directly support without using multi-phase routing. DePOR will initially support this kind of direct routing, but we may also adopt a multi-phase routing operation (hyper-routing) as a way to scale the system in the future.

We assume that all participants know the cheker grouping of the next two blocks n, n+1. In summary, the routing system has the following stages:
- Collectors: chekerV[n][S] in contract members.

- Collectors: FOR EACH team s: make sure there is at least one chekerV[n][S] in the contract.

- Collectors: FOR EACH Team s: Assuming that the exit [n-1][s][S] is available (all data submitted to S in the previous block)

- Collectors: Construct candidate block b for S: (b.header, b.ext, b.proof, b.receipt, b.egress).

- Collectors: send proof information proof[S] = (b.header, b.ext, b.proof, b.receipt, b.egress).

- Collectors: Ensure that the external transaction data b.ext is already available to other Collectors and chekers.

- Collectors: FOR EACH group s: send export information egress[n][S][s]= (b.header, b.ext, b.receipt, b.egress) to the chekerV[ of the receiver group of the next block n+1][s].

- chekerv: members of the same group pre-connected to the next block: let N = Chain[n+1][V]; connect all chekers so that Chain[n+1][v] = N.

- chekerv: Collect all the entry data of this block: FOR EACH Group s: retrieve the exit egress[n-1][s][Chain[n][V]], get Chain[n][v] = Chain from other chekerv [n][V]. It may be by randomly selecting other cheker's proof data.

- chekerv: receive the export data of the candidate block for the next block: FOR EACH group s, receive egress[n][s][N]. Vote on the validity of the block exit; republish among the intention chekers so that Chain[n+1][v] = Chain[n+1][V].

- chekerv: waiting for consensus.

- egress[n][from][to] represents: in block n, the current egress queue information from the source from parachain to the target to parachain. The Collectors belong to Parachain S. chekerV[n][s] is the cheker group of parachain s in block n. Conversely, Chain[n][s] is the parachain to which chekerv belongs in block n. block.egress[to] is the egress queue sent from the parachain block block to the target parachain to.

Because Collector hopes to be able to adopt their blocks, it collects (transaction) fees as an incentive and ensures that the target group members of the next block can know the exit queue of the current block. The incentive of chekers is to reach a consensus on relay chain blocks, so they do not care about which Collector block is finally adopted. In principle, a cheker can collude with a Collector to reduce the probability of adopting other Collectors. However, because the chekers of the parachain are randomly allocated, this is difficult to achieve, and it may be subject to fee reductions, which ultimately affects the consensus process.

#### 4.7 Verification of Parachain

The cheker pledges a large amount of deposit. Their main goal is to verify the validity of the parachain block, including but not limited to: state transition, including external transactions, execution waiting for submission in the entry queue, and execution of the final state of the exit queue. The process itself is relatively simple. Once chekers have completed the packaging of the previous block, they are free to prepare candidate blocks for the parachain for the following rounds of consensus.

The cheker first finds a parachain block through the parachain Collector (described below) or one of his secondary chekers . Parachain candidate block data includes block header, previous block header, external data input (for Ethereum and Bitcoin, these data are called transactions, but they may also be of any structure and purpose), export queue data, and state transitions are valid Sexual internal proof data (for Ethereum, this may be many state/storage tree nodes used to execute each transaction). Experimental evidence shows that for the current Ethereum block, this data set is at most several hundred kilobytes (KiB).

If the check is not completed, cheker will attempt to convert a former block access to relevant information, the previous block cheker start, after all signatures to this data cheker .

Once a cheker receives such a candidate block, they validate it locally. The verification process is included in the cheker module of the parachain . This software module that requires consensus must be written in all DePOR implementations (in principle, a C ABI library can be shared among multiple implementations, but this will reduce security. , Because they are just references to a single implementation).

This process will extract the header of the previous block, and then use the hash value recorded in the relay chain block that just reached a consensus to verify. Once the validity of the parent block header is verified, the specific verification function in the parachain class is called. This is a function that will receive many data items (probably the ones currently given), and the return value is a simple judgment on whether the block is valid.

Most of these verification functions will first check the header data items, which can be directly derived from the parent block (for example, parent block hash, height). Later, in order to process the transaction or submission, they will try to fill in the internal data structure. For a blockchain like Ethereum, all transactions need to be executed to fill such a large amount of data into the Merkle tree. Other types of blockchain may have other measures.

Once verification is completed, entry submissions and external transactions (or others represented) will be fixed according to the rules of the chain. (A possible default method is to require all entry submissions to be processed before serving external transactions, but this should be determined by the logic of the parachain). Through this regulation, a series of export submissions will be created, and it does meet the candidate block requirements of Collector . Finally, the block headers that are reasonably filled and the candidate block headers will be checked together.

After the cheker has completed the verification of the candidate block, it will vote on the hash of the block header and send the necessary verification information to the other secondary chekers in the group .

#### 4.8 Network Design
The network design requirements in traditional blockchains such as Ethereum and Bitcoin are generally relatively simple. All transactions and blocks are broadcast with gossip unguided. There will be more things involved in the synchronization module, and Ethereum can make different responses according to different categories, but in reality this is more the node's strategy rather than the content of the protocol itself.

Ethereum has improved the current network protocol based on the devp2p protocol, and supports the multiplexing of multiple sub-protocols in a single node connection, so it supports multiple p2p protocols at the same time, but the Ethereum protocol is still relatively primitive, and it has not Complete important functions such as supporting QoS. The original desire to create a ubiquitous "web3" protocol basically failed, leaving only a few projects raised from Ethereum crowdfunding.

The demand of DePOR is more fundamental. Compared with a complete unified network, DePOR has many types of participants, each of which has different needs, and participants need to have many different network channels to exchange data. Essentially, this means that a protocol that can support a more hierarchical network structure is needed. In addition, in order to promote more new types of blockchains to expand the network, a new hierarchical structure is also needed.

The deeper discussion of network protocols is beyond the scope of this paper. We need more demand analysis. We can divide network participants into two categories (relay chains and parachains), each of which has three sub-categories. The participants of each parachain communicate with each other, but not with other chains:

**Relay chain participants**

- cheker : P, divided into multiple subsets P[s] for each parallel chain

- Availability Guarantor: A ( replaced by cheker in the basic agreement )

- Relay chain client: M (member of each parachain)

**Parachain participants:**

- Parallel chains Collector : C [0], C [. 1], ...

- Parallel chains blocker : F. [0], F. [. 1], ...

- Parachain client: S[0], S[1],...

- Parachain light client: L[0], L[1],...

Usually we think that the following types of communication will occur between network members and their settings:

- P | A<-> P | A: In order to reach a consensus, the cheker /guarantor must be connected.

- P[s]<-> C[s] | P[s]: Each cheker as a parachain member will connect with other members to discover and share blocks, such as Collector .

- A <->P[s] | C | A: Each availability guarantor will need to collect signed consensus-related cross-chain data from the cheker ; Collector can broadcast to the guarantor to optimize the consensus on their block. Once completed, the data will be broadcast to other guarantors to promote consensus.

- P[s]<-> A | P[s']: The parachain cheker will need to collect additional input data from the previous cheker or collection of availability guarantors.

- P[s]<-> A: When it is necessary to report, blocker announces to any participating parties.

- M <-> M| P | A: Relay chain client outputs data to cheker and guarantor.

- S[s]<-> S[s] | P[s] | A: Parachain client outputs data to cheker and guarantor.

- L[s]<-> L[s] | S[s]: The parachain light client obtains data from the full client.

If in order to ensure efficient transmission, the flat-layer network (similar to Ethereum devp2p) with no difference between each node will no longer adapt. The agreement is likely to be extended to introduce a reasonable node selection and discovery mechanism, and it is also possible to plan some forward-looking algorithms to ensure that the order of nodes is "accidentally" connected at an appropriate time.

The specific strategies of different participating nodes will be different: for a scalable multi-chain system, Collector either needs to continuously reconnect the selected cheker , or connect to a cheker group to ensure that they are always online, even if most of the time They are useless to themselves. The Collector will also maintain one or more stable connections with the availability guarantor to ensure the rapid spread of consensus data.

The availability guarantor will remain connected to each other and also maintain stable connections with chekers (for consensus and parachain data requiring consensus), some Collectors (for parachain data), some blockers, and some full nodes (for missing information). chekers tend to look for other chekers , especially those in the same group, and those Collectors that can provide parachain blocks .

blocker and general relay chain or parachain clients tend to maintain a connection with cheker or guarantor, but many nodes similar to them do not. In addition to connecting other light clients, the parachain light client will also connect to a parachain full client.
