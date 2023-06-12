# 概念

預編譯合約是 EVM 中使用的一種折衷方案，用於提供更複雜的庫函數（通常用於復雜的操作，如加密、哈希等），不適合寫在操作碼中。 它們適用於簡單但經常調用的合約，或者邏輯固定但計算量大的合約。 預編譯合約是在客戶端使用客戶端代碼實現的，因為它們不需要 EVM，所以運行速度很快。 對於開發人員而言，與使用直接在 EVM 中運行的函數相比，它的成本也更低。

## MAP 預編譯合約
為了簡化輕客戶端的開發，區塊鏈級別支持各種密碼學原語，並通過預編譯合約向 EVM 公開。

MAP 中繼鏈將實施預編譯合約以支持：

### Ecrecover

- 地址 0x0000000000000000000000000000000000000001

   ecrecover 作為原生合約實現。

```golang
  func (c *ecrecover) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
  const ecRecoverInputLength = 128
  
      input = common.RightPadBytes(input, ecRecoverInputLength)
      // "input" is (hash, v, r, s), each 32 bytes
      // but for ecrecover we want (r, s, v)
  
      r := new(big.Int).SetBytes(input[64:96])
      s := new(big.Int).SetBytes(input[96:128])
      v := input[63] - 27
  
      // tighter sig s values input homestead only apply to tx sigs
      if !allZero(input[32:63]) || !crypto.ValidateSignatureValues(v, r, s, false) {
          return nil, nil
      }
      // We must make sure not to modify the 'input', so placing the 'v' along with
      // the signature needs to be done on a new allocation
      sig := make([]byte, 65)
      copy(sig, input[64:128])
      sig[64] = v
      // v needs to be at the end for libsecp256k1
      pubKey, err := crypto.Ecrecover(input[:32], sig)
      // make sure the public key is a valid one
      if err != nil {
          return nil, nil
      }
  
      // the first byte of pubkey is bitcoin heritage
      return common.LeftPadBytes(crypto.Keccak256(pubKey[1:])[12:], 32), nil
  }
```

### sha256hash

- 地址 0x0000000000000000000000000000000000000002

 SHA256 作為本地合約實施。

```golang 
  func (c *sha256hash) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    h := sha256.Sum256(input)
    return h[:], nil
  }
```

### dataCopy

- 地址 0x0000000000000000000000000000000000000004
- 
 作為本機合約實現的數據副本。
```golang
    func (c *dataCopy) Run(evm *EVM, contract *Contract, in []byte) ([]byte, error) { 
       return in, nil
    }
```

### bigModExp

- 地址 0x0000000000000000000000000000000000000005

  bigModExp 實現原生大整數指數模運算。

```golang
  func (c *bigModExp) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    var (
    baseLen = new(big.Int).SetBytes(getData(input, 0, 32)).Uint64()
    expLen  = new(big.Int).SetBytes(getData(input, 32, 32)).Uint64()
    modLen  = new(big.Int).SetBytes(getData(input, 64, 32)).Uint64()
    )
    if len(input) > 96 {
       input = input[96:]
    } else {
       input = input[:0]
    }
    // Handle a special case when both the base and mod length is zero
    if baseLen == 0 && modLen == 0 {
       return []byte{}, nil
    }
    // Retrieve the operands and execute the exponentiation
    var (
    base = new(big.Int).SetBytes(getData(input, 0, baseLen))
    exp  = new(big.Int).SetBytes(getData(input, baseLen, expLen))
    mod  = new(big.Int).SetBytes(getData(input, baseLen+expLen, modLen))
    )
    if mod.BitLen() == 0 {
    // Modulo 0 is undefined, return zero
       return common.LeftPadBytes([]byte{}, int(modLen)), nil
    }
       return common.LeftPadBytes(base.Exp(base, exp, mod).Bytes(), int(modLen)), nil
  }
```

### bn256AddIstanbul

- 地址 0x0000000000000000000000000000000000000006

