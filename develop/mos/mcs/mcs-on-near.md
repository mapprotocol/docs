# Map cross chain service (MCS) on near protocol

![MOS](MOS.png)

## Contract Address

[Here to get MAPO mainnet and testnet MCS contract address.](/develop/mos/mcs/README.md)



## Contract interface

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

## Interact with contract interface

### transfer_in

Transfer from Mapo to Near based on the proof data and event. Must attach enough NEAR funds to cover for storage of the proof.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| receipt_proof   | ReceiptProof      | the proof of one receipt |
| index     | usize      | the index of event among all the events in the receipt |


### transfer_out_token

Transfer out tokens to other blockchain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | String      | NEAR account of the mcs token contract |
| to     | Vec<u8>     | the target account address on target blockchain |
| amount   | U128      | the amount of token to transfer out |
| to_chain | u128      | the chain id of target blockchain |


### transfer_out_native

Transfer out native token to other blockchain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| to     | Vec<u8>     | the target account address on target blockchain |
| to_chain | u128      | the chain id of target blockchain |


### deposit_out_native

Deposit out native token to MAP blockchain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| to     | Vec<u8>     | the target account address on MAP blockchain |


## Management Interface

### init

Initialize the contract with configuration parameters.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| map_light_client   | String      | NEAR account of the MAP light client contract |
| map_bridge_address     | String      | the address of the MCS contract on MAP blockchain, in hex |
| wrapped_token     | String      | NEAR account of the wrap near contract |
| near_chain_id     | u128     | the chain id of the near blockchain |


### version

Get the version info of the current MCS contract.


### deploy_mcs_token

Deploy MCS token contract. The deployed token can be minted/burnt by MCS contract.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| name     | String     | the name of MCS token, the full address of the token will be <name>.<MCS contract address>|


### is_used_event

Check if the event is ussed or not.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| order_id   | &CryptoHash   | the order id of the event|

#### output parameters

| type         | comment |
| --------  | ------- |
| bool      |  true if the event is already used, false otherwise | 


### set_metadata

Get the record information( validators, threshold and epoch) for the epecified epoch.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| address   | String      | the contract address of the MCS token |
| name   | Option<String>      | the human-readable name of the token |
| symbol   | Option<String>      | the abbreviation, like wETH or AMPL |
| reference   | Option<String>      | a link to a valid JSON file containing various keys offering supplementary details on the token |
| reference_hash   | Option<String>      | the base64-encoded sha256 hash of the JSON file contained in the reference field |
| decimals   | Option<String>      | used in frontends to show the proper significant digits of a token |
| icon   | Option<String>      | a small image associated with this token |

### controller

Get the controller of the MCS contract.

#### output parameters

| type         | comment |
| --------  | ------- |
| Option<AccountId>      |  the controller account id of the MCS contract | 


### set_controller

Set the controller of the MCS contract.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| controller   | AccountId      | the controller account id of the MCS contract |


### controller_or_self

Check if the predecessor is controller or self.

#### output parameters

| type         | comment |
| --------  | ------- |
| bool      |  true if the predecessor is controller or self, false otherwise | 


### get_mcs_tokens

Get all deployed MCS tokens and their target chains.

#### output parameters

| type         | comment |
| --------  | ------- |
| Vec<(String, HashSet<u128>)>      |  deployed MCS tokens and their target chains | 


### get_fungible_tokens

Get all registered fungible tokens and their target chains.

#### output parameters

| type         | comment |
| --------  | ------- |
| Vec<(String, HashSet<u128>)>      |  registered fungible tokens and their target chains | 


### get_native_token_to_chains

Get all registered native token target chains.

#### output parameters

| type         | comment |
| --------  | ------- |
| HashSet<u128>      |  registered native token target chains | 


### add_native_to_chain

Add target chain for native token.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| to_chain   | u128      | target chain id |


### remove_native_to_chain

Remove target chain for native token.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| to_chain   | u128      | target chain id |


### add_mcs_token_to_chain

Allow the MCS token to be transfered to the target chain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | String      | MCS token address |
| to_chain   | u128      | target chain id |

### remove_mcs_token_to_chain

Remove the target chain id from the allow list of the MCS token.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | String      | MCS token address |
| to_chain   | u128      | target chain id |


### valid_mcs_token_out

Check if the MCS token is allowed to be transfered to the target chain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | &String      | MCS token address |
| to_chain   | u128      | target chain id |

#### output parameters

| type         | comment |
| --------  | ------- |
| bool      |  true if target chain is allowed, false otherwise  | 


### add_fungible_token_to_chain

Allow the fungible token to be transfered to the target chain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | String      | fungible token address |
| to_chain   | u128      | target chain id |

### remove_fungible_token_to_chain

Remove the target chain id from the allow list of the fungible token.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | String      | fungible token address |
| to_chain   | u128      | target chain id |


### valid_fungible_token_out

Check if the fungible token is allowed to be transfered to the target chain.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| token   | &String      | MCS token address |
| to_chain   | u128      | target chain id |

#### output parameters

| type         | comment |
| --------  | ------- |
| bool      |  true if target chain is allowed, false otherwise  | 



## Data structure


### Here are some main data structure for map light client contract.

MapCrossChainService is the map cross chain service contract.
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

ReceiptProof includes the proof and the receipt to prove.
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