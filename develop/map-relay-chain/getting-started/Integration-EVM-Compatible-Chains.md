# MAP Protocol 對接EVM兼容鏈

MAP Protocol 的跨鏈過程涉及多個步驟，從鎖定資產到驗證數據，確保資產在不同區塊鏈之間的安全傳輸和互通，這裏我們僅討論EMV兼容鏈的接入過程。完成以下幾個模塊的開發和部署即可接入到 MAP Protocol。

## Light-client 層

由於接入鏈與Mapo Protocol上的其他鏈的消息跨鏈都是通過`map-relay-chain`作爲中轉，所以接入鏈只需要部署`map-relay-chain`的`light-client`就可以實現來自於`map-relay-chain`的跨鏈消息的驗證。由於`map-relay-chain`已經實現了solidity版本的`light-client`，故接入鏈只需要專注於實現自己的solidity的`light-client`, 接入鏈的`light-client`至少需要滿足兩個功能:

+ 維持和更新`light-client`的狀態,即保存一定數量的區塊頭及持續更新校驗新的區塊頭。
+ 可以根據`light-client`的當前狀態，驗證源鏈上的合約事件功能，通常是交易的收據的驗證（MPT的驗證信息）。

### light-client合約開發:

	要 MAP Protocol 協議，接入鏈方需要滿足 MAP Protocol 定義的 [ILightNode接口](https://github.com/mapprotocol/map-contracts/blob/main/protocol/contracts/interface/ILightNode.sol),其中主要滿足以下接口:

```solidity

// Verify the validity of the transaction according to the header, receipt
// The interface will be updated later to return logs

function verifyProofData(bytes memory _receiptProof) external view returns (bool success, string memory message, bytes memory logs);

function updateBlockHeader(bytes memory _blockHeader) external;

function updateLightClient(bytes memory _data) external;

``` 

### 合約部署
   
+ 在 map-relay-chain 上部署接入鏈的`light-client`合約.
+ 在 接入鏈上部署 map-relay-chain 的[light-clinet](https://github.com/mapprotocol/map-contracts/tree/main/mapclients)合約.


### Maintainer 開發

Maintainer服務是一個獨立的程序,用於更新同步`light-client`的狀態,向源鏈和目標鏈上的`light-client`提交對應鏈上的區塊頭數據. 由於 Maintainer 服務已經支持了map-relay-chain,所以接入鏈的開發者只需要在Maintainer服務中增加對自己鏈的支持即可. 接入鏈的開發者可以fork一個 MAP Protocol 提供的 [Maintainer](https://github.com/mapprotocol/compass) 做二次開發以增加對自己鏈的支持。

+ 獲取當前`light-client`的狀態
+ 根據當前`light-client`的狀態獲取對應鏈的區塊頭數據並提交到`light-client`


## MOS 層

MOS 層定義了 MAP Protocol 通用消息跨鏈的框架結構及實現邏輯，接入鏈方的開發者不需要再實現該模塊，而可以直接部署使用，MOS 層需要在跨鏈雙方的鏈上都部署，由於是EVM兼容鏈，這裏我們實現的Mos是solidity版本，其安裝部署流程參考[這裏](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md).

主要數據結構和[接口](https://github.com/mapprotocol/mapo-service-contracts/tree/main/evm/contracts/interface):

```solidity
 enum MessageType {
        CALLDATA,
        MESSAGE
    }

    // @notice This is the configuration you need across the chain.
    // @param relay - When it is true, the relay chain is required to perform a special execution to continue across the chain.
    // @param msgType - Different execution patterns of messages across chains.
    // @param target - The contract address of the target chain.
    // @param payload - Cross-chain content.
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    // @param value - Collateral value cross-chain, currently not supported, default is 0.
    struct MessageData {
        bool relay;
        MessageType msgType;
        bytes target;
        bytes payload;
        uint256 gasLimit;
        uint256 value;
    }

    // @notice Gets the fee to cross to the target chain.
    // @param toChain - Target chain chainID.
    // @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);


    // @notice Initiate cross-chain transactions. Generate cross-chain logs.
    // @param toChain - Target chain chainID.
    // @param messageData - Structure MessageData encoding.
    // @param feeToken - In what Token would you like to pay the fee.
    function transferOut(uint256 toChain, bytes memory messageData, address feeToken) external payable  returns(bytes32);


    // @notice Add the fromaddress permission.
    // @param fromChain - The chainID of the source chain.
    // @param fromAddress - The call address of the source chain.
    // @param tag - Permission,false: revoke permission.
    function addRemoteCaller(uint256 fromChain, bytes memory fromAddress, bool tag) external;

    // @notice Query whether the contract has execution permission.
    // @param mosAddress - This is the mos query address.
    // @param fromChainId - The call chain id of the source chain.
    // @param fromAddress - The call address of the source chain.
    function getExecutePermission(address mosAddress,uint256 fromChainId,bytes memory fromAddress) external view returns(bool);

    event mapMessageOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData);

    event mapMessageIn(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData, bool result, bytes reason);
```


### MOS 合約部署
   
在map-relay-chain上部署mos合約:
+ 下載[mos庫](https://github.com/mapprotocol/mapo-service-contracts)。
+ 前往evm文件夾。
+ 按照[README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md)中提供的部署說明部署mos合約。
  
在接入鏈上部署mos合約:   
+ 下載[mos庫](https://github.com/mapprotocol/mapo-service-contracts)。
+ 前往evm文件夾。
+ 按照[README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md)中提供的部署說明部署mos合約。

MOS 合約部署後可以通過`setLightClient`方法設置對應`light-client`合約地址.


### Messeager 程序開發

Messenger服務是一個獨立的程序，旨在監控並路由源鏈和目標鏈上`mos合約`的特定事件。這些事件包括常見的消息事件，如`mapMessageOut`和`mapMessageIn`。Messenger服務爲這些事件構建相應的證明數據，並最終將跨鏈消息以及證明數據提交到目標鏈。由於 Messenger 服務已經支持`map-relay-chain`，集成鏈的開發者只需要在 Messenger 服務中添加對自己鏈的支持。開發者可以fork一個 MAP Protocol 提供的[Messenger服務](https://github.com/mapprotocol/compass)，並自定義以添加對自己鏈的支持。

在這個過程中，Messenger服務在促進跨鏈消息及其相關證明的傳輸中扮演關鍵角色，確保信息在源鏈和目標鏈之間安全可靠地傳輸。它抽象了與智能合約交互和處理事件的複雜性，使開發者更容易將自己的鏈集成到 MAP Protocol 框架中。

## 應用層

應用層代表了跨鏈框架的真正業務邏輯。用戶在該層定義具體的業務邏輯，如資產管理以及鎖定、解鎖、鑄造、銷燬等操作。實際的跨鏈操作發生在應用層中，其中會調用 MOS 層的transferOut接口，將具體的跨鏈消息寫入鏈上。

以下是應用層內的跨鏈過程流程：

+ 用戶交互：用戶與應用層進行交互，啓動跨鏈操作，如在鏈之間鎖定、解鎖或轉移資產。

+ 調用transferOut：當用戶啓動跨鏈操作時，應用層會調用`Mos`層的`transferOut`接口。該接口構建和格式化跨鏈消息，包括在目標鏈上執行的操作的細節。

+ 路由消息：一旦跨鏈消息構建完成，Messenger服務會被通知(監聽)。Messenger服務收集必要的證明數據，並將跨鏈消息與證明一起提交到目標鏈。

+ 目標鏈驗證：在目標鏈上，使用爲源鏈部署的`light-client`來驗證接收到的跨鏈消息的真實性和合法性。`light-client`確保數據與源鏈的數據一致，確認消息的有效性。

+ 執行和操作：在成功驗證後，目標鏈上的應用層解碼接收到的消息並執行相應的操作，如鑄造新代幣、解鎖鎖定的資產等。

應用層充當用戶意圖與跨鏈通信的技術複雜性之間的橋樑。它提供了用戶友好的界面，讓用戶觸發跨鏈操作，並確保這些操作在涉及的鏈之間安全執行和驗證。
