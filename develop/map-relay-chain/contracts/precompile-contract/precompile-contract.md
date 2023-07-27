# Concept

Precompiled contracts are a compromise used in the EVM to provide more complex library functions (usually used for complex operations such as encryption, hashing, etc.) that are not suitable for writing in opcode. They are applied to contracts that are simple but frequently called, or that are logically fixed but computationally intensive. Precompiled contracts are implemented on the client-side with client code, and because they do not require the EVM, they run fast. It also costs less for developers than using functions that run directly in the EVM.

## MAP Pre-compiled contracts
To ease the development of light clients, all kinds of cryptography primitives are supported at the blockchain level and are exposed to EVM via pre-compiled contracts.

MAP Relay Chain will implement the pre-compiled contracts to support:

### ecrecover

- Address 0x0000000000000000000000000000000000000001

  ecrecover implemented as a native contract.

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

- Address 0x0000000000000000000000000000000000000002

  SHA256 implemented as a native contract.

```golang 
  func (c *sha256hash) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    h := sha256.Sum256(input)
    return h[:], nil
  }
```

### dataCopy

- Address 0x0000000000000000000000000000000000000004
- 
  data copy implemented as a native contract.

```golang
    func (c *dataCopy) Run(evm *EVM, contract *Contract, in []byte) ([]byte, error) { 
       return in, nil
    }
```

### bigModExp

- Address 0x0000000000000000000000000000000000000005

  bigModExp implements a native big integer exponential modular operation.

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

- Address 0x0000000000000000000000000000000000000006

  bn256Add implements a native elliptic curve point addition conforming to Istanbul    consensus rules.
  
```golang
  func (c *bn256AddIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
     return runBn256Add(input)
  }
```
### bn256ScalarMulIstanbul

- Address 0x0000000000000000000000000000000000000007

  bn256ScalarMulIstanbul implements a native elliptic curve scalar multiplication conforming to Istanbul consensus rules.

```golang
  func (c *bn256ScalarMulIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    return runBn256ScalarMul(input)
  }
```

### bn256PairingIstanbul

- Address 0x0000000000000000000000000000000000000008

  bn256PairingIstanbul implements a pairing pre-compile for the bn256 curve
  conforming to Istanbul consensus rules.

```golang
  func (c *bn256PairingIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
   return runBn256Pairing(input)
  }
```

### store

- Address 0x000068656164657273746F726541646472657373

  execute atlas header store contract
```golang
  func (s *store) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
     return RunHeaderStore(evm, contract, input)
  }
```

### verify

- Address 0x0000000000747856657269667941646472657373

  RunTxVerify execute atlas tx verify contract

```golang
  func (tv *verify) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
    return RunTxVerify(evm, contract, input)
  }
```
  
### transfer

- Address 0x00000000000000000000000000000000000000fd

  Native transfer contract to make Atlas Gold ERC20 compatible.

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

- Address 0x00000000000000000000000000000000000000fc

  computes a * (b ^ exponent) to `decimals` places of precision, where a and b are fractions

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

  ### proofOfPossession

- Address 0x00000000000000000000000000000000000000fb

  verify validator`s address 、publicKey、g1publickey、signature

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

### getValidator

- Address 0x00000000000000000000000000000000000000fa

  Return the validators that are required to sign the given, possibly unsealed, block number. If this block is the last in an epoch, note that that may mean one or more of those validators may no longer be elected for subsequent blocks.
  WARNING: Validator set is always constructed from the canonical chain, therefore this precompile is undefined  if the engine is aware of a chain with higher total difficulty.

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

### numberValidators

- Address 0x00000000000000000000000000000000000000f9

  Return the number of validators that are required to sign this current, possibly unsealed, block. If this block is the last in an epoch, note that that may mean one or more of those validators may no longer be elected for subsequent blocks.

      WARNING: Validator set is always constructed from the canonical chain,therefore this precompile is undefined if the engine is aware of a chain with higher total difficulty


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

### epochSize

- Address 0x00000000000000000000000000000000000000f8

  return the epochSize

```golang
  func (c *epochSize) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    epochSize := new(big.Int).SetUint64(evm.Context.EpochSize).Bytes()
    epochSizeBytes := common.LeftPadBytes(epochSize[:], 32)
    return epochSizeBytes, nil
  }