bn256Add 實現了一個符合伊斯坦布爾共識規則的原生橢圓曲線點加法。
  
```golang
  func (c *bn256AddIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
     return runBn256Add(input)
  }
```
### bn256ScalarMulIstanbul

- 地址 0x0000000000000000000000000000000000000007

bn256ScalarMuIstanbul 實現了一個符合伊斯坦布爾共識規則的原生橢圓曲線標量乘法。

```golang
  func (c *bn256ScalarMulIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    return runBn256ScalarMul(input)
  }
```

### bn256PairingIstanbul

- Address 0x0000000000000000000000000000000000000008

 bn256PairingIstanbul 實現bn256曲線的配對預編譯
   符合伊斯坦布爾共識規則。

```golang
  func (c *bn256PairingIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
   return runBn256Pairing(input)
  }
```

### 儲存

- Address 0x000068656164657273746F726541646472657373

  執行 atlas header 儲存合約
```golang
  func (s *store) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
     return RunHeaderStore(evm, contract, input)
  }
```

### 驗證

- 地址 0x0000000000747856657269667941646472657373

  RunTxVerify execute atlas tx verify contract

```golang
  func (tv *verify) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
    return RunTxVerify(evm, contract, input)
  }
```
  
### 轉移

- 地址 0x00000000000000000000000000000000000000fd

   使 Atlas Gold ERC20 兼容的本地傳輸合約。

```golang
  func (c *transfer) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
     caller := contract.CallerAddress
     atlasGoldAddress, err := evm.Context.GetRegisteredAddress(evm, params2.GoldTokenRegistryId)
     if err != nil {
        return nil, err
     }
    // input is comprised of 3 arguments:
    //   from:  32 bytes representing the address of the sender
    //   to:    32 bytes representing the address of the recipient
    //   value: 32 bytes, a 256 bit integer representing the amount of Atlas Gold to transfer
    // 3 arguments x 32 bytes each = 96 bytes total input
    if len(input) < 96 {
        return nil, ErrInputLength
    }

    if caller != atlasGoldAddress {
        return nil, fmt.Errorf("Unable to call transfer from unpermissioned address")
    }
    from := common.BytesToAddress(input[0:32])
    to := common.BytesToAddress(input[32:64])

    var parsed bool
    value, parsed := math.ParseBig256(hexutil.Encode(input[64:96]))
    if !parsed {
        return nil, fmt.Errorf("Error parsing transfer: unable to parse value from " + hexutil.Encode(input[64:96]))
    }

    if from == params2.ZeroAddress {
        // Mint case: Create cGLD out of thin air
        evm.StateDB.AddBalance(to, value)
    } else {
        // Fail if we're trying to transfer more than the available balance
        if !evm.Context.CanTransfer(evm.StateDB, from, value) {
            return nil, ErrInsufficientBalance
        }

        //evm.Context.Transfer(evm, from, to, value)
    }

    return input, err
  }
```
  
### fractionMulExp

- 地址 0x00000000000000000000000000000000000000fc

   計算 a * (b ^ exponent) 到精度的小數位，其中 a 和 b 是分數
