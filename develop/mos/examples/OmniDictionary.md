# OmniDictionary 全鏈詞典



#### 簡要描述;簡介

OmniDictionary 是一個簡單的 dApp demo，演示瞭如何通過 mapo omnichain 服務合約處理跨鏈合約交互。 它基本上允許用戶發送請求以使用簡單的鍵及其對應的值在外部鏈上的字典（簡單映射）中添加新條目。



### 技術細節

為了實現跨鏈合約執行，開發者首先需要在一個邏輯合約中處理他們的執行邏輯，然後通過mapo omnichain服務合約通過'TransferOut'方法從源鏈發送生成的調用數據。

在我們的示例中，'TransferOut' 方法被調用如下，調用數據也在中間過程中生成：

```solidity
function sendDictionaryInput(uint256 _tochainId,bytes memory _target,string memory _key,string memory _val) external {

        bytes memory data = encodeDictionaryInput(_key,_val);

        IMOSV3.CallData memory cData = IMOSV3.CallData(_target,data,50000,0);

        require(
            IMOSV3(mos).transferOut(
                _tochainId,
                cData
            ),
            "send request failed"
        );
}
```

其次，信使獲取上述交易產生的事件並構建相應的證明，然後將它們打包在一起發送到 MAP 中繼鏈上的 mapo omnichain 服務合約：
```solidity
function transferOut(uint256 _toChain, CallData memory _callData) external override
    payable
    nonReentrant
    whenNotPaused
    checkBridgeable(Utils.fromBytes(_callData.target), _toChain)
    returns(bool)
    {
        require(_toChain != selfChainId, "Only other chain");
        require(_callData.gasLimit >= gasLimitMin ,"Execution gas too low");
        require(_callData.gasLimit <= gasLimitMax ,"Execution gas too high");

        require(_callData.value == 0,"Not supported at present value");

        (uint256 fee,address receiverFeeAddress) = feeService.getMessageFee(_toChain,_callData.target);
        //require(fee > 0,"Address has no message fee");
        uint256 amount = msg.value;
        require(amount == fee, "Need message fee");
        if (amount > 0) {
            TransferHelper.safeTransferETH(receiverFeeAddress, amount);
        }

        bytes32 orderId = _getOrderID(msg.sender, _callData.target, _toChain);

        bytes memory fromAddress = Utils.toBytes(msg.sender);

        bytes memory callData = abi.encode(_callData);

        emit mapMessageOut(selfChainId, _toChain, orderId,fromAddress,callData);
        return true;
    }
```

最後，信使將重複上述過程，並通過“TransferIn”方法將所有數據發送到目標鏈，該方法還將執行用戶輸入的調用數據：

```solidity
function transferIn(uint256 _chainId, bytes memory _receiptProof) external nonReentrant whenNotPaused {
        require(_chainId == relayChainId, "invalid chain id");
        (bool sucess, string memory message, bytes memory logArray) = lightNode.verifyProofData(_receiptProof);
        require(sucess, message);
        IEvent.txLog[] memory logs = EvmDecoder.decodeTxLogs(logArray);

        for (uint i = 0; i < logs.length; i++) {
            IEvent.txLog memory log = logs[i];
            bytes32 topic = abi.decode(log.topics[0], (bytes32));

            if (topic == EvmDecoder.MAP_MESSAGE_TOPIC && relayContract == log.addr) {
                (, IEvent.dataOutEvent memory outEvent) = EvmDecoder.decodeDataLog(log);

                if(outEvent.toChain == selfChainId){
                    _messageIn(outEvent);
                }
            }
        }
        emit mapTransferExecute(_chainId, selfChainId, msg.sender);
    }
```

目標字典最終由此設定

```solidity
function setDictionaryEntry(string memory _key,string memory _val) external returns(bool) {
        require(whitelist[msg.sender],"access denied");
        dictionary[_key] = _val;
        return true;
    }
```



以上所有代碼都可以在我們的 github repo 中找到。

* [OmniDictionary Example contract](https://github.com/mapprotocol/omnichain-examples/blob/main/evm/contracts/omniDictionary.sol)

* [MAP Omnichain Service contracts](https://github.com/mapprotocol/mapo-service-contracts/tree/main/evm/contracts)
