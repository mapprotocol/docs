# AggregatedSeal
The following will introduce the generation process of the `AggregatedSeal` field in the `extraData` field of the block header

### Data structures and variables that may be used

```go
// Header represents a block header in the Ethereum blockchain.
type Header struct {
	ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
	Coinbase    common.Address `json:"miner"            gencodec:"required"`
	Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
	TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
	ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
	Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
	Number      *big.Int       `json:"number"           gencodec:"required"`
	GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
	GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
	Time        uint64         `json:"timestamp"        gencodec:"required"`
	Extra       []byte         `json:"extraData"        gencodec:"required"`
	MixDigest   common.Hash    `json:"mixHash"`
	Nonce       BlockNonce     `json:"nonce"`

	// BaseFee was added by EIP-1559 and is ignored in legacy headers.
	BaseFee *big.Int `json:"baseFeePerGas" rlp:"optional"`
}

var (
	IstanbulExtraVanity       = 32  // Fixed number of extra-data bytes reserved for validator vanity
	IstanbulExtraBlsSignature = 64  // Fixed number of extra-data bytes reserved for validator 
)

type IstanbulExtra struct {
	// AddedValidators are the validators that have been added in the block
	AddedValidators []common.Address
	// AddedValidatorsPublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsPublicKeys []blscrypto.SerializedPublicKey
	// AddedValidatorsG1PublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsG1PublicKeys []blscrypto.SerializedG1PublicKey
	// RemovedValidators is a bitmap having an active bit for each removed validator in the block
	RemovedValidators *big.Int
	// Seal is an ECDSA signature by the proposer
	Seal []byte
	// AggregatedSeal contains the aggregated BLS signature created via IBFT consensus.
	AggregatedSeal IstanbulAggregatedSeal
	// ParentAggregatedSeal contains and aggregated BLS signature for the previous block.
	ParentAggregatedSeal IstanbulAggregatedSeal
}

type IstanbulAggregatedSeal struct {
	// Bitmap is a bitmap having an active bit for each validator that signed this block
	Bitmap *big.Int
	// Signature is an aggregated BLS signature resulting from signatures by each validator that signed this block
	Signature []byte
	// Round is the round in which the signature was created.
	Round *big.Int
}
```

### get header hash
```go
// Hash returns the block hash of the header, which is simply the keccak256 hash of its
// RLP encoding.
func (h *Header) Hash() common.Hash {
	// Seal is reserved in extra-data. To prove block is signed by the proposer.
    // IstanbulExtraVanity = 32
	if len(h.Extra) >= IstanbulExtraVanity {
		if istanbulHeader := IstanbulFilteredHeader(h, true); istanbulHeader != nil {
			return rlpHash(istanbulHeader)
		}
	}
    // which is simply the keccak256 hash of its RLP encoding.
	return rlpHash(h)
}

// IstanbulFilteredHeader returns a filtered header which some information (like seal, aggregated signature)
// are clean to fulfill the Istanbul hash rules. It returns nil if the extra-data cannot be
// decoded/encoded by rlp.
func IstanbulFilteredHeader(h *Header, keepSeal bool) *Header {
	newHeader := CopyHeader(h)
	istanbulExtra, err := ExtractIstanbulExtra(newHeader)
	if err != nil {
		return nil
	}

	if !keepSeal {
		istanbulExtra.Seal = []byte{}
	}
	istanbulExtra.AggregatedSeal = IstanbulAggregatedSeal{}

	payload, err := rlp.EncodeToBytes(&istanbulExtra)
	if err != nil {
		return nil
	}

    // IstanbulExtraVanity = 32
	newHeader.Extra = append(newHeader.Extra[:IstanbulExtraVanity], payload...)

	return newHeader
}

// ExtractIstanbulExtra extracts all values of the IstanbulExtra from the header. It returns an
// error if the length of the given extra-data is less than 32 bytes or the extra-data can not
// be decoded.
func ExtractIstanbulExtra(h *Header) (*IstanbulExtra, error) {
	
    ......

	var istanbulExtra *IstanbulExtra
    // IstanbulExtraVanity = 32
	err := rlp.DecodeBytes(h.Extra[IstanbulExtraVanity:], &istanbulExtra)
	if err != nil {
		return nil, err
	}
	return istanbulExtra, nil
}

```