```

### blockNumberFromHeader

- Address 0x00000000000000000000000000000000000000f7

  return the blockNumber from header

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

  return the hashHeader from header

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

  Return the signer bitmap from the parent seal of a past block in the chain.
  Requested parent seal must have occurred within 4 epochs of the current block number.

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

  rerurn the extra.AggregatedSeal.Bitmap from header

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

  ed25519Verify implements a native Ed25519 signature verification.

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

- Address 0x000000000000000000000000000000000000000a
  
  Implements EIP-2537 G1Add precompile.
	
  \> G1 addition call expects `256` bytes as an input that is interpreted as byte concatenation of two G1 points (`128` bytes each).
	
  \> Output is an encoding of addition operation result - single G1 point (`128` bytes).

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

- Address 0x000000000000000000000000000000000000000b

  Implements EIP-2537 G1Mul precompile.
  
  \> G1 multiplication call expects `160` bytes as an input that is interpreted as byte concatenation of encoding of G1 point (`128` bytes) and encoding of a scalar value (`32` bytes).
	
  \> Output is an encoding of multiplication operation result - single G1 point(`128` bytes). 

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

- Address 0x000000000000000000000000000000000000000c

  Implements EIP-2537 G1MultiExp precompile.
	
  G1 multiplication call expects `160*k` bytes as an input that is interpreted as byte concatenation of `k` slices each of them being a byte concatenation of encoding of G1 point (`128` bytes) and encoding of a scalar value (`32` bytes).
	
  Output is an encoding of multiexponentiation operation result - single G1 point (`128` bytes).

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
  
  Implements EIP-2537 G2Add precompile.
	
  \> G2 addition call expects `512` bytes as an input that is interpreted as byte concatenation of two G2 points (`256` bytes each).
	
  \> Output is an encoding of addition operation result - single G2 point (`256` bytes).

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
  
  Implements EIP-2537 G2MUL precompile logic.
	
  \> G2 multiplication call expects `288` bytes as an input that is interpreted as byte concatenation of encoding of G2 point (`256` bytes) and encoding of a scalar value (`32` bytes).
	
  \> Output is an encoding of multiplication operation result - single G2 point (`256` bytes).

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

  Implements EIP-2537 G2MultiExp precompile logic
	
  \> G2 multiplication call expects `288*k` bytes as an input that is interpreted as byte concatenation of `k` slices each of them being a byte concatenation of encoding of G2 point (`256` bytes) and encoding of a scalar value (`32` bytes).
	
  \> Output is an encoding of multiexponentiation operation result - single G2 point (`256` bytes).

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

  Implements EIP-2537 Pairing precompile logic.
	
  \> Pairing call expects `384*k` bytes as an inputs that is interpreted as byte concatenation of `k` slices. Each slice has the following structure:
	
  \> - `128` bytes of G1 point encoding
	
  \> - `256` bytes of G2 point encoding
	
  \> Output is a `32` bytes where last single byte is `0x01` if pairing result is equal to multiplicative identity in a pairing target field and `0x00` otherwise
	
  \> (which is equivalent of Big Endian encoding of Solidity values `uint256(1)` and `uin256(0)` respectively).

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

Implements EIP-2537 Map_To_G1 precompile.

\> Field-to-curve call expects `64` bytes an an input that is interpreted as a an element of the base field.

\> Output of this call is `128` bytes and is G1 point following respective encoding rules.

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

  Implements EIP-2537 Map_FP2_TO_G2 precompile logic.

  \> Field-to-curve call expects `128` bytes an an input that is interpreted as a an element of the quadratic extension field.

  \> Output of this call is `256` bytes and is G2 point following respective encoding rules.

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