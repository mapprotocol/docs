# 在 near 協議上映射跨鏈服務 (MCS)

![MOS](MOS.png)

## 合約地址

[此處獲取MAPO主網和測試網MCS合約地址。](/develop/mos/mcs/README.md)



## 合約界面

```rust

impl MapCrossChainService {
    pub fn init(map_light_client: String, map_bridge_address: String, wrapped_token: String, near_chain_id: u128) -> Promise;

    pub fn version() -> &'static str;

    #[payable]
    pub fn transfer_in(&mut self, receipt_proof: ReceiptProof, index: usize) -> Promise;

    pub fn transfer_out_token(&mut self, token: String, to: Vec<u8>, amount: U128, to_chain: u128) -> Promise;

    #[payable]
    pub fn transfer_out_native(&mut self, to: Vec<u8>, to_chain: u128) -> Promise;

    #[payable]
    pub fn deposit_out_native(&mut self, to: Vec<u8>);

    #[payable]
    pub fn deploy_mcs_token(&mut self, name: String) -> Promise;

    pub fn is_used_event(&self, order_id: &CryptoHash) -> bool;

    pub fn set_metadata(
        &mut self,
        address: String,
        name: Option<String>,
        symbol: Option<String>,
        reference: Option<String>,
        reference_hash: Option<Base64VecU8>,
        decimals: Option<u8>,
        icon: Option<String>,
    ) -> Promise;

    pub fn controller(&self) -> Option<AccountId>;

    pub fn set_controller(&mut self, controller: AccountId);

    pub fn controller_or_self(&self) -> bool;

    pub fn get_mcs_tokens(&self) -> Vec<(String, HashSet<u128>)>;

    pub fn get_fungible_tokens(&self) -> Vec<(String, HashSet<u128>)>;

    pub fn get_native_token_to_chains(&self) -> HashSet<u128>;

    pub fn add_native_to_chain(&mut self, to_chain: u128);

    pub fn remove_native_to_chain(&mut self, to_chain: u128);

    pub fn add_mcs_token_to_chain(&mut self, token: String, to_chain: u128);

    pub fn remove_mcs_token_to_chain(&mut self, token: String, to_chain: u128);

    pub fn valid_mcs_token_out(&self, token: &String, to_chain: u128) -> bool;

    pub fn add_fungible_token_to_chain(&mut self, token: String, to_chain: u128) -> PromiseOrValue<()>;

    pub fn remove_fungible_token_to_chain(&mut self, token: String, to_chain: u128);

    pub fn valid_fungible_token_out(&self, token: &String, to_chain: u128) -> bool;
}
```

## 與合約接口交互

### 轉入

根據證明數據和事件從 Mapo 轉移到 Near。 必須附加足夠的 NEAR 資金來支付證明的存儲費用。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| receipt_proof   | ReceiptProof      | the proof of one receipt |
| index     | usize      | the index of event among all the events in the receipt |


### transfer_out_token

Transfer out tokens to other blockchain.

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| token   | String      | NEAR account of the mcs token contract |
| to     | Vec<u8>     | the target account address on target blockchain |
| amount   | U128      | the amount of token to transfer out |
| to_chain | u128      | the chain id of target blockchain |


### transfer_out_native

Transfer out native token to other blockchain.

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| to     | Vec<u8>     | the target account address on target blockchain |
| to_chain | u128      | the chain id of target blockchain |


### deposit_out_native

Deposit out native token to MAP blockchain.

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| to     | Vec<u8>     | the target account address on MAP blockchain |


## 管理界面

### 初始化

使用配置參數初始化合約。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| map_light_client   | String      | MAP 輕客戶端合約的 NEAR 賬戶 |
| map_bridge_address     | String      | MOS 合約在 MAP 區塊鏈上的十六進制地址 |
| wrapped_token     | String      |wrap near 合約的 NEAR 賬戶 |
| near_chain_id     | u128     |NEAR 區塊鏈的鏈ID|


### 版本

獲取當前 MCS 合約的版本信息。


### deploy_mcs_token

部署 MOS 代幣合約。 部署的代幣可以由 MOS 合約鑄造/銷毀。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| name     | String     | MOS代幣的名稱，代幣的完整地址為<名稱>.<MOS合約地址>|


### is_used_event

檢查事件是否被使用。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| order_id   | &CryptoHash   | event事件的訂單id|

#### 輸出參數

| type         | comment |
| --------  | ------- |
| bool      |  如果事件已被使用，則為 true，否則為 false | 


### set_metadata

獲取指定紀元的記錄信息（驗證者、閾值和紀元）。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| address   | String      | MOS 代幣合約地址 |
| name   | Option<String>      | 代幣可讀名字 |
| symbol   | Option<String>      | 略縮形式例如wETH 或者 AMPL |
| reference   | Option<String>      | 指向包含各種密鑰的有效 JSON 文件的鏈接，提供有關令牌的補充詳細信息 |
| reference_hash   | Option<String>      | 引用字段中包含的 JSON 文件的 base64 編碼的 sha256 哈希|
| decimals   | Option<String>      | 在前端用於顯示令牌的正確有效數字 |
| icon   | Option<String>      | 與此代幣關聯的頭像 |

### 控制帳戶 controller

