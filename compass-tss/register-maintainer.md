# Register as Maintainer

## Overview

To participate in MAP Protocol v2's TSS network, you must register as a Maintainer on MAP Relay Chain. This document explains the registration process.

## Prerequisites

Before registering as Maintainer:

1. **Running Validator**: Active validator on MAP Relay Chain
2. **Sufficient Stake**: Meet minimum staking requirements
3. **Compass-TSS Ready**: Node software installed and configured

## Registration Process

### Step 1: Check Validator Status

Ensure your validator is active:

```bash
# Check validator status
marker getValidator --rpcaddr <rpc-url> --address <validator-address>
```

Expected output should show:
- `isValidator: true`
- `isElected: true` (if currently in validator set)

### Step 2: Prepare Registration Information

Gather the following information:

| Field | Description | Example |
|-------|-------------|---------|
| Validator Address | Your validator's address | 0x1234...abcd |
| P2P Public Key | Compass-TSS P2P identity | Generated during setup |
| P2P Endpoint | Your node's P2P address | 192.168.1.1:8080 |

### Step 3: Generate P2P Identity

If not already done during Compass-TSS setup:

```bash
# Generate P2P key pair
compass-tss keys generate --output ./keys/p2p
```

This creates:
- `p2p.key`: Private key (keep secure)
- `p2p.pub`: Public key (used for registration)

### Step 4: Register on Chain

Call the Maintainer Manager contract:

```bash
# Register as Maintainer
marker registerMaintainer \
  --rpcaddr <rpc-url> \
  --keystore <keystore-path> \
  --p2pPubKey <p2p-public-key> \
  --p2pEndpoint <ip:port>
```

### Step 5: Verify Registration

Check your registration status:

```bash
# Check Maintainer status
marker getMaintainer --rpcaddr <rpc-url> --address <your-address>
```

## Election Process

### How Election Works

1. **Registration**: Maintainers register during open period
2. **Snapshot**: At epoch boundary, registered Maintainers are snapshotted
3. **Selection**: Top N Maintainers by stake are elected
4. **Activation**: Elected Maintainers participate in next epoch

### Election Parameters

| Parameter | Value |
|-----------|-------|
| Election Period | Every epoch (~1 day) |
| Active Set Size | Variable (e.g., 21) |
| Selection Criteria | Stake-weighted |

### Check Election Status

```bash
# Check if elected for current epoch
marker isElectedMaintainer --rpcaddr <rpc-url> --address <your-address>
```

## After Registration

### Wait for Election

- Registration doesn't guarantee immediate participation
- You'll be considered in the next election cycle
- Higher stake increases election probability

### Start Compass-TSS

Once registered, start your Compass-TSS node:

```bash
# Start Compass-TSS
compass-tss start --config ./config.toml
```

### Monitor Status

Check your Maintainer status regularly:

```bash
# Check participation metrics
compass-tss status
```

## Updating Registration

### Update P2P Endpoint

If your IP changes:

```bash
marker updateMaintainer \
  --rpcaddr <rpc-url> \
  --keystore <keystore-path> \
  --p2pEndpoint <new-ip:port>
```

### Update P2P Key

If you need to rotate your P2P key:

```bash
# Generate new key
compass-tss keys generate --output ./keys/p2p-new

# Update on chain
marker updateMaintainer \
  --rpcaddr <rpc-url> \
  --keystore <keystore-path> \
  --p2pPubKey <new-p2p-public-key>
```

## Deregistration

To stop being a Maintainer:

```bash
marker deregisterMaintainer \
  --rpcaddr <rpc-url> \
  --keystore <keystore-path>
```

**Note**: Deregistration may have a cooldown period. Check current protocol parameters.

## Troubleshooting

### Registration Failed

| Error | Solution |
|-------|----------|
| "Not a validator" | Ensure validator is registered and active |
| "Insufficient stake" | Lock additional MAPO tokens |
| "Invalid P2P key" | Regenerate P2P key pair |
| "Registration closed" | Wait for next registration period |

### Not Getting Elected

- Increase your stake
- Ensure validator is performing well (not jailed)
- Check for pending slashing

### P2P Connection Issues

- Verify firewall allows P2P port
- Check P2P endpoint is reachable
- Ensure correct P2P public key registered

## Best Practices

1. **Reliable Infrastructure**: Use stable, well-connected servers
2. **Monitor Continuously**: Set up alerts for node issues
3. **Keep Software Updated**: Update Compass-TSS promptly
4. **Secure Keys**: Backup P2P and TSS keys securely
5. **Maintain Stake**: Keep stake above minimum to remain competitive
