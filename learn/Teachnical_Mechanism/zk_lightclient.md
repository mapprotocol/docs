## Peer-to-Peer Cross-chain Technology with ZK-enabled Light Clients

Light clients in MAP Protocol are constructed as smart contracts on MAP Relay Chain and all connected blockchains. While transparency and decentralization are well preserved with on-chain smart contracts, gas consumption is non-negligible;
With the recent development of zkSNARK, the foundation of MAP Protocol, light client construction and cross-chain proof verification can be optimized;
Correctness of light client’s state transition is vital to MAP Protocol as it determines the validation of cryptographic proof for cross-chain message.

Checking the validity of cross-chain messaging involves two main verifications:
1) the validity of the block header;
2) the validity of the given Merkle proof.

According to current engineering experiences in zero-knowledge proofs, due to factors such as the variability of the MPT’s  (Merkle Patricia Tree) height, using zkSNARK technology for generating validity proofs of MPT tree's Merkle proofs can lead to high engineering complexity and significant operational expenses. However, for the currently mainstream PoS+BFT type blockchains, employing zero-knowledge proof technology can optimize verification for block header validity.

### The construction of the light client in MAP Relay Chain
When not utilizing zero-knowledge proof technology, the light client needs to store the public keys of all current validators along with their respective weights. When verifying the validity of a block, it requires accessing all the public keys of the validator set and aggregating the public keys of the validators participating in block signing (MAP relay chain uses aggregated BLS signatures). In contrast, when employing zero-knowledge proofs, the aforementioned process can be expressed through arithmetic circuits, generating the corresponding zero-knowledge proofs.

In this design, the light client no longer needs to store the public keys and weights of all validators in the current validator set. Instead, it only stores the commitment value (hashed values of the industrial and weight information of all validators encoded) of the public keys and weights of the current validator set. The calculations for aggregating public keys and verifying the validity of the aggregated BLS signature are both expressed through arithmetic circuits and computed using the Groth16 protocol to generate zero-knowledge proofs.

In this approach, the zkSNARK proof demonstrates that the current block header, under the validator set corresponding to the commitment value stored within the light client, is a legitimate block header. This means that the verification of the block header's validity for the light client, involving a series of operations such as accessing all public keys and weights, calculating aggregated public keys, and verifying signature validity, simplifies into verifying the legitimacy of a constant-sized zkSNARK proof.