```golang
  func (c *fractionMulExp) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    // input is comprised of 6 arguments:
    //   aNumerator:   32 bytes, 256 bit integer, numerator for the first fraction (a)
    //   aDenominator: 32 bytes, 256 bit integer, denominator for the first fraction (a)
    //   bNumerator:   32 bytes, 256 bit integer, numerator for the second fraction (b)
    //   bDenominator: 32 bytes, 256 bit integer, denominator for the second fraction (b)
    //   exponent:     32 bytes, 256 bit integer, exponent to raise the second fraction (b) to
    //   decimals:     32 bytes, 256 bit integer, places of precision
    //
    // 6 args x 32 bytes each = 192 bytes total input length
    if len(input) < 192 {
        return nil, ErrInputLength
    }

    parseErrorStr := "Error parsing input: unable to parse %s value from %s"

    aNumerator, parsed := math.ParseBig256(hexutil.Encode(input[0:32]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "aNumerator", hexutil.Encode(input[0:32]))
    }

    aDenominator, parsed := math.ParseBig256(hexutil.Encode(input[32:64]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "aDenominator", hexutil.Encode(input[32:64]))
    }

    bNumerator, parsed := math.ParseBig256(hexutil.Encode(input[64:96]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "bNumerator", hexutil.Encode(input[64:96]))
    }

    bDenominator, parsed := math.ParseBig256(hexutil.Encode(input[96:128]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "bDenominator", hexutil.Encode(input[96:128]))
    }

    exponent, parsed := math.ParseBig256(hexutil.Encode(input[128:160]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "exponent", hexutil.Encode(input[128:160]))
    }

    decimals, parsed := math.ParseBig256(hexutil.Encode(input[160:192]))
    if !parsed {
        return nil, fmt.Errorf(parseErrorStr, "decimals", hexutil.Encode(input[160:192]))
    }

    // Handle passing of zero denominators
    if aDenominator == big.NewInt(0) || bDenominator == big.NewInt(0) {
        return nil, fmt.Errorf("Input Error: Denominator of zero provided!")
    }

    if !decimals.IsInt64() || !exponent.IsInt64() || max(decimals.Int64(), exponent.Int64()) > 100000 {
        return nil, fmt.Errorf("Input Error: Decimals or exponent too large")
    }

    numeratorExp := new(big.Int).Mul(aNumerator, new(big.Int).Exp(bNumerator, exponent, nil))
    denominatorExp := new(big.Int).Mul(aDenominator, new(big.Int).Exp(bDenominator, exponent, nil))

    decimalAdjustment := new(big.Int).Exp(big.NewInt(10), decimals, nil) //10^18

    numeratorDecimalAdjusted := new(big.Int).Div(new(big.Int).Mul(numeratorExp, decimalAdjustment), denominatorExp).Bytes()
    denominatorDecimalAdjusted := decimalAdjustment.Bytes()

    numeratorPadded := common.LeftPadBytes(numeratorDecimalAdjusted, 32)
    denominatorPadded := common.LeftPadBytes(denominatorDecimalAdjusted, 32)

    return append(numeratorPadded, denominatorPadded...), nil
  }
```

  ### 佔有證明

- 地址 0x00000000000000000000000000000000000000fb

   驗證validator的地址、publicKey、g1publickey、signature

```golang 
  func (c *proofOfPossession) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
      // input is comprised of 3 arguments:
      //   address:   20 bytes, an address used to generate the proof-of-possession
      //   publicKey: 129 bytes, representing the public key (defined as a const in bls package)
      //   G1PUBLICKEYBYTES: 129 bytes, representing the bls public key (defined as a const in bls package)
      //   signature: 64 bytes, representing the signature on `address` (defined as a const in bls package)
      // the total length of input required is the sum of these constants
      if len(input) != common.AddressLength+blscrypto.PUBLICKEYBYTES+blscrypto.G1PUBLICKEYBYTES+blscrypto.SIGNATUREBYTES {
           return nil, ErrInputLength
      }
      addressBytes := input[:common.AddressLength]

      publicKeyBytes := input[common.AddressLength : common.AddressLength+blscrypto.PUBLICKEYBYTES]
      publicKey, err := bls.UnmarshalPk(publicKeyBytes)
      if err != nil {
          return nil, err
      }

      apk := bls.NewApk(publicKey)
      signatureBytes := input[common.AddressLength+blscrypto.PUBLICKEYBYTES+blscrypto.G1PUBLICKEYBYTES : common.AddressLength+blscrypto.PUBLICKEYBYTES+blscrypto.G1PUBLICKEYBYTES+blscrypto.SIGNATUREBYTES]
	  
      signature := bls.Signature{}
      signature.Unmarshal(signatureBytes)
      err = bls.Verify(apk, addressBytes, &signature)
      if err != nil {
          return nil, err
      }

      G1 := input[common.AddressLength+blscrypto.PUBLICKEYBYTES : common.AddressLength+blscrypto.PUBLICKEYBYTES+blscrypto.G1PUBLICKEYBYTES]
	  
      err = bls.VerifyG1Pk(G1, publicKeyBytes)
      if err != nil {
          return nil, err
      }
      return true32Byte, nil
  }
```

