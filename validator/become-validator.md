# Become a Validator

## Overview

MAP Protocol uses Proof of Stake (POS) consensus. Validators secure the network by:

- Staking at least 1,000,000 MAP tokens
- Running a validator node to produce and validate blocks
- Earning rewards based on votes received

## Prerequisites

### Hardware Requirements

| Component | Requirement |
|-----------|-------------|
| CPU | Quad core 2.5 GHz (64-bit) |
| RAM | 16 GB |
| Storage | 256 GB SSD + secondary HDD |
| Network | 100 Mb/s, fiber connection recommended |

### Software Requirements

- Go 1.14 or later
- Git
- C compiler

### MAP Tokens

Your account needs at least **1,000,000 MAP** for staking.

## Build Tools

```bash
# Clone repository
git clone https://github.com/mapprotocol/atlas.git
cd atlas

# Build atlas and marker
make atlas
make marker
```

## Prepare Accounts

You need two accounts:

| Account | Purpose | File |
|---------|---------|------|
| Account | Staking, receives rewards | account.json |
| Signer | Signs blocks | signer.json |

### Generate Keystore

```bash
./atlas account new --keystore ./datadir/keystore
```

Or import existing private key:

```bash
./atlas --dataDir "./data" console
> web3.personal.importRawKey("your_private_key", "your_password")
```

## Start Validator Node

```bash
./atlas --datadir ./node \
  --syncmode "full" \
  --port 30321 \
  --v5disc \
  --mine \
  --miner.validator <SIGNER_ADDRESS> \
  --unlock <SIGNER_ADDRESS>
```

Wait for the node to sync with the network.

## Register as Validator

### Step 1: Create Account

Register your account with the management contract:

```bash
./marker createAccount \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --name "validator"
```

### Step 2: Authorize Signer

Authorize the signer address to sign blocks on behalf of your account:

```bash
./marker authorizeValidatorSigner \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --signerPriv <SIGNER_PRIVATE_KEY>
```

### Step 3: Lock MAP

Lock at least 1,000,000 MAP:

```bash
./marker lockedMAP \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --lockedNum 1000000
```

### Step 4: Register Validator

```bash
./marker register \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --signerPriv <SIGNER_PRIVATE_KEY> \
  --commission 150000
```

### Step 5: Vote for Yourself

Validators need votes to be elected. Vote for yourself:

```bash
./marker vote \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --target <YOUR_ACCOUNT_ADDRESS> \
  --voteNum 1000000
```

## Verify Registration

Check if you're registered as a validator:

```bash
./marker getTotalVotesForEligibleValidators --rpcaddr http://127.0.0.1:7445
```

Check if you're in the active validator set (after next epoch):

```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' \
  http://127.0.0.1:7445
```

---

## Advanced: Signature Separation

For enhanced security, you can separate the signing process from registration. This keeps your signer's private key more secure.

### Generate ECDSA Signature

```bash
./marker makeECDSASignatureFromSigner \
  --target <ACCOUNT_ADDRESS> \
  --signerPriv <SIGNER_PRIVATE_KEY>
```

Save the output signature (e.g., `0x59dff185...32f0d700`).

### Authorize by Signature

```bash
./marker authorizeValidatorSignerBySignature \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --signer <SIGNER_ADDRESS> \
  --signature <SIGNATURE_FROM_ABOVE>
```

### Generate Signer Proof

```bash
./marker generateSignerProof \
  --validator <ACCOUNT_ADDRESS> \
  --signerPriv <SIGNER_PRIVATE_KEY>
```

Save the output proof (e.g., `0xf90149b8...0e56f0ab1`).

### Register by Proof

```bash
./marker registerByProof \
  --rpcaddr http://127.0.0.1:7445 \
  --keystore ./account.json \
  --proof <PROOF_FROM_ABOVE> \
  --commission 150000
```

## Related

- [Voting & Withdrawal](./vote.md)
- [Marker Tool](./marker/overview.md)
- [Network Info](../network/relay-chain.md)
