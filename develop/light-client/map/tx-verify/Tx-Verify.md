# Cross Chain Transaction Verify struct

## MAP

a cross tx on the MAP mainnet, the relayer which watch the MAP will capture the tx and send the key information
to the ethereum.
 
key info details: 

```
    struct proveData {
        blockHeader header;
        txLogs logs;
        txProve prove;
    }

```

'proveData'.'header' is a block header 

```
    struct blockHeader {
        bytes parentHash;
        address coinbase;
        bytes root;
        bytes txHash;
        bytes receipHash;
        bytes bloom;
        uint256 number;
        uint256 gasLimit;
        uint256 gasUsed;
        uint256 time;
        bytes extraData;
        bytes mixDigest;
        bytes nonce;
        uint256 baseFee;
    }
```


'proveData'.'logs' is a transaction logs

```
     struct txLogs{
        bytes PostStateOrStatus;
        uint CumulativeGasUsed;
        bytes Bloom;
        Log[] logs;
    }
    //log struct    
    struct Log {
        address addr;
        bytes[] topics;
        bytes data;
    }
```

'proveData'.'prove' is a proof of transaction

```
    struct txProve{
        bytes keyIndex;
        bytes[] prove;
        bytes expectedValue;
    }

```
