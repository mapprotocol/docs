# Summary

* Learn
  * About MAP Protocol
    * [Background](learn/background.md)
    * [Introduction of MAP Protocol](learn/introduction.md)
    * [History of MAP Protocol](learn/history.md)
  * Comparison with other cross-chain models
    * [Summary](architecture/comparison.md)
    * [MAP Protocol vs Cosmos & Polkadot](learn/CosmosPolkadot.md)
    * [MAP vs other cross-chain solutions without relay chain](learn/others.md)
  * [Technical Mechanism](architecture/overiew.md)
    * [MAP Protocol's Technical Mechanism](architecture/technical-mechanism.md)
    * [Three-layer Architecture](architecture/3layer.md)
    * [MAP Protocol Layer](learn/protocollayer.md)
      * MAP Relay Chain
          * [Concept](architecture/concept.md)
          * [Isomorphism with All Chains](architecture/precompile.md)
      * [Verification & Maintenance Network](architecture/verification.md)
    * [MAP Cross-chain Service (MCS) Layer](architecture/mcs.md)
    * [MAP Application Layer](architecture/application.md)
  * [Gas Fee Model](learn/fee.md)
  * [DAO](learn/dao.md)
  * [Construction of MAP Protocol](architecture/construction.md)
     * [Developers](learn/developers.md)
     * [Validators](learn/validators.md)
     * [Maintainers](learn/maintainer.md)
     * [Messengers](learn/messengers.md)
     * [Liquidity providers](learn/liquidityprovider.md)
     * [End users](learn/enduser.md)
  * [Tokenomics](learn/tokenomics.md)
  * [Roadmap](learn/roadmap.md)
  * [Purchase $MAP](/learn/purchase.md)
* Develop
  * MAP Relay Chain
    * [Make Private Chain](map-chain/privatenet/PrivateNet.md) 
    * [How To Make Genesis](map-chain/validator/Overview/HowToMakeGenesis.md)
    * [How To Vote](map-chain/validator/Overview/HowToVote.md)
    * [How To Withdraw](map-chain/validator/Overview/HowToWithdraw.md)
    * [Marker](map-chain/marker/Marker.md)
        * [Validator](map-chain/marker/AboutValidator.md)
        * [Vote](map-chain/marker/AboutVote.md)
        * [ContractOwner](map-chain/marker/AboutContractOwner.md)
        * [Common](map-chain/marker/AboutCommon.md)
    * Consensus
        * [Proof-of-Stake](map-chain/consensus/Proof-of-Stake.md)
        * Epoch And Block
          * [AggregatedSeal](map-protocol/consensus/epoch-and-block/AggregatedSeal.md)
        * [Validator](map-chain/validator/Validator.md)
        * [Locked MAP](map-chain/validator/LockedMAP.md)
        * [Election](map-chain/validator/Election.md)
        * [Rewards](map-chain/validator/Rewards.md)
    * [Precompiled Contracts](map-chain/precompile-contract.md)
    * [Contracts](map-chain/validator/contracts/ContractsAddress.md)
        * [Validator Contracts Deploy](map-chain/validator/contracts/DeployContracts.md)
        * [AccountsABI](map-chain/validator/contracts/ABI/AccountsABI.md)
        * [ElectionABI](map-chain/validator/contracts/ABI/ElectionABI.md)
        * [EpochRewardsABI](map-chain/validator/contracts/ABI/EpochRewardsABI.md)
        * [LockedGoldABI](map-chain/validator/contracts/ABI/LockedGoldABI.md)
        * [ValidatorsABI](map-chain/validator/contracts/ABI/ValidatorsABI.md)
    * Makalu PoC
        * [PoC-1](Makalu-PoC/PoC-1.md)
        * [PoC-2](Makalu-PoC/PoC-2.md)
  * Light Client
    * [Eth LightClient Contract](cross-chain/ethereum/light-client-data/Header-Store-Contract.md)
    * [Eth LightClient Verify](cross-chain/ethereum/tx-verify/Tx-Verify.md)
    * [Eth LightClient Verify Contract](cross-chain/ethereum/tx-verify/Tx-Verify-Contract.md)
    * [MAP LightClient Verify](cross-chain/map/tx-verify/Tx-Verify.md)
    * [MAP LightClient Verify Contract](cross-chain/map/tx-verify/Tx-Verify-Contract.md)
  * [Maintainer](map-chain/relayer/Compass.md)
  * MAP Cross-chain Service
      * [Contract](mcs/contract/bridge.md)
      * ABI
        * [Fee Center](mcs/abi/FeeCenter.md)
        * [Map Bridge Relay](mcs/abi/MAPBridgeRelayV2.md)
        * [Map Bridge](mcs/abi/MAPBridgeV2.md)
        * [Vault Token](mcs/abi/VToken.md)
        * [Staking](mcs/abi/MasterChef.md)
      * Address
        * [MCS V1](mcs/address/bridge-v1.md)
        * [MCS V2 Test](mcs/address/bridge_v2_test.md)
      * [Messenger](mcs/messenger/messenger.md)
  * SDK
    * [Atlas JSON RPC](rpc-api/RPC-API.md)
    * [Atlas Consensus API](map-chain/consensus/ConsensusAPI.md)
* Run
    * [How To Become A New Validator](map-chain/validator/Overview/HowToBecomeANewValidator.md)
    