獲取MOS 合約的控制帳戶 ID

#### 輸出參數

|  類型 | 備註  |
| --------  | ------- |
| Option<AccountId>      |  MOS 合約的控制帳戶 ID | 


### set_controller

設置 MCS 合約的控制帳戶。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| controller   | AccountId      | MOS 合約的控制帳戶 ID|


### controller_or_self

檢查前任是控制者還是自己。

#### 輸出參數

| type         | comment |
| --------  | ------- |
| bool      | 如果前任是控制者或自己，則為 true，否則為 false| 


### get_mcs_tokens

獲取已部署的所有MOS代幣和其目標鏈 

#### 輸出參數

| type         | comment |
| --------  | ------- |
| Vec<(String, HashSet<u128>)>      |  已部署的MOS代幣和其目標鏈  | 


### get_fungible_tokens

獲取所有可置換代幣和其目標鏈 

#### 輸出參數

| type         | comment |
| --------  | ------- |
| Vec<(String, HashSet<u128>)>      |  已註冊可置換代幣和其目標鏈 | 


### get_native_token_to_chains

獲取所有已註冊的本地代幣目標鏈。

#### 輸出參數

| 類型 | 備註 |
| --------  | ------- |
| HashSet<u128>      |  註冊原生代幣目標鏈 | 


### add_native_to_chain

為原生代幣添加目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| to_chain   | u128      | 目標鏈 id |


### remove_native_to_chain

刪除本地代幣的目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註|
| -------- | ------------ | ------- |
| to_chain   | u128      | 目標鏈 id  |


### add_mcs_token_to_chain

允許 MOS 代幣轉移到目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| token   | String      | MOS代幣地址  |
| to_chain   | u128      | 目標鏈 id  |

### remove_mcs_token_to_chain

從 MOS 代幣的允許列表中刪除目標鏈 ID。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| token   | String      | MOS代幣地址  |
| to_chain   | u128      | 目標鏈 id  |


### valid_mcs_token_out

檢查是否允許將代幣轉移到目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註 |
| -------- | ------------ | ------- |
| token   | &String      | MOS代幣地址 |
| to_chain   | u128      | 目標鏈 id |

#### 輸出參數

類型 | 備註 |
| --------  | ------- |
| bool      |  如果允許目標鏈，則為 true，否則為 false | 


### add_fungible_token_to_chain
允許將可置換代幣轉移到目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註  |
| -------- | ------------ | ------- |
| token   | String      | 可置換代幣的地址 |
| to_chain   | u128      | 目標鏈 id |

### remove_fungible_token_to_chain

從可替代代幣的允許列表中刪除目標鏈 ID。

#### 輸入參數

| 參數 | 類型 | 備註  |
| -------- | ------------ | ------- |
| token   | String      | 可置換代幣的地址 |
| to_chain   | u128      | 目標鏈 id |


### valid_fungible_token_out

檢查是否允許將代幣轉移到目標鏈。

#### 輸入參數

| 參數 | 類型 | 備註   |
| -------- | ------------ | ------- |
| token   | &String      | MOS 代幣地址 |
| to_chain   | u128      | 目標鏈 id |

#### 輸出參數

|  類型 | 備註   |
| --------  | ------- |
| bool      |  如果允許目標鏈，則為 true，否則為 false| 


## 數據結構


### 以下是地圖燈客戶端合約的一些主要數據結構。

MapCrossChainService 是地圖跨鏈服務合約。
```rust

pub struct MapCrossChainService {
    /// The account of the map light client that we can use to prove
    pub map_client_account: AccountId,
    /// Address of the MAP bridge contract.
    pub map_bridge_address: Address,
    /// Set of created MCSToken contracts.
    pub mcs_tokens: UnorderedMap<String, HashSet<u128>>,
    /// Set of other fungible token contracts.
    pub fungible_tokens: UnorderedMap<String, HashSet<u128>>,
    /// Map of other fungible token contracts and their min storage balance.
    pub fungible_tokens_storage_balance: UnorderedMap<String, u128>,
    /// Map of token contracts and their decimals
    pub token_decimals: UnorderedMap<String, u8>,
    /// Set of other fungible token contracts.
    pub native_to_chains: HashSet<u128>,
    /// Hashes of the events that were already used.
    pub used_events: UnorderedSet<CryptoHash>,
    /// Public key of the account deploying the MCS contract.
    pub owner_pk: PublicKey,
    /// Balance required to register a new account in the MCSToken
    pub mcs_storage_transfer_in_required: Balance,
    // Wrap token for near
    pub wrapped_token: String,
    // Near chain id
    // FIXME: get from env?
    pub near_chain_id: u128,
    // Nonce to generate order id
    pub nonce: u128,
    /// Mask determining all paused functions
    paused: Mask,
}
```

ReceiptProof包括證明和收據證明。
```rust
pub struct ReceiptProof {
    // the block header where the receipt exists
    pub header: Header,
    // the aggregated G2 public key of the signed validators
    pub agg_pk: G2,
    // the receipt to prove
    pub receipt: Receipt,
    // the index of the receipt in the block
    pub key_index: Vec<u8>,
    // the proof to prove the existance of the above receipt
    pub proof: Vec<ProofEntry>,
}
```