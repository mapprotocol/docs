# AggregatedSeal 聚合印章
下面將介紹區塊頭`extraData`字段中`AggregatedSeal`字段的生成過程

### MPA區塊頭結構
目前MAP的區塊頭主要包含以下幾個字段。

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

```
chunk header 中的 `extraData` 字段是 rlp 編碼的結果。 編碼前的結構如下：

```go
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

如果你想獲取`extraData`的結構化信息。 只需要對 extraData 32 字節之後的其他字節進行 rlp 解碼。 具體的解碼過程可以參考`ExtractIstanbulExtra`函數

```go
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

### 計算區塊頭的hash
計算區塊頭的哈希需要區塊頭中的所有字段。
但是 extraData 需要特別注意，因為 extraData 的長度會影響 hash 的計算方式。
下面根據`extraData`的長度分兩種情況進行說明。

##### `extraData` 的長度小於 32 字節
區塊頭的哈希就是區塊頭的RLP編碼keccak256哈希。

##### `extraData`的長度大於等於32字節
在計算hash之前，我們首先需要對extraData進行解碼，然後將AggregatedSeal的字段設置為空。
這一步的具體操作可以在下面的`IstanbulFilteredHeader`函數中找到。
之後，對區塊頭的RLP編碼進行keccak256 hash，得到區塊頭的hash。

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
```
### 驗證節點廣播提交消息
驗證者節點廣播提交消息，消息中攜帶`CommittedSeal`。
`AggregatedSeal` 中的 `Signature` 字段是所有收集到的提交消息中的 `CommittedSeal` 的聚合簽名的結果
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

### 生成承諾印章
sub.Digest：區塊的哈希值
sub.View.Round：回合數
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

Convert `hash`, `round`, `MsgCommit` into bytes and concatenate, I can also get a simple seal.
`MsgCommit` is a constant, its value is 2

```go
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

上面我們通過拼接塊頭的`hash`和`round`和一個固定的`MsgCommit`得到了一個簡單的印章，
但這還不夠，因為這個印記，任何人都可以生成。
然後我們還需要用驗證者的私鑰對印章進行bls簽名，得到最終的`committedSeal`

```go

type Account struct {
	Address common.Address `json:"address"` // Ethereum account address derived from the key
	URL     URL            `json:"url"`     // Optional resource locator within a backend
}

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
###AggregatedSeal 聚合印章

將所有收集到的消息中的CommittedSeal放入一個二維數組中，第一維存儲
消息列表中消息的索引，第二個維度存儲真實的CommittedSeal。
然後對這個二維數組進行bls聚合簽名。

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

### 將 sig 附加到塊頭的 extraData 字段
```go
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
