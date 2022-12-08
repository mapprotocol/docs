# MAPO light client on near protocol

## Contract Address

[Here to get MAPO mainnet and testnet light client contract address.](/develop/light-client/README.md)


## Contract interface

```rust
impl MapLightClient {
     pub fn new(threshold: u128,
               validators: Vec<Validator>,
               epoch: u64,
               epoch_size: u64) -> Self;

    pub fn update_block_header(&mut self, header: &Header, agg_pk: G2);

    pub fn verify_proof_data(&self, receipt_proof: ReceiptProof);

    pub fn initialized() -> bool;

    pub fn next_block_header_epoch(&self) -> u64;

    pub fn get_epoch_size(&self) -> u64;

    pub fn get_record_for_epoch(&self, epoch: u64) -> Option<EpochRecord>;
}
```

## Interact with contract interface


### new

Initialize the contract with configuration parameters.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| threshold   | u128      | validators count |
| validators     | Vec<Validator>      | validators information |
| epoch     | u64      | the epoch which above validators work on |
| epoch_size     | u64     | the block count of one epoch |


### update_block_header

Get the last block header of each block, verify the signatures and update validators for the next epoch.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| header   | Header     | the last block header of one epoch |
| agg_pk     | G2      | the aggreated public key of the BLS signers |


### verify_proof_data

Verify the specified receipt is in the specified block.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| receipt_proof   | ReceiptProof      | the proof of one receipt |
| agg_pk     | G2      | the aggreated public key of the BLS signers |


### initialized

Check if the contract is intialized.

#### output parameters

| type         | comment |
| --------  | ------- |
| bool      |  true if the contract is already initialized, false otherwise| 


### get_header_height

Get the latest block header number the light client has updated.

#### output parameters

| type         | comment |
| --------  | ------- |
| u64      |  the epoch of the next block header which will update the validators| 


### get_epoch_size

Get the epoch size of the map blockchain.

#### output parameters

| type         | comment |
| --------  | ------- |
| u64      |  the epoch size of the map blockchain| 


### get_record_for_epoch

Get the record information( validators, threshold and epoch) for the epecified epoch.

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| epoch   | u64      | the specified epoch |

#### output parameters

| type         | comment |
| --------  | ------- |
| Option<EpochRecord>      |  the record of the specified epoch, maybe None| 

## Data structure


### Here are some main data structure for map light client contract.

MapLightClient is the map light client contract.
```rust
pub struct MapLightClient {
    // the epoch information records of latest 20 epochs
    epoch_records: UnorderedMap<u64, EpochRecord>,
    // the block count of one epoch
    epoch_size: u64,
    // the latest block header height that the contract has updated
    header_height: u64,
}
```

EpochRecord is the record for epoch information.
```rust
pub struct EpochRecord {
    // threshold for current epoch
    pub threshold: u128,
    // epoch id
    pub epoch: u64,
    // validators to verify the proof for current epoch
    pub validators: Vec<Validator>,
}
```

Validator is the data structure for each epoch validator.
```rust
pub struct Validator {
    // BLS G1 public key of current validater
    g1_pub_key: G1,
    // the weight of current validator
    weight: u128,
    // the secp256k1 address
    address: Address,
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