# Map light client on near blockchain

## Contract Address

Map light client contract is deployed at address:

```
```

## Contract interface

```rust
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


### next_block_header_epoch

Get the epoch of the next block header which will update the validators.

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