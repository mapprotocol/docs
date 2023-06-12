# Marker genesis 創世標記符

`Marker genesis 創世標記符` 是一個開發者工具，可以輕鬆運行Atlas區塊鏈測試網和圍繞測試網的相關工作。

與之前的解決方案相比，它的主要優勢在於它能夠創建一個`genesis.json`，所有的核心合約都已經部署在其中。
已經部署在其中。

我們需要使用marker工具。關於如何使用標記工具的信息，請參考[Marker](/develop/map-relay-chain/marker/Marker.md)

## 生成 genesis.json 文件

```
git clone github.com/mapprotocol/atlas
cd marker/config
vim markerConfig.json
```

首先你需要像這樣配置markerConfig.json：設置你自己的 key

```shell
{
 "AdminAddress": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
 "Validators": [
  {
   "Address": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
   "SignerAddress": "0x1c0eDab88dbb72B119039c4d14b1663525b3aC15",
   "ECDSASignature": "0x2ac44a912dbf3205f0500401cf02db33240fa03198dd4c3b202dfe7be3dbe1ed7cbfbe9326d50223faf2971e3ecefed027df1dda6727e9c45b55b377384dacf201",
   "PublicKeyHex": "0x0491c373a1504d67e0c6c98276fc6043544fd09a623473b6936a107943baf666612b5e2a3beacf839d1ec74fd00f4388d4b813eac26b26ab4859003473b286650a",
   "BLSPubKey": "0x136ef6be87de9c925869387782afb4cf19496999c2684709daeb3af8d0b59d800bbe05870789f0f9b3cadababa69f5a00a38bbcba71d99c4c35d671442232c4d3017fd6b99e8356a3e4e985bdfc60bbcb8d939c87976a1ff677d7c42989b379a0b4c0f168a544c892bd2b3ec480e3d6c58c7dddb8d83677ebee2e87ab3660b80",
   "BLSG1PubKey": "0x14d44a97d2fc3ea62b6dcf2bd857079bd261993152f11aef5dd001db68b20d2d1ba45f117b6530a7aec45d7d90fd4e15d2a62f62b706eaa115aa801caeee294b",
   "BLSProofOfPossession": "0x15933821ab5c324c95ba46ad99349e4c0d068a290fabd90f59eee9e69597ce012e4df0614f48696ff22c52a0667ee1daf8ca581d920c39ba726f0f3c69cae66e"
  },
  {
   "Address": "0xdC757c8e3b8800d977a34f802131FAA870d264c4",
   "SignerAddress": "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF",
   "ECDSASignature": "0x9b4f29d54a31b052fc9fcfdc968912a59b308df021f32b3c923438c0d65ff2cc47b8d01bf5968d70a8c82875a1852c313540f89467a3fe833902a0c0aff5a27a01",
   "PublicKeyHex": "0x04e7678fb997c00d5998f79413d73ebde98865cd0d7fa82e2ab6d0920a72204d8c49c14f873ec9ee0e0b38651001acc9a4c1a0a63de6c6589b896f21f6a6bb6837",
   "BLSPubKey": "0x0a2e37ecad6e69bfec9fec2b345d0f8441a0f63acf8b45c0131a78e5d777d52e0a39404ca85f2c08752c1d4ff8df05c82c7880779d61fe3fabcd4fd682463c0515b1f0217561a6a72bd381da19e34c5560c6eccb08ff83d7d3f4ac6da7f5d1ed15a2780f782c1fa571fa65b99694af559b9df168b1d8745ac3bbc7d3fe550b94",
   "BLSG1PubKey": "0x15b7bcf0accf839170a5d4621282edcf14f4a438f8e53abcead5f0528cb91cb1135fd4e82ede1493ab1209af122e1dc186c885cc96d2413cbc09a58163b91eb9",
   "BLSProofOfPossession": "0x2cfa8f2daa103964dfa380bfc30e695b94ef62832e1e641d9febf4ab78b128392f2e33612a66cc75e35a6326b8a6aa0be26fd9567d4aeff16e9883a671648b5d"
  },
  {
   "Address": "0x257Cc34FB139A2db4Da96496Be03358d89e52d95",
   "SignerAddress": "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11",
   "ECDSASignature": "0x303ea4fdbd348c32655e7a460802be169f4bc4ca1a7030af941de4a57069dfb5253c0c087e06e258f63e50176bd5b54bf462514d8116ec3394bdc875c5678f7500",
   "PublicKeyHex": "0x04ec7664543f2dae218176a072ca7bfc16632438793077c06cf05975cc1302ee60c27f29e2cc3b64ffbaa69d2939e937f99a7bf93d7c5fa59bffbcd769e4f234e8",
   "BLSPubKey": "0x086fac850f3a9f36e8a5107eab0ba79044043dc2cc6b897cbbd0d4bf805570ff270a98f28e2d2e70b7b2ecc41a4a13e453178354997aa2038852c5945f0564bb02cdf57642881a1b40417fe3620429fc087f8dee6a68e5d7193d3243c38a1f3827d0f4cb616722a1fa78a283a17589d7688a769ade77e9d6417c6e2a9adf59c3",
   "BLSG1PubKey": "0x2fd433e93187f6b3d15664ec48073bd73d57c801c4a8bfc1e0e3abd3deefc45619d45ac7ad54df7dda5b8afd6f882c9d9f879dbc6d587f1da5da1751baac729f",
   "BLSProofOfPossession": "0x1ee63371c158365acde92ee1a53ca1dcb54efb52d1c077735dcd46237c7938b62720e4903dd9b825f3bed819422791352a9b7fe1a39e1183d33c0ca892cff1f4"
  },
  {
   "Address": "0xac146d6629F8C3B8F2e830275B583C5402032472",
   "SignerAddress": "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5",
   "ECDSASignature": "0x29388d40ee2ee4468fca04b624c77208b7b00c56c532fac53314cf39e3e8feb80c6bd2b7d25a6e20e9da0adaeec4b49c13b673e6ca2d088370e0b6189fb216f000",
   "PublicKeyHex": "0x04ef2af91ba2fc2b04bc47c7d59d6d07a0dea2a62c5b537d4a83a387bee44245317de753c4e45858708c0d31473c6595ac9dddbcf7ac02a13df4af1a188e2c9c24",
   "BLSPubKey": "0x03fea7bc386ea24aaa19c563a4f26f38cbc2ce172ba2310587405f4f05777fb911a4c3553b7b6529ea02a9da3ae2df6f70c3409105b39e1930d6a6ae8344fc221f5dfb2e73cc8ce434d1af33d95366796bdec26ca7cfcc0a03867fabf471884206db6b9e175a131995bd0c70b93a6f2eec96d831ad0c42d13d334f780d578834",
   "BLSG1PubKey": "0x1b037f39d9f8e74b608a898249cc3d156ff1f0051026388366b85a84aac43bb4068275cd909e16b29f1b3bc97e91ec0a8b95a11b8a574cbc2c9ea142d26c8a49",
   "BLSProofOfPossession": "0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402"
  }
 ]
}
```
## MarkerConfig.json 参数指令