### getValidator 獲取驗證者

- 地址 0x00000000000000000000000000000000000000fa

   返回簽署給定的、可能未密封的塊號所需的驗證器。 如果這個塊是一個紀元中的最後一個塊，請注意，這可能意味著這些驗證器中的一個或多個可能不再被選為後續塊。
   警告：驗證器集總是從規範鏈構建，因此如果引擎知道總難度更高的鏈，則此預編譯是未定義的。

```golang
    func (c *getValidator) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
       // input is comprised of two arguments:
       //   index: 32 byte integer representing the index of the validator to get
       //   blockNumber: 32 byte integer representing the block number to access
       if len(input) < 64 {
          return nil, ErrInputLength
       }
       index := new(big.Int).SetBytes(input[0:32])

       blockNumber := new(big.Int).SetBytes(input[32:64])
       if blockNumber.Cmp(common.Big0) == 0 {
           // Validator set for the genesis block is empty, so any index is out of bounds.
           return nil, ErrValidatorsOutOfBounds
       }
       if blockNumber.Cmp(evm.Context.BlockNumber) > 0 {
         return nil, ErrBlockNumberOutOfBounds
       }

       // Note: Passing empty hash as here as it is an extra expense and the hash is not actually used.
       validators := evm.Context.GetValidators(new(big.Int).Sub(blockNumber, common.Big1), common.Hash{})

       // Ensure index, which is guaranteed to be non-negative, is valid.
       if index.Cmp(big.NewInt(int64(len(validators)))) >= 0 {
          return nil, ErrValidatorsOutOfBounds
       }

       validatorAddress := validators[index.Uint64()].Address()
       addressBytes := common.LeftPadBytes(validatorAddress[:], 32)

       return addressBytes, nil
    }
```

### numberValidators 驗證者數量

- 地址 0x00000000000000000000000000000000000000f9

   返回簽署此當前（可能未密封）塊所需的驗證者數量。 如果這個塊是一個紀元中的最後一個塊，請注意，這可能意味著這些驗證者中的一個或多個可能不再被選為後續塊。

       警告：驗證者集總是從規範鏈構建，因此如果引擎知道總難度更高的鏈，則此預編譯是未定義的


```golang
      func (c *numberValidators) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
        // input is comprised of a single argument:
        //   blockNumber: 32 byte integer representing the block number to access
        if len(input) < 32 {
          return nil, ErrInputLength
        }
        blockNumber := new(big.Int).SetBytes(input[0:32])
         if blockNumber.Cmp(common.Big0) == 0 {
           // Genesis validator set is empty. Return 0.
           return make([]byte, 32), nil
        }
        if blockNumber.Cmp(evm.Context.BlockNumber) > 0 {
         return nil, ErrBlockNumberOutOfBounds
        }

        // Note: Passing empty hash as here as it is an extra expense and the hash is not actually used.
        validators := evm.Context.GetValidators(new(big.Int).Sub(blockNumber, common.Big1), common.Hash{})

        numberValidators := big.NewInt(int64(len(validators))).Bytes()
        numberValidatorsBytes := common.LeftPadBytes(numberValidators[:], 32)
        return numberValidatorsBytes, nil
      }
```

### epochSize 紀元大小

- 地址 0x00000000000000000000000000000000000000f8

   返回epochSize

```golang
  func (c *epochSize) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    epochSize := new(big.Int).SetUint64(evm.Context.EpochSize).Bytes()
    epochSizeBytes := common.LeftPadBytes(epochSize[:], 32)
    return epochSizeBytes, nil
  }
```

