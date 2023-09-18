# Conflux Light Client

## How it works

ConfluxLightClient is a light client smart contract implemented in solidity on Mapo Relaychain

In the Hydra hard fork (v2.0) of Conflux, the introduction of CIP-43 has incorporated a POS (Proof of Stake) system to enhance the overall network's finality, resulting in Conflux network now operating on a hybrid consensus of POW (Proof of Work) and POS.

The light client will determine the storage of the final block header number and hash value after verifying the committee's voting weight and passing the aggregation BLS signatures. Subsequently, it will update the ledger data, structured as follows:

```
    struct LedgerInfoWithSignatures {
        uint64 epoch;
        uint64 round;
        bytes32 id;                 // block hash
        bytes32 executedStateId;   
        uint64 version;
        uint64 timestampUsecs;
        EpochState nextEpochState;  // only available for the last block of epoch
        Decision pivot;             // may be empty for epoch genesis block
        bytes32 consensusDataHash;
        bytes32[] accounts;
        bytes aggregatedSignature;
    }
```



## How to verify

#### updateBlockHeader

After storing the final block header number and hash value, we can easily verify the validity of previous block header numbers and hash values. To comprehensively consider proof verification, we can conveniently submit a series of block headers and hash values at intervals to facilitate swift verification.

```
    function updateBlockHeader(bytes memory _blockHeader) external override {
        bytes[] memory headers = abi.decode(_blockHeader, (bytes[]));
        relayPOW(headers);
    }

    function relayPOW(bytes[] memory headers) public override onlyInitialized whenNotPaused {
        Types.BlockHeaderWrapper memory head = _validateHeaders(headers);

        if (finalizedBlocks[head.height] == bytes32(0)) {
            _state.blocks++;
            finalizedBlocks[head.height] = keccak256(headers[0]);
        }

        removeBlockHeader(1);

        emit UpdateBlockHeader(tx.origin,head.height);
    }

    function _validateHeaders(bytes[] memory headers) private view returns (Types.BlockHeaderWrapper memory head) {
        require(headers.length > 0, "empty block headers");

        Types.BlockHeaderWrapper memory tail = Types.rlpDecodeBlockHeader(headers[headers.length - 1]);
        uint256 expectedBlockNumber = tail.height;
        bytes32 expectedBlockHash = finalizedBlocks[tail.height];
        require(expectedBlockHash != bytes32(0), "tail block not found");
        for (uint256 i = 0; i < headers.length; i++) {
            // validate in reverse order
            uint256 index = headers.length - 1 - i;
            Types.BlockHeaderWrapper memory header = Types.rlpDecodeBlockHeader(headers[index]);

            require(header.height == expectedBlockNumber, "block number mismatch");
            require(keccak256(headers[index]) == expectedBlockHash, "block hash mismatch");

            expectedBlockNumber--;
            expectedBlockHash = header.parentHash;
        }

        head = Types.rlpDecodeBlockHeader(headers[0]);
        require(head.height > _state.earliestBlockNumber, "block number too small");
    }
    
```

#### verify receipt

The light client follows these steps to verify the proof data:

1. Check that the <block number, block hash> pair of the block header in the proof data is recorded in the light client. If not, and error is returned.
2. Compute the hash of the block header in the proof data, and verify that it equals to the hash stored. This proves the validity of the block header.If not, an error is returned.
3. In Conflux, all receipt hash values form a Merkle Patricia Tree, and the root of this tree is recorded in the block header. The light client retrieves tree leaves from the block header, key index, and proof data through the root using the proof. It then verifies whether the tree leaves match the hash values of the receipts included in the proof data. If they do not match, an error is returned.

If all above verifications pass, the proof data is proved to be valid.

```
    function verifyReceiptProof(Types.ReceiptProof memory proof) public view override returns (bool) {
        Types.BlockHeaderWrapper memory head = _validateHeaders(proof.headers);

        return _mptVerify.proveReceipt(
            head.deferredReceiptsRoot,
            proof.blockIndex,
            proof.blockProof,
            proof.receiptsRoot,
            proof.index,
            proof.receipt,
            proof.receiptProof
        );
    }
```

## Proof

```
    struct ReceiptProof {
        // Continuous block headers (RLP encoded), that head is for receipts root,
        // and tail block should be relayed on chain.
        bytes[] headers;
        bytes blockIndex;
        ProofLib.ProofNode[] blockProof;
        bytes32 receiptsRoot;
        bytes index;
        bytes receipt; // RLP encoded
        ProofLib.ProofNode[] receiptProof;
    }
    
    struct TxReceipt {
        uint256 accumulatedGasUsed;
        uint256 gasFee;
        bool gasSponsorPaid;
        bytes logBloom;
        TxLog[] logs;
        uint8 outcomeStatus;
        bool storageSponsorPaid;
        StorageChange[] storageCollateralized;
        StorageChange[] storageReleased;
    }

    struct TxLog {
        address addr;
        bytes32[] topics;
        bytes data;
        uint8 space; // Native: 1, Ethereum: 2
    }

    struct StorageChange {
        address account;
        uint64 collaterals;
    }
    

```