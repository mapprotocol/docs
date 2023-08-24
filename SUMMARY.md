# Summary

* [Overview](README.md)

## Learn

* About MAP Protocol
  * [Background](learn/background.md)
  * [Introduction of MAP Protocol](learn/introduction.md)
  * [History of MAP Protocol](learn/history.md)
* [Comparison with other cross-chain models](learn/comparison.md)
  * [MAP Protocol vs Cosmos &amp; Polkadot](learn/CosmosPolkadot.md)
  * [MAP vs other cross-chain solutions without relay chain](learn/others.md)
* [Technical Mechanism](learn/Teachnical_Mechanism/overiew.md)
  * [MAP Protocol&#39;s Technical Mechanism](learn/Teachnical_Mechanism/technical-mechanism.md)
  * [Three-layer Architecture](learn/Teachnical_Mechanism/3layer.md)
  * [MAP Protocol Layer](learn/Teachnical_Mechanism/protocollayer.md)
    * MAP Relay Chain
      * [Concept](learn/Teachnical_Mechanism/concept.md)
      * [Isomorphism with All Chains](learn/Teachnical_Mechanism/precompile.md)
      * [Verification &amp; Maintenance Network](learn/Teachnical_Mechanism/verification.md)
  * [MAPO Service (MOS) Layer](learn/Teachnical_Mechanism/mcs.md)
  * [MAPO Application Layer](learn/Teachnical_Mechanism/application.md)
  * [Peer-to-Peer Cross-chain Technology with ZK-enabled Light Clients](learn/Teachnical_Mechanism/Peer-to-Peer Cross-chain Technology with ZK-enabled Light Clients.md)
* [Gas Fee Model](learn/fee.md)
* [DAO](learn/dao.md)
* [Construction of MAP Protocol](learn/Construction_of_MAP_Protocol/construction.md)
  * [Developers](learn/Construction_of_MAP_Protocol/developers.md)
  * [Validators](learn/Construction_of_MAP_Protocol/validators.md)
  * [Maintainers](learn/Construction_of_MAP_Protocol/maintainer.md)
  * [Messengers](learn/Construction_of_MAP_Protocol/messengers.md)
  * [Liquidity providers](learn/Construction_of_MAP_Protocol/liquidityprovider.md)
  * [End users](learn/Construction_of_MAP_Protocol/enduser.md)
* [Tokenomics](learn/tokenomics.md)
* [Roadmap](learn/roadmap.md)
* [Purchase $MAP](/learn/purchase.md)

## Develop

* [MAP Relay Chain](develop/map-relay-chain/README.md)
  * Getting Started

    * [Build](develop/map-relay-chain/getting-started/build.md)
    * [Make Private Chain](develop/map-relay-chain/getting-started/make-private-chain.md)
    * [How To Vote](develop/map-relay-chain/getting-started/how-to-vote.md)
    * [How To Withdraw](develop/map-relay-chain/getting-started/how-to-withdraw.md)
    * [Integrate an Exchange](develop/map-relay-chain/getting-started/Integrate-an-Exchange.md)
    * [Integration of MAP with EVM-Compatible Chains](develop/map-relay-chain/getting-started/Integration-EVM-Compatible-Chains.md)
  * Consensus

    * [Overview](develop/map-relay-chain/consensus/Overview.md)
    * [Proof-of-Stake](develop/map-relay-chain/consensus/Proof-of-Stake.md)
    * Validator
      * [Validator](develop/map-relay-chain/consensus/Validator.md)
      * [Locked MAP](develop/map-relay-chain/consensus/LockedMAP.md)
    * [Election](develop/map-relay-chain/consensus/Election.md)
    * [Rewards](develop/map-relay-chain/consensus/Rewards.md)
    * [Aggregated Seal](develop/map-relay-chain/consensus/AggregatedSeal.md)
  * [Contracts](develop/map-relay-chain/contracts/ContractsAddress.md)

    * [Precompiled Contracts](develop/map-relay-chain/contracts/precompile-contract/precompile-contract.md)
    * Genesis Contracts
      * ABI
        * [AccountsABI](develop/map-relay-chain/contracts/ABI/AccountsABI.md)
        * [ElectionABI](develop/map-relay-chain/contracts/ABI/ElectionABI.md)
        * [EpochRewardsABI](develop/map-relay-chain/contracts/ABI/EpochRewardsABI.md)
        * [LockedGoldABI](develop/map-relay-chain/contracts/ABI/LockedGoldABI.md)
        * [ValidatorsABI](develop/map-relay-chain/contracts/ABI/ValidatorsABI.md)
      * [Deploy](develop/map-relay-chain/contracts/DeployContracts.md)
  * [Marker](develop/map-relay-chain/marker/Marker.md)

    * [Genesis](develop/map-relay-chain/getting-started/how-to-make-genesis.md)
    * [Validator](develop/map-relay-chain/marker/AboutValidator.md)
    * [Vote](develop/map-relay-chain/marker/AboutVote.md)
    * [ContractOwner](develop/map-relay-chain/marker/AboutContractOwner.md)
    * [Common](develop/map-relay-chain/marker/AboutCommon.md)
  * [Account-Abstraction](develop/map-relay-chain/account-abstraction/eip4337.md) 
* [Light Client](develop/light-client/README.md)
  * Verification based on Light Client
  * MAPO Light Client
    * [EVM Chains](develop/light-client/map/evm.md)
    * [Near](develop/light-client/map/near.md)
  * Light Clients
    * [Client Manager](develop/light-client/light-client-manager.md)
    * [BNB Smart Chain](develop/light-client/clients/bsc.md)
    * [Near Protocol](develop/light-client/clients/near.md)
    * [Polygon(Matic)](develop/light-client/clients/matic.md)
    * [Ethereum 2.0](develop/light-client/clients/eth2.md))
  * [Maintainer](develop/light-client/Maintainer.md)
* MAPO Service
  * [MOS Message Guides](develop/mos/message/README.md)
    * [How It Works](develop/mos/message/cross-chain-message.md)
  * [MCS Guides](develop/mos/mcs/README.md)
    * [How It Works](develop/mos/mcs/how-it-works.md)
    * [How To Use](develop/mos/mcs/how-to.md)
      * [How To On Evm Chains](develop/mos/mcs/how-to-evm.md)
      * [How To On Near Protocol](develop/mos/mcs/how-to-near.md)
    * [Relay Chain Contract](develop/mos/mcs/mcs-on-mapo.md)
    * [EVM Chains Contract](develop/mos/mcs/mcs-on-evm.md)
    * [Near Protocol Contract](develop/mos/mcs/mcs-on-near.md)
  * [Messenger](develop/mos/messenger/messenger.md)
  * [API](develop/mos/mcs/api/api.md)
* OmniChain Examples
  * [OmniApp](develop/mos/examples/omni-app.md)
  * [OmniDictionary](develop/mos/examples/OmniDictionary.md)
  
* API & SDK
  * [Butter SDK](sdk/butter.md)
  * [Atlas JSON RPC](sdk/RPC-API.md)
  * [Atlas Consensus API](sdk/ConsensusAPI.md)
  * [MAP Scan API](develop/sdk/scan-api.md)

## Run

* [How To Become A New Validator](run/HowToBecomeANewValidator.md)
* [How To Become A New Validator[advanced]](run/HowToBecomeANewValidatorAdvanced.md)
* [Withdraw](run/Withdraw.md)