### blockNumberFromHeader

- Address 0x00000000000000000000000000000000000000f7

 返回blockNumber from header

```golang
  func (c *blockNumberFromHeader) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    var header types.Header
    err := rlp.DecodeBytes(input, &header)
    if err != nil {
      return nil, ErrInputDecode
    }
    blockNumber := header.Number.Bytes()
    blockNumberBytes := common.LeftPadBytes(blockNumber[:], 32)
    return blockNumberBytes, nil
  }
```

### hashHeader

- Address 0x00000000000000000000000000000000000000f6

  返回 the hashHeader from header

```golang
  func (c *hashHeader) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    var header types.Header
    err := rlp.DecodeBytes(input, &header)
    if err != nil {
       return nil, ErrInputDecode
    }
    hashBytes := header.Hash().Bytes()
    return hashBytes, nil
  }
```

### getParentSealBitmap

- Address 0x00000000000000000000000000000000000000F5

從鏈中過去區塊的父印章返回簽名者位圖signer bitmap。
   請求的父印章必須在當前塊號的 4 個紀元epochs內發生。

```golang
      func (c *getParentSealBitmap) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
        // input is comprised of a single argument:
        //   blockNumber: 32 byte integer representing the block number to access
        if len(input) < 32 {
           return nil, ErrInputLength
        }
        blockNumber := new(big.Int).SetBytes(input[0:32])
    
        // Ensure the request is for information from a previously sealed block.
        if blockNumber.Cmp(common.Big0) == 0 || blockNumber.Cmp(evm.Context.BlockNumber) > 0 {
            return nil, ErrBlockNumberOutOfBounds
        }
    
        // Ensure the request is for a sufficiently recent block to limit state expansion.
        historyLimit := new(big.Int).SetUint64(evm.Context.EpochSize * 4)
        if blockNumber.Cmp(new(big.Int).Sub(evm.Context.BlockNumber, historyLimit)) <= 0 {
            return nil, ErrBlockNumberOutOfBounds
        }
    
        header := evm.Context.GetHeaderByNumber(blockNumber.Uint64())
        if header == nil {
            log.Error("Unexpected failure to retrieve block in getParentSealBitmap precompile", "blockNumber", blockNumber)
            return nil, ErrUnexpected
        }
    
        extra, err := types.ExtractIstanbulExtra(header)
        if err != nil {
            log.Error("Header without Istanbul extra data encountered in getParentSealBitmap precompile", "blockNumber", blockNumber, "err", err)
            return nil, ErrEngineIncompatible
        }
    
      return common.LeftPadBytes(extra.ParentAggregatedSeal.Bitmap.Bytes()[:], 32), nil
    }
```

### getVerifiedSealBitmap

- Address 0x00000000000000000000000000000000000000F4

  返回 the extra.AggregatedSeal.Bitmap from header

```golang
  func (c *getVerifiedSealBitmap) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    // input is comprised of a single argument:
    // header:  rlp encoded block header
    var header types.Header
    if err := rlp.DecodeBytes(input, &header); err != nil {
      return nil, ErrInputDecode
    }
      // Verify the seal against the engine rules.
	if !evm.Context.VerifySeal(&header) {
		return nil, ErrInputVerification
	}

	// Extract the verified seal from the header.
	extra, err := types.ExtractIstanbulExtra(&header)
	if err != nil {
		log.Error("Header without Istanbul extra data encountered in getVerifiedSealBitmap precompile", "extraData", header.Extra, "err", err)
		// Seal verified by a non-Istanbul engine. Return an error.
		return nil, ErrEngineIncompatible
	}
      return common.LeftPadBytes(extra.AggregatedSeal.Bitmap.Bytes()[:], 32), nil
   }
```

### ed25519Verify

- Address 0x00000000000000000000000000000000000000f3

ed25519Verify 實現了原生的 Ed25519 簽名驗證。

