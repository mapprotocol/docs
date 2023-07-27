# **MAP Governance**

MAP Protocol uses a formal **on-chain** governance mechanism to manage and upgrade the protocol such as for upgrading smart contracts, adding new stable currencies, or modifying the reserve target asset allocation. All changes must be agreed upon by **MAP holders**. A **quorum threshold** model is used to determine the number of votes needed for a proposal to pass.


## **Proposal Process**

Changes are managed via the MAP Governance smart contract. This contract acts as an "owner" for making modifications to other protocol smart contracts. Such smart contracts are termed **governable**. The Governance contract itself is governable, and owned by itself.

The change procedure happens in the following phases:

- Proposal
- Approval
- Referendum
- Execution

## Phases Overview

Each proposal starts on the **Proposal Queue** where it may receive **upvotes** to move forward in the queue relative to other queued proposals. Proposal authors should work to find community members to upvote their proposal (proposers may also upvote their proposals). Up to **3 proposals** from the top of the queue are dequeued and promoted to the approval stage automatically per day. Any proposal that remains in the queue for **4 weeks** will expire.

- **Approval** lasts **1 day (24 hours)**, during which the proposal must be approved by the Approver(s). Approved proposals are promoted to the Referendum stage.
- **Referendum** lasts **5 days**, during which owners of Locked MAP vote yes or no on the proposal. Proposals that satisfy the necessary quorum are promoted to the execution phase.
- **Execution** lasts up to **3 days**, during which anybody may trigger the execution of the proposal.

## Proposal

Any user may submit a Proposal to the Governance smart contract, along with **50000 MAP** deposit. This deposit is required to avoid spam proposals, and is refunded to the proposer if the proposal reaches the Approval stage. A Proposal consists of a list of transactions, and a description URL where voters can get more information about the proposal. It is encouraged that this description URL points to a **MEP document** in the [https://github.com/mapprotocol/MEPs](https://github.com/mapprotocol/MEPs) repository. Transaction data in the proposal includes the destination address, data, and value. If the proposal passes, the included transactions will be executed by the Governance contract.

Submitted proposals are added to the queue of proposals. While a proposal is on this queue, voters may use their [Locked MAP](https://docs.mapprotocol.io/develop/map-relay-chain/how-to-vote) to upvote the proposal. Once per day the **top three proposals**, by weight of the Locked Map upvoting them, are dequeued and moved into the Approval phase. Note that if there are fewer than three proposals on the queue, all may be dequeued even if they have no upvotes. If a proposal has been on the queue for for more than **4 weeks**, it expires and the deposit is forfeited.

## Approval

Every day the top three proposals at the head of the queue are pop off and move to the Approval phase. At this time, the original proposers are eligible to reclaim their Locked Map deposit. In this phase, the proposal needs to be approved by the Approver. The Approver is initially a 3 of 9 multi-signature address held by individuals selected by the Map Foundation, and will move to a DAO in the future. The Approval phase lasts **1 day** and if the proposal is not approved in this window, it is considered expired and does not move on to the “Referendum” phase.

## Referendum

Once the Approval phase is over, approved proposals graduate to the referendum phase. Any user may vote **yes**, **no**, or **abstain** on these proposals. Their vote's weight is determined by the **weight of their Locked Map**. After the Referendum phase is over, which lasts **five days**, each proposal is marked as passed or failed as a function of the votes and the corresponding passing function parameters.

In order for a proposal to pass, it must meet a minimum threshold for participation, and agreement:

- **Participation** is the **minimum portion of Locked MAP** which must cast a vote for a proposal to pass. It exists to prevent proposals passing with very low participation. The participation requirement is calculated as a governable portion of the participation baseline, which is an exponential moving average of final participation in past governance proposals.
- **Agreement** is the portion of votes cast that must be **"yes" votes** for a proposal to pass. Each contract and function can define a required level of agreement, and the required agreement for a proposal is the maximum requirement among its constituent transactions.

## **Execution**

Proposals that graduate from the Referendum phase to the Execution phase may be **executed by anyone**, triggering a call operation code with the arguments defined in the proposal, originating from the Governance smart contract. Proposals expire from this phase after **three days**.
