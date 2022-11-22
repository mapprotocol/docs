
Messenger is an independent inter-chain program. Running as a messenger requires a sufficient amount of gas fee for the MAPO Relay Chain ($MAPO) and the target chains (native token). Messengers are incentivized by each Dapp.

- Messenger listens to relevant events as preset in the program and builds a proof on the ledger of the source chain; then transmit the message of the event and proof to Vault or Data on the destination chain.
- Messenger needs to prepay the gas fee of MAPO Relay Chain and the destination chain for cross-chain users and thus get rewards from applications.
- As gas fees of destination chains cannot be estimated, MAP Protocol Layer cannot include this process into the bottom network because of the principle of absoluteness adopted by MAP Protocol.
- The flexibility of applications opens up many possibilities for Messenger, where applications can charge cross-chain users flexible transaction fees and reward Messenger accordingly. 
- As a main component of MAPO Services, Messenger SDK is open to Dapp developers.
- Messenger is a high concurrency inter-chain program. Theoretically, as long as one honest Messenger is working in between chains, all cross-chain transactions messages of the Dapp can be transferred.
- Malicious attacks by messengers will not cause the loss of assets and will only result in invalidity of verification on the MAP Protocol Layer.  