```golang
  func (c *ed25519Verify) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	// Setup success/failure return values
	var fail32byte, success32Byte = true32Byte, false32Byte

	// Check if all required arguments are present
	if len(input) < 96 {
		return fail32byte, nil
	}

	publicKey := input[0:32]  // 32 bytes
	signature := input[32:96] // 64 bytes
	message := input[96:]     // arbitrary length

	// Verify the Ed25519 signature against the public key and message
	// https://godoc.org/golang.org/x/crypto/ed25519#Verify
	if ed25519.Verify(publicKey, message, signature) {
		return success32Byte, nil
	}
	return fail32byte, nil
  }
```

### BLS12_G1ADD

- 地址 0x000000000000000000000000000000000000000a
  
實現 EIP-2537 G1Add 預編譯。

   \> G1 加法調用需要“256”字節作為輸入，該輸入被解釋為兩個 G1 點（每個“128”字節）的字節串聯。

   \> 輸出是加法運算結果的編碼 - 單個 G1 點（`128` 字節）。

```golang

func (c *bls12381G1Add) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
  if len(input) != 256 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	var p0, p1 *bls12381.PointG1

	// Initialize G1
	g := bls12381.NewG1()
	// Decode G1 point p_0
	if p0, err = g.DecodePoint(input[:128]); err != nil {
		return nil, err
	}
	// Decode G1 point p_1
	if p1, err = g.DecodePoint(input[128:]); err != nil {
		return nil, err
	}
	// Compute r = p_0 + p_1
	r := g.New()
	g.Add(r, p0, p1)
	// Encode the G1 point result into 128 bytes
	return g.EncodePoint(r), nil
}
```

### BLS12_G1MUL

- 地址 0x000000000000000000000000000000000000000b

 實現 EIP-2537 G1Mul 預編譯。
  
   \> G1 乘法調用需要“160”字節作為輸入，該輸入被解釋為 G1 點編碼（“128”字節）和標量值編碼（“32”字節）的字節串聯。

   \> 輸出是乘法運算結果的編碼 - 單個 G1 點（`128` 字節）。

```golang

 func (c *bls12381G1Mul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
	if len(input) != 160 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	var p0 *bls12381.PointG1

	// Initialize G1
	g := bls12381.NewG1()
	// Decode G1 point
	if p0, err = g.DecodePoint(input[:128]); err != nil {
		return nil, err
	}
	// Decode scalar value
	e := new(big.Int).SetBytes(input[128:])
	// Compute r = e * p_0
	r := g.New()
	g.MulScalar(r, p0, e)
	// Encode the G1 point into 128 bytes
	return g.EncodePoint(r), nil
}

```

### BLS12_G1MULTIEXP

- 地址 0x000000000000000000000000000000000000000c

