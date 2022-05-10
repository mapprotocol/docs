## Concept

Precompiled contracts are a compromise used in the EVM to provide more complex library functions (usually used for complex operations such as encryption, hashing, etc.) that are not suitable for writing in opcode. They are applied to contracts that are simple but frequently called, or that are logically fixed but computationally intensive. Precompiled contracts are implemented on the client-side with client code, and because they do not require the EVM, they run fast. It also costs less for developers than using functions that run directly in the EVM.

## MAP Pre-compiled contracts
To ease the development of light clients, all kinds of cryptography primitives are supported at the blockchain level and are exposed to EVM via pre-compiled contracts.

MAP Relay Chain will implement the pre-compiled contracts to support:

- Address 0x0000000000000000000000000000000000000001: ecrecover

  ecrecover implemented as a native contract.

  ```shell
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

- Address 0x0000000000000000000000000000000000000002: sha256hash

  SHA256 implemented as a native contract.

  ```go 
  func (c *sha256hash) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    h := sha256.Sum256(input)
    return h[:], nil
  }
  ```
- Address 0x0000000000000000000000000000000000000004: dataCopy
- 
  data copy implemented as a native contract.
    ```shell
    func (c *dataCopy) Run(evm *EVM, contract *Contract, in []byte) ([]byte, error) { 
       return in, nil
    }
    ```

- Address 0x0000000000000000000000000000000000000005: bigModExp

  bigModExp implements a native big integer exponential modular operation.
  ```shell
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

- Address 0x0000000000000000000000000000000000000006: bn256AddIstanbul

  bn256Add implements a native elliptic curve point addition conforming to Istanbul    consensus rules.
  ```shell
  func (c *bn256AddIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
     return runBn256Add(input)
  }
  ```

- Address 0x0000000000000000000000000000000000000007: bn256ScalarMulIstanbul

  bn256ScalarMulIstanbul implements a native elliptic curve scalar multiplication conforming to Istanbul consensus rules.
  ```shell
  func (c *bn256ScalarMulIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    return runBn256ScalarMul(input)
  }
  
  ```


- Address 0x0000000000000000000000000000000000000008: bn256PairingIstanbul

  bn256PairingIstanbul implements a pairing pre-compile for the bn256 curve
  conforming to Istanbul consensus rules.
  ```shell
  func (c *bn256PairingIstanbul) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
   return runBn256Pairing(input)
  }
  ```

- Address 0x000068656164657273746F726541646472657373: store

  execute atlas header store contract
  ```shell
  func (s *store) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
     return RunHeaderStore(evm, contract, input)
  }
  ```

- Address 0x0000000000747856657269667941646472657373: verify

  RunTxVerify execute atlas tx verify contract

  ```shell
  func (tv *verify) Run(evm *EVM, contract *Contract, input []byte) (ret []byte, err error) {
    return RunTxVerify(evm, contract, input)
  }
  ```
  

- Address 0x00000000000000000000000000000000000000fd: transfer

  Native transfer contract to make Atlas Gold ERC20 compatible.
  ```go
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
  

- Address 0x00000000000000000000000000000000000000fc: fractionMulExp

  computes a * (b ^ exponent) to `decimals` places of precision, where a and b are fractions

  ```shell
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
- Address 0x00000000000000000000000000000000000000fb: proofOfPossession

  verify validator`s address 、publicKey、g1publickey、signature

  ```shell 
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

- Address 0x00000000000000000000000000000000000000fa: getValidator

  Return the validators that are required to sign the given, possibly unsealed, block number. If this block is the last in an epoch, note that that may mean one or more of those validators may no longer be elected for subsequent blocks.
  WARNING: Validator set is always constructed from the canonical chain, therefore this precompile is undefined  if the engine is aware of a chain with higher total difficulty.

    ```shell
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

- Address 0x00000000000000000000000000000000000000f9: numberValidators

  Return the number of validators that are required to sign this current, possibly unsealed, block. If this block is the last in an epoch, note that that may mean one or more of those validators may no longer be elected for subsequent blocks.

      WARNING: Validator set is always constructed from the canonical chain,therefore this precompile is undefined if the engine is aware of a chain with higher total difficulty


      ```shell
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

- Address 0x00000000000000000000000000000000000000f8: epochSize

  return the epochSize

  ```shell
  func (c *epochSize) Run(evm *EVM, contract *Contract, input []byte) ([]byte, error) {
    epochSize := new(big.Int).SetUint64(evm.Context.EpochSize).Bytes()
    epochSizeBytes := common.LeftPadBytes(epochSize[:], 32)
    return epochSizeBytes, nil
  }
  ```

- Address 0x00000000000000000000000000000000000000f7: blockNumberFromHeader

  return the blockNumber from header

  ```shell
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

- Address 0x00000000000000000000000000000000000000f6: hashHeader

  return the hashHeader from header

  ```shell
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


- Address 0x00000000000000000000000000000000000000F5: getParentSealBitmap

  Return the signer bitmap from the parent seal of a past block in the chain.
  Requested parent seal must have occurred within 4 epochs of the current block number.

  ```shell
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

- Address 0x00000000000000000000000000000000000000F4: getVerifiedSealBitmap

  rerurn the extra.AggregatedSeal.Bitmap from header

  ```shell
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