### Validator nodes broadcast commit messages
The validator node broadcasts the commit message, and the message carries the `CommittedSeal`.
The `Signature` field in  `AggregatedSeal` is the result of the aggregated signature of the `CommittedSeal` in all the collected commit messages

```go
func (c *core) broadcastCommit(sub *istanbul.Subject) {
	
	......
 
	committedSeal, err := c.generateCommittedSeal(sub)
	
	......
	
	istMsg := istanbul.NewCommitMessage(&istanbul.CommittedSubject{
		Subject:               sub,
		CommittedSeal:         committedSeal[:],
		EpochValidatorSetSeal: epochValidatorSetSeal[:],
	}, c.address)
	c.broadcast(istMsg)
}

```

### Generate Committed Seal
sub.Digest: the hash of the block
sub.View.Round: number of rounds

```go
func (c *core) generateCommittedSeal(sub *istanbul.Subject) (blscrypto.SerializedSignature, error) {
	seal := PrepareCommittedSeal(sub.Digest, sub.View.Round)
	
	committedSeal, err := c.backend.SignBLS(seal, []byte{}, false, false)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}
	return committedSeal, nil
}
```

### Generate seal based on given hash and round
Convert `hash`, `round`, `MsgCommit` into bytes and splicing, `MsgCommit` is a constant, its value is 2

```go
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

### BLS Sign the generated seal

```go
func (sb *Backend) SignBLS(data []byte, extra []byte, useComposite, cip22 bool) (blscrypto.SerializedSignature, error) {
	w := sb.wallets()
    // w.Bls.Sign is the following method
    // func (bi *BlsInfo) Sign(data []byte, extra []byte, useComposite, cip22 bool) 
	return w.Bls.Sign(data, extra, useComposite, cip22)
}

type BlsInfo struct {
	Address common.Address       // Ethereum address of the BLS signing key
	sign    istanbul.BLSSignerFn // Signer function to authorize BLS messages
}

// Account represents an Ethereum account located at a specific location defined
// by the optional URL field.
type Account struct {
	Address common.Address `json:"address"` // Ethereum account address derived from the key
	URL     URL            `json:"url"`     // Optional resource locator within a backend
}

func (bi *BlsInfo) Sign(data []byte, extra []byte, useComposite, cip22 bool) (blscrypto.SerializedSignature, error) {
	......

    // bi.Address: address of validator node
    // bi.sign() is the following method
    // func (w *keystoreWallet) SignBLS(account accounts.Account, msg []byte, extraData []byte, useComposite, cip22 bool)
	return bi.sign(accounts.Account{Address: bi.Address}, data, extra, useComposite, cip22)
}

func (w *keystoreWallet) SignBLS(account accounts.Account, msg []byte, extraData []byte, useComposite, cip22 bool) (bls.SerializedSignature, error) {
	
    ......

	// Account seems valid, request the keystore to sign
	return w.keystore.SignBLS(account, msg, extraData, useComposite, cip22)
}

// The msg is the seal generated by `PrepareCommittedSeal`, and the first return value is the `CommittedSeal` of the final return value of `generateCommittedSeal` above
func (ks *KeyStore) SignBLS(a accounts.Account, msg []byte, extraData []byte, useComposite, cip22 bool) (blscrypto.SerializedSignature, error) {
	// Look up the key to sign with and abort if it cannot be found
	ks.mu.RLock()
	defer ks.mu.RUnlock()

    // a.Address is the address of validator
    // when starting the validator node, you need to unlock the validator account through the `--unlock` flag. When unlocking, 
    // the account's private key and other information will be put into `unlocked`
	unlockedKey, found := ks.unlocked[a.Address]
	if !found {
		return blscrypto.SerializedSignature{}, ErrLocked
	}

    // The private key corresponding to the validator account
	privateKeyBytes, err := blscrypto.CryptoType().ECDSAToBLS(unlockedKey.PrivateKey)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}

	blskey, err := bn256.DeserializePrivateKey(privateKeyBytes)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}

	sign, err := bn256.Sign(blskey, blskey.ToPublic(), msg)
	if err != nil {
		return blscrypto.SerializedSignature{}, err
	}
	signature := blscrypto.SerializedSignature{}
	copy(signature[:], sign.Marshal())
	return signature, nil
}