實現 EIP-2537 G1MultiExp 預編譯。

  \> G1 乘法調用期望“160*k”字節作為輸入，該輸入被解釋為“k”切片的字節串聯，每個切片都是 G1 點編碼（“128”字節）和標量值編碼（“ 32` 字節）。

  \> 輸出是多重指數運算結果的編碼 - 單個 G1 點（`128` 字節）。

```golang
  func (c *bls12381G1MultiExp) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
  k := len(input) / 160
	if len(input) == 0 || len(input)%160 != 0 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	points := make([]*bls12381.PointG1, k)
	scalars := make([]*big.Int, k)
	// Initialize G1
	g := bls12381.NewG1()
	// Decode point scalar pairs
	for i := 0; i < k; i++ {
		off := 160 * i
		t0, t1, t2 := off, off+128, off+160
		// Decode G1 point
		if points[i], err = g.DecodePoint(input[t0:t1]); err != nil {
			return nil, err
		}
		// Decode scalar value
		scalars[i] = new(big.Int).SetBytes(input[t1:t2])
	}
	// Compute r = e_0 * p_0 + e_1 * p_1 + ... + e_(k-1) * p_(k-1)
	r := g.New()
	g.MultiExp(r, points, scalars)
	// Encode the G1 point to 128 bytes
	return g.EncodePoint(r), nil
}
```

### BLS12_G2ADD

- Address 0x000000000000000000000000000000000000000d
  
  預編譯 EIP-2537 G2Add
	
\> G2 加法調用需要“512”字節作為輸入，該輸入被解釋為兩個 G2 點（每個“256”字節）的字節串聯。

   \> 輸出是加法運算結果的編碼 - 單個 G2 點（`256` 字節）。

```golang

  func (c *bls12381G2Add) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
  if len(input) != 512 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	var p0, p1 *bls12381.PointG2
	// Initialize G2
	g := bls12381.NewG2()
	r := g.New()
	// Decode G2 point p_0
	if p0, err = g.DecodePoint(input[:256]); err != nil {
		return nil, err
	}
	// Decode G2 point p_1
	if p1, err = g.DecodePoint(input[256:]); err != nil {
		return nil, err
	}
	// Compute r = p_0 + p_1
	g.Add(r, p0, p1)
	// Encode the G2 point into 256 bytes
	return g.EncodePoint(r), nil
  }
