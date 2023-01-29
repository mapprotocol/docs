# Near Protocol Light Client

## How it works

NearProtocolLightClient is an implementation of the [NEAR light client](https://nomicon.io/ChainSpec/LightClient) in Solidity as an Mapo contract. The state of the light client is defined by:

1. `BlockHeaderInnerLiteView` for the current head (which contains `height`, `epoch_id`, `next_epoch_id`, `prev_state_root`, `outcome_root`, `timestamp`, the hash of the block producers set for the next epoch `next_bp_hash`, and the merkle root of all the block hashes `block_merkle_root`);
2. The set of block producers for the current and next epochs.

The `epoch_id` refers to the epoch to which the block that is the current known head belongs, and `next_epoch_id` is the epoch that will follow.

Light clients operate by periodically fetching instances of `LightClientBlockView` via particular RPC end-point described [here](https://nomicon.io/ChainSpec/LightClient#rpc-end-points).

Light client doesn't need to receive `LightClientBlockView` for all the blocks. Having the `LightClientBlockView` for block `B` is sufficient to be able to verify any statement about state or outcomes in any block in the ancestry of `B` (including `B` itself). In particular, having the `LightClientBlockView` for the head is sufficient to locally verify any statement about state or outcomes in any block on the canonical chain.

However, to verify the validity of a particular `LightClientBlockView`, the light client must have verified a `LightClientBlockView` for at least one block in the preceding epoch, thus to sync to the head the light client will have to fetch and verify a `LightClientBlockView` per epoch passed.

## How to verify

#### updateBlockHeader

```solidity
struct LightClientBlock {
        bytes32 prev_block_hash;
        bytes32 next_block_inner_hash;
        BlockHeaderInnerLite inner_lite;
        bytes32 inner_rest_hash;
        OptionalBlockProducers next_bps;
        OptionalSignature[] approvals_after_next;
        bytes32 hash;   
        bytes32 next_hash;
}

struct BlockHeaderInnerLite {
        uint64 height; // Height of this block since the genesis block (height 0).
        bytes32 epoch_id; // Epoch start hash of this block's epoch. Used for retrieving validator information
        bytes32 next_epoch_id;
        bytes32 prev_state_root; // Root hash of the state at the previous block.
        bytes32 outcome_root; // Root of the outcomes of transactions and receipts.
        uint64 timestamp; // Timestamp at which the block was built.
        bytes32 next_bp_hash; // Hash of the next epoch block producers set
        bytes32 block_merkle_root;
        bytes32 hash; // Additional computable element
}

struct PublicKey {
        bytes32 k;
}

struct Signature {
        bytes32 r;
        bytes32 s;
}

struct BlockProducer {
        PublicKey publicKey;
        uint128 stake;
        // Flag indicating if this validator proposed to be a chunk-only producer (i.e. cannot become a block producer).
        bool isChunkOnly;
}

struct OptionalBlockProducers {
        bool some;
        BlockProducer[] blockProducers;
        bytes32 hash; // Additional computable element
}

struct OptionalSignature {
        bool some;
        Signature signature;
}



```

The fields `prev_block_hash`, `next_block_inner_hash` and `inner_rest_hash` are used to reconstruct the hashes of the current and next block, and the approvals that will be signed, in the following way (where `block_view` is an instance of `LightClientBlockView`):

```solidity
hash = sha256(
            abi.encodePacked(sha256(abi.encodePacked(sha256(borsh(inner_lite), inner_rest_hash)), prev_block_hash)
        );

next_hash = sha256(abi.encodePacked(next_block_inner_hash,hash));

approval_message =  abi.encodePacked(
                uint8(0),
                nextHash,
                Utils.swapBytes8(blockHeight + 2),
                bytes23(0)
            )
```

The light client updates its head with the information from `LightClientBlockView` iff:

1. The height of the block is higher than the height of the current head;
2. The epoch of the block is equal to the `next_epoch_id` known for the current head;
3. If the epoch of the block is equal to the `next_epoch_id` of the head, then `next_bps` is not `None`;
4. `approvals_after_next` contain valid signatures on `approval_message` from the block producers of the corresponding epoch (see next section);
5. The signatures present in `approvals_after_next` correspond to more than 2/3 of the total stake (see next section).
6. If `next_bps` is not none, `sha256(borsh(next_bps))` corresponds to the `next_bp_hash` in `inner_lite`.

   ```solidity
     // 1
     require(nearBlock.inner_lite.height > curHeight,"New block must have higher height");
     //2
     require(nearBlock.inner_lite.epoch_id == nextEpochId,"initialized or unknown epoch");
     //3 
     require(nearBlock.next_bps.some,"Next next_bps should not be None");
     //4
     if (approval.some) {
                   bool check = Ed25519Verify.checkBlockProducerSignatureInHead(
                       thisEpoch.keys[i],
                       approval.signature.r,
                       approval.signature.s,
                       nearBlock.next_hash,
                       nearBlock.inner_lite.height
                   );
                   if (!check) {
                       return (false, "Invalid Signature");
                   }
       }
      //5 
       uint256 votedFor = 0;
           for ((uint256 i, uint256 cnt) = (0, thisEpoch.numBPs); i != cnt; ++i) {
               bytes32 stakes = thisEpoch.packedStakes[i >> 1];
               if (nearBlock.approvals_after_next[i].some) {
                   votedFor += uint128(bytes16(stakes));
               }
               if (++i == cnt) {
                   break;
               }
               if (nearBlock.approvals_after_next[i].some) {
                   votedFor += uint128(uint256(stakes));
               }
           }

           if (votedFor <= thisEpoch.stakeThreshold) {
               return (false, "Too few approvals");
           }
   //6 
     require(nearBlock.next_bps.hash == nearBlock.inner_lite.next_bp_hash,"Hash of block producers does not match");
   ```

   To simplify the protocol we require that the next block and the block after next are both in the same epoch as the block that `LightClientBlockView` corresponds to. It is guaranteed that each epoch has at least one final block for which the next two blocks that build on top of it are in the same epoch.

   By construction by the time the `LightClientBlockView` is being validated, the block producers set for its epoch is known. Specifically, when the first light client block view of the previous epoch was processed, due to (3) above the `next_bps` was not `None`, and due to (6) above it was corresponding to the `next_bp_hash` in the block header.

   The sum of all the stakes of `next_bps` in the previous epoch is `total_stake` referred to in (5) above.

   The signatures in the `LightClientBlockView::approvals_after_next` are signatures on `approval_message`. The `i`-th signature in `approvals_after_next`, if present, must validate against the `i`-th public key in `next_bps` from the previous epoch. `approvals_after_next` can contain fewer elements than `next_bps` in the previous epoch.

   `approvals_after_next` can also contain more signatures than the length of `next_bps` in the previous epoch. This is due to the fact that, as per [consensus specification](https://nomicon.io/ChainSpec/Consensus), the last blocks in each epoch contain signatures from both the block producers of the current epoch, and the next epoch. The trailing signatures can be safely ignored by the light client implementation.

#### verifyProofData

To verify that a transaction or receipt happens on chain, a light client can request a proof through rpc by providing receipt_id and the block hash of light client head. The rpc will return the following struct

```solidity

struct FullOutcomeProof {
        ExecutionOutcomeWithIdAndProof outcome_proof;
        MerklePath outcome_root_proof; 
        BlockHeaderLight block_header_lite;
        MerklePath block_proof;
}
struct ExecutionOutcomeWithIdAndProof {
        MerklePath proof;
        bytes32 block_hash;
        ExecutionOutcomeWithId outcome_with_id;
}
struct BlockHeaderLight {
        bytes32 prev_block_hash;
        bytes32 inner_rest_hash;
        NearDecoder.BlockHeaderInnerLite inner_lite;
        bytes32 hash; // Computable
}
struct ExecutionStatus {
        uint8 enumIndex;
        bool unknown;
        bool failed;
        bytes successValue; // The final action succeeded and returned some value or an empty vec.
        bytes32 successReceiptId; // The final action of the receipt returned a promise or the signed transaction was converted to a receipt. Contains the receipt_id of the generated receipt.
}
struct ExecutionOutcome {
        bytes[] logs; // Logs from this transaction or receipt.
        bytes32[] receipt_ids; // Receipt IDs generated by this transaction or receipt.
        uint64 gas_burnt; // The amount of the gas burnt by the given transaction or receipt.
        uint128 tokens_burnt; // The total number of the tokens burnt by the given transaction or receipt.
        bytes executor_id; // Hash of the transaction or receipt id that produced this outcome.
        ExecutionStatus status; // Execution status. Contains the result in case of successful execution.
        bytes32[] merkelization_hashes;
}

struct ExecutionOutcomeWithId {
        bytes32 id; // The transaction hash or the receipt ID.
        ExecutionOutcome outcome;
        bytes32 hash;
}

struct MerklePathItem {
        bytes32 hash;
        uint8 direction; // 0 = left, 1 = right
}
struct MerklePath {
        MerklePathItem[] items;
}
```

which includes everything that a light client needs to prove the execution outcome of the given transaction or receipt.

The proof verification can be broken down into two steps, execution outcome root verification and block merkle root verification.

###### Execution Outcome Root Verification

If the outcome root of the transaction or receipt is included in block `H`, then `outcome_proof` includes the block hash of `H`, as well as the merkle proof of the execution outcome in its given shard. The outcome root in `H` can be reconstructed by

```solidity
    bytes32 hash = _computeRoot(
            fullOutcomeProof.outcome_proof.outcome_with_id.hash,
            fullOutcomeProof.outcome_proof.proof
        );

        hash = sha256(abi.encodePacked(hash));

        hash = _computeRoot(hash, fullOutcomeProof.outcome_root_proof);

        if (
            hash != fullOutcomeProof.block_header_lite.inner_lite.outcome_root
        ) {
            return (false, "outcome merkle proof is not valid");
        }
```

This outcome root must match the outcome root in `block_header_lite.inner_lite`.

###### Block Merkle Root Verification

Recall that block hash can be computed from `LightClientBlockLiteView` by

```solidity
hash = sha256(
            abi.encodePacked(sha256(abi.encodePacked(sha256(borsh(inner_lite), inner_rest_hash)), prev_block_hash)
        );
```

The expected block merkle root can be computed by

```solidity
 if (
            _computeRoot(
                fullOutcomeProof.block_header_lite.hash,
                fullOutcomeProof.block_proof
            ) != block_merkle_root
        ) {
            return (false, "block proof is not valid");
        }
```

which must match the block merkle root in the light client block of the light client head.

## Proof

###### LightClientBlock

```solidity
struct LightClientBlock {
        bytes32 prev_block_hash;
        bytes32 next_block_inner_hash;
        BlockHeaderInnerLite inner_lite;
        bytes32 inner_rest_hash;
        OptionalBlockProducers next_bps;
        OptionalSignature[] approvals_after_next;
        bytes32 hash;   
        bytes32 next_hash;
}


```

###### Proof of transation

```solidity
struct FullOutcomeProof {
        ExecutionOutcomeWithIdAndProof outcome_proof;
        MerklePath outcome_root_proof; 
        BlockHeaderLight block_header_lite;
        MerklePath block_proof;
}
```
