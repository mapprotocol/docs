# ＃MAPO全鏈服務（MOS）消息傳遞

## 什麼是MOS全鏈消息

MOS全鏈消息使建立在一個鏈上的項目可以很容易地將一些項目信息同步到其他鏈上，也可以調用其他連接鏈上的合約方法。

MOS使用MAP協議輕型客戶端來驗證跨鏈消息的交易，以確保跨鏈消息的真實性和在鏈上可查。

使用MOS可以實現與兩個鏈的互操作：

- 從A鏈調用B鏈上的合約。
- 將A鏈中的消息變化打包，寫入B鏈中，實現消息同步

## 如何運作的

請看這裏的[細節](/develop/mos/message/cross-chain-message.md)。

## 在MOS全鏈消息上構建dApps
這裏給出了一些例子，說明如何在MOS消息上構建dapps。
* [OmniDictionary](/develop/mos/examples/OmniDictionary.md)