```

### BLS12_G2MUL

- Address 0x000000000000000000000000000000000000000e
  
  預編譯 EIP-2537 G2MUL 邏輯.
	
 \> G2 乘法調用需要“288”字節作為輸入，該輸入被解釋為 G2 點編碼（“256”字節）和標量值編碼（“32”字節）的字節串聯。

   \> 輸出是乘法運算結果的編碼 - 單個 G2 點（`256` 字節）。

```golang

  func (c *bls12381G2Mul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
	if len(input) != 288 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	var p0 *bls12381.PointG2
	// Initialize G2
	g := bls12381.NewG2()
	// Decode G2 point
	if p0, err = g.DecodePoint(input[:256]); err != nil {
		return nil, err
	}
	// Decode scalar value
	e := new(big.Int).SetBytes(input[256:])
	// Compute r = e * p_0
	r := g.New()
	g.MulScalar(r, p0, e)
	// Encode the G2 point into 256 bytes
	return g.EncodePoint(r), nil
}
```

### BLS12_G2MULTIEXP

- Address 0x000000000000000000000000000000000000000f

  預編譯 EIP-2537 G2MultiExp 邏輯
	
 \> G2 乘法調用期望將“288*k”字節作為輸入，該輸入被解釋為“k”切片的字節串聯，每個切片都是 G2 點（“256”字節）編碼和標量值編碼的字節串聯 （`32` 字節）。

   \> 輸出是多重指數運算結果的編碼 - 單個 G2 點（`256` 字節）。

```golang
  func (c *bls12381G2MultiExp) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
	k := len(input) / 288
	if len(input) == 0 || len(input)%288 != 0 {
		return nil, errBLS12381InvalidInputLength
	}
	var err error
	points := make([]*bls12381.PointG2, k)
	scalars := make([]*big.Int, k)

	// Initialize G2
	g := bls12381.NewG2()

	// Decode point scalar pairs
	for i := 0; i < k; i++ {
		off := 288 * i
		t0, t1, t2 := off, off+256, off+288
		// Decode G1 point
		if points[i], err = g.DecodePoint(input[t0:t1]); err != nil {
			return nil, err
		}
		// Decode scalar value
		scalars[i] = new(big.Int).SetBytes(input[t1:t2])
	}

	// Compute r = e_0 * p_0 + e_1 * p_1 + ... + e_(k-1) * p_(k-1)
	r := g.New()
	g.MultiExp(r, points, scalars)

	// Encode the G2 point to 256 bytes.
	return g.EncodePoint(r), nil
}
```

### BLS12_PAIRING

- Address 0x0000000000000000000000000000000000000010

  預編譯 EIP-2537 Pairing 邏輯.
	
 \> 配對調用需要 `384*k` 字節作為輸入，被解釋為 `k` 切片的字節連接。 每個切片具有以下結構：

   \> - `128` 字節的 G1 點編碼

   \> - `256` 字節的 G2 點編碼

   \> 輸出是一個“32”字節，如果配對結果等於配對目標字段中的乘法標識，最後一個字節為“0x01”，否則為“0x00”

   \>（分別相當於 Solidity 值 `uint256(1)` 和 `uin256(0)` 的 Big Endian 編碼）。

```golang
func (c *bls12381Pairing) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
	k := len(input) / 384
	if len(input) == 0 || len(input)%384 != 0 {
		return nil, errBLS12381InvalidInputLength
	}

	// Initialize BLS12-381 pairing engine
	e := bls12381.NewPairingEngine()
	g1, g2 := e.G1, e.G2

	// Decode pairs
	for i := 0; i < k; i++ {
		off := 384 * i
		t0, t1, t2 := off, off+128, off+384
		// Decode G1 point
		p1, err := g1.DecodePoint(input[t0:t1])
		if err != nil {
			return nil, err
		}
		// Decode G2 point
		p2, err := g2.DecodePoint(input[t1:t2])
		if err != nil {
			return nil, err
		}
		// 'point is on curve' check already done,
		// Here we need to apply subgroup checks.
		if !g1.InCorrectSubgroup(p1) {
			return nil, errBLS12381G1PointSubgroup
		}
		if !g2.InCorrectSubgroup(p2) {
			return nil, errBLS12381G2PointSubgroup
		}
		// Update pairing engine with G1 and G2 ponits
		e.AddPair(p1, p2)
	}
	// Prepare 32 byte output
	out := make([]byte, 32)
	// Compute pairing and set the result
	if e.Check() {
		out[31] = 1
	}
	return out, nil
}
```

### BLS12_MAP_FP_TO_G1

- Address 0x0000000000000000000000000000000000000011

預變異 EIP-2537 Map_To_G1

\> 字段到曲線調用需要“64”字節和一個被解釋為基本字段元素的輸入。

\> 此調用的輸出是 128 個字節，並且是遵循各自編碼規則的 G1 點。
```golang

  func (c *bls12381MapG1) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
	
	if len(input) != 64 {
		return nil, errBLS12381InvalidInputLength
	}
	// Decode input field element
	fe, err := decodeBLS12381FieldElement(input)
	if err != nil {
		return nil, err
	}
	// Initialize G1
	g := bls12381.NewG1()
	// Compute mapping
	r, err := g.MapToCurve(fe)
	if err != nil {
		return nil, err
	}
	// Encode the G1 point to 128 bytes
	return g.EncodePoint(r), nil
}
```

### BLS12_MAP_FP2_TO_G2

- Address 0x0000000000000000000000000000000000000012

  預編譯 EIP-2537 Map_FP2_TO_G2 邏輯

\> 場到曲線調用需要“128”字節和一個輸入，該輸入被解釋為二次擴展場的一個元素。

   \> 此調用的輸出是 `256` 字節，並且是遵循各自編碼規則的 G2 點。
```golang
  func (c *bls12381MapG2) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) 
  {
    if len(input) != 128 {
		  return nil, errBLS12381InvalidInputLength
	  }
	  // Decode input field element
	  fe := make([]byte, 96)
	  c0, err := decodeBLS12381FieldElement(input[:64])
	  if err != nil 
    {
		  return nil, err
	  }
	  copy(fe[48:], c0)
	  c1, err := decodeBLS12381FieldElement(input[64:])
	  if err != nil 
    {
		  return nil, err
	  }
	  copy(fe[:48], c1)
	  // Initialize G2
	  g := bls12381.NewG2()
	  // Compute mapping
	  r, err := g.MapToCurve(fe)
	  if err != nil 
    {
		  return nil, err
	  }
	  // Encode the G2 point to 256 bytes
	  return g.EncodePoint(r), nil
  }
```