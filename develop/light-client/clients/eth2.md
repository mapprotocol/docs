# Eth2.0 PoS light client

## How it works

Eth2.0 light client is a light client which aims to verify the receipt in the execution layer blocks after MERGE. It is
divided into 2 parts:

1. the solidity contract part which implemented the main light client logic;
2. the precompiled contract part which verifies the finality of the beacon block header and the next sync committee, and
   also
   verifies the aggregated BLS signature of the attested beacon block header.

Here is the main workflow about how it works:

1. The deployment scripts get the light client bootstrap information based on the trusted block root through beacon
   chain RPC, also the scripts get some other necessary information, and then deploy and initialize the light client
   with the obtained information.
2. The light client receives the light client update from an off-chain program called **maintainer**. The
   data structure of the light client update is as follows:
    ```solidity
    struct LightClientUpdate {
        BeaconBlockHeader attestedHeader;  // attested beacon block header which is signed by the sync committee of one period
        SyncCommittee nextSyncCommittee;   // the sync committee for the next period, may be empty
        bytes32[] nextSyncCommitteeBranch; // the merkle proof of nextSyncCommittee
        BeaconBlockHeader finalizedHeader; // finalized beacon block header in the state of the attested header
        bytes32[] finalityBranch;          // the merkle proof of finalizedHeader
        BlockHeader finalizedExeHeader;    // the finalized execution layer block header corresponding to the finalized beacon header
        bytes32[] exeFinalityBranch;       // the proof of finalizedExeHeader
        SyncAggregate syncAggregate;       // the aggregated signature of the attested beancon header
        uint64 signatureSlot;              // in which slot the aggregated signature is signed
    }
    ```
   The light client calls the precompiled contract to verify the finality of the beacon block header and execution
   layer block header, also verifies the next sync committee if it exists, and verifies the aggregated BLS signature
   of the attested block header. Once all verifications pass, the light client stores the next sync committee, updates
   the finalized beacon chain slot, and the finalized execution layer block header number and hash.
3. Everytime the **maintainer** updates the light client successfully, the finalized beacon chain slot and finalized
   execution layer header number are updated. There is a gap between the latest finalized execution layer header and
   the previous finalized header. The **maintainer** should update the execution layer block headers to the light
   client, and the light client will verify the validity of the block headers and store the hashes for further proof
   verification. The maintainer could not do the next light client update util the gap is filled.
4. The light client can verify the validity of the receipt in a certain ethereum execution layer block only if the hash
   of the block is stored and the gap it locates is filled. So if an application want to use the eth2.0 light client to
   verify the proof, it can first check if the corresponding header is verifiable by getting the verifiable header range
   from the eth2.0 light client.

## How to verify

The content of proof data includes:

1. Ethereum execution layer block header where the receipt exists
2. The receipt to prove
3. The index of the receipt in the block
4. The proof to prove the existence of the above receipt

The light client follows these steps to verify the proof data:

1. Check that the <block number, block hash> pair of the block header in the proof data is recorded in the light client.
   If not, and error is returned.
2. Compute the hash of the block header in the proof data, and verify that it equals to the hash stored. This proves the
   validity of the block header.If not, an error is returned.
3. All the receipts hashes in an ethereum execution layer block constructs a Merkle Patricia Tree, and the tree root is
   recorded in the block header. The light client retrieves the tree leaf through the tree root from block header,the
   key index and the proof in the proof data. Then it checks whether the tree leaf equals to the hash of the receipt
   included in the proof data. If not, an error is returned.

If all above verifications pass, the proof data is proved to be valid.

## Proof

### Here is the data structure about the proof.

ReceiptProof includes the proof and the receipt to prove.

```solidity
   struct ReceiptProof {
      BlockHeader header;  // the block header where the receipt exists
      TxReceipt txReceipt; // the receipt to prove
      bytes keyIndex;      // the index of the receipt in the block
      bytes[] proof;       // the proof to prove the existence of the above receipt
   }
```