```

### aggregates all the given seals for a given message set to a bls aggregated signature and bitmap

```go
func GetAggregatedSeal(seals MessageSet, round *big.Int) (types.IstanbulAggregatedSeal, error) {
	bitmap := big.NewInt(0)
	committedSeals := make([][]byte, seals.Size())
    // Get all collected messages
	for i, v := range seals.Values() {
        // IstanbulExtraBlsSignature = 64
		committedSeals[i] = make([]byte, types.IstanbulExtraBlsSignature)
        // get the `committedSubject` in each message
		commit := v.Commit()
        // the commit.CommittedSeal here is the CommittedSeal in the message broadcast by the validator
		copy(committedSeals[i][:], commit.CommittedSeal[:])

		j, err := seals.GetAddressIndex(v.Address)
		if err != nil {
			return types.IstanbulAggregatedSeal{}, err
		}
		bitmap.SetBit(bitmap, int(j), 1)
	}

	asig, err := blscrypto.CryptoType().AggregateSignatures(committedSeals)
	if err != nil {
		return types.IstanbulAggregatedSeal{}, err
	}
	return types.IstanbulAggregatedSeal{Bitmap: bitmap, Signature: asig, Round: round}, nil
}
```

### Append sig to the extra-data field of a block header
```go
func (c *core) commit() error {
	
	......
	
	proposal := c.current.Proposal()
	if proposal != nil {
		aggregatedSeal, err := GetAggregatedSeal(c.current.Commits(), c.current.Round())
		
		......
		
		// Query the StateProcessResult cache, nil if it's cache miss
		result := c.current.GetStateProcessResult(proposal.Hash())
		if err := c.backend.Commit(proposal, aggregatedSeal, aggregatedEpochValidatorSetSeal, result); err != nil {
			nextRound := new(big.Int).Add(c.current.Round(), common.Big1)
			logger.Warn("Error on commit, waiting for desired round", "reason", "backend.Commit", "err", err, "desired_round", nextRound)
			c.waitForDesiredRound(nextRound)
			return nil
		}
	}

    ......
}

func (sb *Backend) Commit(proposal istanbul.Proposal, aggregatedSeal types.IstanbulAggregatedSeal, aggregatedEpochValidatorSetSeal types.IstanbulEpochValidatorSetSeal, result *istanbulCore.StateProcessResult) error {
	
	......
	
	h := block.Header()
	// Append seals into extra-data
	err := writeAggregatedSeal(h, aggregatedSeal, false)
	if err != nil {
		return err
	}
	
	......
}

// writeAggregatedSeal writes the extra-data field of a block header with given committed
// seals. If isParent is set to true, then it will write to the fields related
// to the parent commits of the block
func writeAggregatedSeal(h *types.Header, aggregatedSeal types.IstanbulAggregatedSeal, isParent bool) error {
	// IstanbulExtraBlsSignature = 64
    if len(aggregatedSeal.Signature) != types.IstanbulExtraBlsSignature {
		return errInvalidAggregatedSeal
	}

	istanbulExtra, err := types.ExtractIstanbulExtra(h)
	if err != nil {
		return err
	}

	if isParent {
		istanbulExtra.ParentAggregatedSeal = aggregatedSeal
	} else {
		istanbulExtra.AggregatedSeal = aggregatedSeal
	}

	payload, err := rlp.EncodeToBytes(&istanbulExtra)
	if err != nil {
		return err
	}

	// compensate the lack bytes if header.Extra is not enough IstanbulExtraVanity bytes.
    // IstanbulExtraVanity = 32
	if len(h.Extra) < types.IstanbulExtraVanity {
		h.Extra = append(h.Extra, bytes.Repeat([]byte{0x00}, types.IstanbulExtraVanity-len(h.Extra))...)
	}

	h.Extra = append(h.Extra[:types.IstanbulExtraVanity], payload...)
	return nil
}

```
