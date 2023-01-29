# MCS 

## What is MCS
MCS(MAPO Cross-chain Service) is a MOS implementation. It supports token cross-chain transfer.

Now MCS supports [EVM chains](https://github.com/mapprotocol/map-contracts/tree/main/mcs/evmv2) and [Near Protocol](https://github.com/mapprotocol/map-contracts/tree/main/mcs/near).

## How to test
1. Get MAPO and MOST test token from [MAPO Faucet](https://faucet.mapprotocol.io)
2. Try to cross-chain transfer on [Butter Bridge](https://test-bridge.butternetwork.io)

## MCS testnet address


| Network      | Chain ID                | Type      | Contract Address                                                                                                                |
|--------------|-------------------------|-----------|---------------------------------------------------------------------------------------------------------------------------------|
| MAPO Makalu  | 212                     | MOS Relay | [0xB6c1b689291532D11172Fb4C204bf13169EC0dCA](https://testnet.maposcan.io/address/0xb6c1b689291532d11172fb4c204bf13169ec0dca)    |
| BSC Testnet  | 97                      | MOS       | [0x220bE51C717c4E257Cb8e96be8591740336623F8](https://testnet.bscscan.com/address/0x220bE51C717c4E257Cb8e96be8591740336623F8)    |
| Matic Mumbai | 137                     | MOS       | [0x688f3Ef5f728995a9DcB299DAEC849CA2E49ddE1](https://mumbai.polygonscan.com/address/0x688f3Ef5f728995a9DcB299DAEC849CA2E49ddE1) |
| Near Testnet | 5566818579631833089 |  MOS      | [mos2.mfac.maplabs.testnet](https://explorer.testnet.near.org/accounts/mos2.mfac.maplabs.testnet)                               |

### Tokens

| Token       | Network      | Token Address                                                                                                                 | Decimals | Note     |
|-------------|--------------|-------------------------------------------------------------------------------------------------------------------------------|----------|----------|
| MAPO(WMAPO) | MAPO Makalu  | [0x2eD27dF9B4c903aB53666CcA59AFB431F7D15e91](https://testnet.maposcan.io/token/0x2ed27df9b4c903ab53666cca59afb431f7d15e91)    | 18       |          |
|             | BSC Testnet  | [0xad4c2B6e113113d345c167F7BdAA5A5D1cD00273](https://testnet.bscscan.com/token/0xad4c2b6e113113d345c167f7bdaa5a5d1cd00273)    | 18       | mintable |
|             | Matic Mumbai | [0xE6687528C7b85115a038D806339dd7E7b869B87C](https://mumbai.polygonscan.com/token/0xE6687528C7b85115a038D806339dd7E7b869B87C) | 18       |          |
|             | Near Testnet | [mapo.maplabs.testnet](https://explorer.testnet.near.org/accounts/mapo.maplabs.testnet)                                       | 24       |          |
| MOST        | MAPO Makalu  | [0xc74bc33a95a62D90672aEFAf4bA784285903cf09](https://testnet.maposcan.io/token/0xc74bc33a95a62d90672aefaf4ba784285903cf09)    | 18       |          |
|             | BSC Testnet  | [0x688f3Ef5f728995a9DcB299DAEC849CA2E49ddE1](https://testnet.bscscan.com/token/0x688f3Ef5f728995a9DcB299DAEC849CA2E49ddE1)    | 18       | mintable |
|             | Near Testnet | [most.mos2.mfac.maplabs.testnet](https://explorer.testnet.near.org/accounts/most.mos2.mfac.maplabs.testnet)                   | 24       | mintable |
| BNB(WBNB)   | MAPO Makalu  | [0xc0fAa9255A4099D50C2b356bFbD440B69359cEa3](https://testnet.maposcan.io/token/0xc0faa9255a4099d50c2b356bfbd440b69359cea3)    | 18       | mintable |
|             | BSC Testnet  | [0xae13d989daC2f0dEbFf460aC112a837C89BAa7cd](https://testnet.bscscan.com/token/0xae13d989daC2f0dEbFf460aC112a837C89BAa7cd)    | 18       |          |
| NEAR        | MAPO Makalu | [0x9a5a085F5ffF09e922149426CbF6892F7df1dF79](https://testnet.maposcan.io/token/0x9a5a085f5fff09e922149426cbf6892f7df1df79)    | 18       | mintable |
|             | Near Testnet | [wrap.testnet](https://explorer.testnet.near.org/accounts/wrap.testnet)                                                       | 24       |          |
