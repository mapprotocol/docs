# 概括
* [概述](README.md)

## 學習

* 關於 MAP 協議
  * [背景](learn/background.md)
  * [介紹 MAP 協議](learn/introduction.md)
  * [MAP 協議的發展歷程](learn/history.md)
* [與其他跨鏈模型進行比較](learn/comparison.md)
  * [MAP 協議與Cosmos＆ Pokadot](learn/CosmosPolkadot.md)
  * [MAP vs. 其他無中繼鏈的跨鏈解決方案](learn/others.md)
* [技術機制](learn/Teachnical_Mechanism/overiew.md)
  * [MAP協議'的技術機制](learn/Teachnical_Mechanism/technical-mechanism.md)
  * [三層結構](learn/Teachnical_Mechanism/3layer.md)
  * [MAP協議層](learn/Teachnical_Mechanism/protocollayer.md)
  * MAP中繼鏈
    * [概念](learn/Teachnical_Mechanism/concept.md)
    * [與所有鏈的同構性](learn/Teachnical_Mechanism/precompile.md)
    * [驗證& 維護網絡](learn/Teachnical_Mechanism/verification.md)
  * [MAPO服務(MOS)層](learn/Teachnical_Mechanism/mcs.md)
  * [MAPO應用層](learn/Teachnical_Mechanism/application.md)
  * [ZK 支持下的輕節點](learn/Teachnical_Mechanism/zk_lightclient.md)
* [燃氣費收費模式](learn/fee.md)
* [DAO](learn/dao.md)
* [MAP協議的構建](learn/Construction_of_MAP_Protocol/construction.md)
  * [開發者](learn/Construction_of_MAP_Protocol/developers.md)
  * [驗證者](learn/Construction_of_MAP_Protocol/validators.md)
  * [維護者](learn/Construction_of_MAP_Protocol/maintainer.md)
  * [信使](learn/Construction_of_MAP_Protocol/messengers.md)
  * [流動資金提供者](learn/Construction_of_MAP_Protocol/liquidityprovider.md)
  * [終端用戶](learn/Construction_of_MAP_Protocol/enduser.md)
* [Tokenomics](learn/tokenomics.md)
* [路線圖](learn/roadmap.md)
* [購買$MAP](/learn/purchase.md)

## 開發

* [MAP Relay Chain](develop/map-relay-chain/README.md)

  * 入門
    * [構建](develop/map-relay-chain/getting-started/build.md)
    * [製作私有鏈](develop/map-relay-chain/getting-started/make-private-chain.md)
    * [如何投票](develop/map-relay-chain/getting-started/how-to-vote.md)
    * [如何取出質押](develop/map-relay-chain/getting-started/how-to-withdraw.md)
    * [集成到交易所](develop/map-relay-chain/getting-started/Integrate-an-Exchange.md)
    * [MAP Protocol 對接EVM兼容鏈](develop/map-relay-chain/getting-started/Integration-EVM-Compatible-Chains.md)
  * 共識
    * [概述](develop/map-relay-chain/consensus/Overview.md)
    * [Proof-of-Stake](develop/map-relay-chain/consensus/Proof-of-Stake.md)
    * 驗證器
      * [驗證器](develop/map-relay-chain/consensus/Validator.md)
      * [鎖定的MAP](develop/map-relay-chain/consensus/LockedMAP.md)
    * [選舉](develop/map-relay-chain/consensus/Election.md)
    * [獎勵](develop/map-relay-chain/consensus/Rewards.md)
    * [聚合印章](develop/map-relay-chain/consensus/AggregatedSeal.md)
  * [合約](develop/map-relay-chain/contracts/ContractsAddress.md)

    * [預編譯合約](develop/map-relay-chain/contracts/precompile-contract/precompile-contract.md)
    * 創世紀合約
      * ABI
        * [AccountsABI](develop/map-relay-chain/contracts/ABI/AccountsABI.md)
        * [ElectionABI](develop/map-relay-chain/contracts/ABI/ElectionABI.md)
        * [EpochRewardsABI](develop/map-relay-chain/contracts/ABI/EpochRewardsABI.md)
        * [LockedGoldABI](develop/map-relay-chain/contracts/ABI/LockedGoldABI.md)
        * [ValidatorsABI](develop/map-relay-chain/contracts/ABI/ValidatorsABI.md)
      * [Deploy](develop/map-relay-chain/contracts/DeployContracts.md)
  * [Marker](develop/map-relay-chain/marker/Marker.md)

    * [Genesis](develop/map-relay-chain/getting-started/how-to-make-genesis.md)
    * [驗證器](develop/map-relay-chain/marker/AboutValidator.md)
    * [Vote](develop/map-relay-chain/marker/AboutVote.md)
    * [ContractOwner](develop/map-relay-chain/marker/AboutContractOwner.md)
    * [Common](develop/map-relay-chain/marker/AboutCommon.md)
* [輕型客戶端](develop/light-client/README.md)
  * 基於輕型客戶端的驗證
  * MAPO輕型客戶端
    * [EVM鏈](develop/light-client/map/evm.md)
    * [Near](develop/light-client/map/near.md)
  * 輕客戶端
    * [客戶端管理器](develop/light-client/light-client-manager.md)
    * [BNB 鏈](develop/light-client/clients/bsc.md)
    * [NEAR 協議](develop/light-client/clients/near.md)
    * [Polygon(Matic)](develop/light-client/clients/matic.md)
    * [Ethereum 2.0](develop/light-client/clients/eth2.md))
    * [維護者](develop/light-client/Maintainer.md)
* MAPO 全鏈服務
  * [MOS消息指南](develop/mos/message/README.md)
    * [How It Works](develop/mos/message/cross-chain-message.md)
  * [MOS 指南](develop/mos/mcs/README.md)
    * [運行原理](develop/mos/mcs/how-it-works.md)
    * [如何使用](develop/mos/mcs/how-to.md)
      * [在Evm鏈的方法](develop/mos/mcs/how-to-evm.md)
      * [在NEAR協議上](develop/mos/mcs/how-to-near.md)
    * [中繼鏈合約](develop/mos/mcs/mcs-on-mapo.md)
    * [EVM鏈合約](develop/mos/mcs/mcs-on-evm.md)
    * [ NEAR 協議合約](develop/mos/mcs/mcs-on-near.md)
  * [信使](develop/mos/messenger/messenger.md)
  * [API](develop/mos/mcs/api/api.md)
* OmniChain實例
  * [Omni App](develop/mos/examples/Omni-app.md)
  * [OmniDictionary](develop/mos/examples/OmniDictionary.md)

* API & SDK
  * [Butter SDK](sdk/butter.md)
  * [Atlas JSON RPC](sdk/RPC-API.md)
  * [Atlas Consensus API](sdk/ConsensusAPI.md)
  * [MAP掃描API](develop/sdk/scan-api.md)
* [最新接鏈進展以及對應地址](develop/chains-connect)

## 運行

* [如何成爲一個新的驗證者](run/HowToBecomeANewValidator.md)
* [如何成爲一個新的驗證者[進階版]](run/HowToBecomeANewValidatorAdvanced.md)
* [取出或撤回](run/Withdraw.md)
