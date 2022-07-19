# near light client on map blockchain

## Contract Address

near light client contract is deployed at address:

```

```

## Contract interface

```

// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

interface ILightNode {
    function verifyProofData(bytes memory _receiptProof) external view returns (bool success, bytes memory logs);

    function updateBlockHeader(bytes memory _blockHeader) external;

    function headerHeight() external view returns(uint256);
}
```

## Interact with contract interface

### update_block_header

Get the last block header of each block, verify the signatures and update validators for the next epoch.

#### input parameters

| parameter         | type  | comment                                            |
| ----------------- | ----- | -------------------------------------------------- |
| _blockHeaderheade | bytes | borsh encode of the last block header of one epoch |

### verify_proof_data

Verify the specified receipt is in the specified block.

#### input parameters

| parameter     | type             | comment                                                                                                       |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------------- |
| receipt_proof | bytesReceiptProo | abi encode of borth encode of the proof of one receipt and borsh encode of the specified blockblock header  |


### headerHeight

Get the latest block header number the light client has updated.

#### output parameters

| type    | comment                                                     |
| ------- | ----------------------------------------------------------- |
| uint256 | the latest block header number the light client has updated |