- 地址:       該賬戶地址將作爲驗證人賬戶註冊到驗證人名單中。
- 簽名者地址:  它由賬戶地址授權，取代驗證人，與`MAP`網絡上的其他節點達成共識
- ECDSASignature: Signeraddress ecdsasignature for account address
- PublicKeyHex:   Signeraddress`s Publickeyhex
- BLSPubKey:      Signeraddress`s Blspubkey
- BLSG1PubKey:    Signeraddress`s BLSG1PubKey
- BLSProofOfPossession: 簽名人的BLS簽名數據到賬戶地址
生成簽名人的`PublicKeyHex`、`BLSPubKey`、`BLSG1PubKey`信息請參考[創建四個驗證人賬戶](./make-private-chain.md#create-four-validator-accounts)

生成簽名人的 "ECDSASignature "信息，請參考[從簽名人制作ECDSA簽名](/develop/map-relay-chain/marker/AboutValidator.md#makeecdsasignaturefromsigner)

生成簽名人的`BLSProofOfPossession`信息請參考[make BLS proof of possession from signer](/develop/map-relay-chain/marker/AboutValidator.md#makeblsproofofpossessionfromsigner)

注意:如果你使用與Address和SignerAddress相同的賬戶地址，signeraddress和blsproofofpossession是賬戶地址的默認值。

其次，你需要編譯你的`atlas-contracts`項目，我們需要關於`atlas-contracts`的字節碼來製作`genesis.json`文件。

請參考[部署與驗證器相關的合約](/develop/map-relay-chain/contracts/DeployContracts.md#deployment-of-contract-related tovalidator)

要這樣做，請運行

```shell
USAGE
  $ ./marker genesis

OPTIONS
  --buildpath:  buildpath is the path to truffle compile output folder.
  
  --newenv:   the genesis.json will be generated under this folder 

  --markercfg:  this your markerConfig.json path default to github.com/mapprotocol/atlas/marker/config/markerConfig.json
                                                     
  
EXAMPLES:

marker genesis --buildpath ./root/atlas-contracts/build/contracts --newenv ./root/atlasEnv --markercfg "./root/atlas/marker/config/markerConfig.json"


This will create a `genesis.json` in the ./root/atlasEnv folder
```

ps：你可以使用這個`genesis.json`啓動`MAP`驗證器節點
- 你可以更新`genesis.json`中的`chainid'。
- 一種方法：像這樣`./atlas --datadir ./node-1 init ./genesis.json`。
- 另一種方法：把`genesis.json`中的`alloc`信息複製到core/chain/genesis_alloc_mainnet.go文件，覆蓋`mainnetAllocJSON`信息。