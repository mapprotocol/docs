# Overview

ButterPay is non-custodial crypto payment infrastructure for stablecoin settlement. Merchants create
invoices denominated in USD; customers pay with USDT or USDC on a supported chain; settlement is
atomic and on-chain — funds go directly from payer to merchant wallet, never through ButterPay.
Webhook delivery follows the Stripe signing format (`t=...,v1=hmac`), so existing webhook tooling
works without modification. A flat 0.8% service fee is deducted at the moment of on-chain settlement.

## How it works

1. **Register** — create a merchant account, configure a receiving address, and generate an API key
   in the dashboard Settings page.
2. **Create invoice** — `POST /v1/invoices` with `amountUsd` and an optional `externalId`. The API
   returns an `invoiceId` and a hosted payment URL (`pay.butterpay.io/pay/<invoiceId>`).
3. **Customer pays** — the customer opens the payment URL, connects a wallet, selects a chain and
   token, and signs the on-chain transaction. `PaymentRouter` atomically splits the transfer:
   99.2% to the merchant address and 0.8% to the fee collector in a single transaction.
4. **Webhook delivered** — the backend detects the `PaymentProcessed` on-chain event, verifies the
   amount, marks the invoice `confirmed`, and POST the `payment.confirmed` event to the merchant's
   configured webhook URL.

## Architecture

- **Frontend**: `pay.butterpay.io` — hosted payment pages (`/pay/<id>`) and subscription pages
  (`/subscribe/<planId>`); merchant dashboard at the same domain.
- **Backend API**: `api.butterpay.io` — REST API, accepts `Authorization: Bearer <jwt>` or
  `X-Api-Key: <key>` depending on the endpoint.
- **Smart contracts**: `PaymentRouter` handles one-time payments; `SubscriptionManager` handles
  recurring charges. Both are deployed on supported chains (see [Supported chains](#supported-chains)).

## Supported chains

| Chain | Status | Chain ID |
|-------|--------|----------|
| Arbitrum | Live | 42161 |
| Ethereum | Coming soon (audit pending) | 1 |
| BSC | Coming soon (audit pending) | 56 |
| Polygon | Coming soon (audit pending) | 137 |

### Arbitrum mainnet contract addresses

Deployed 2026-04-16.

| Contract | Address |
|----------|---------|
| PaymentRouter | [`0x4b32bcd3eC4F0a14D7061e0d239eBAd84F77743f`](https://arbiscan.io/address/0x4b32bcd3eC4F0a14D7061e0d239eBAd84F77743f) |
| SubscriptionManager | [`0xBC7a2Ea456AE255C67b98Bd9559F15Fc742C0C66`](https://arbiscan.io/address/0xBC7a2Ea456AE255C67b98Bd9559F15Fc742C0C66) |
| Splitter | [`0xB1a5050EA64e94467621207174E964F42D59aBc7`](https://arbiscan.io/address/0xB1a5050EA64e94467621207174E964F42D59aBc7) |

## Supported tokens

USDT and USDC are accepted on all live chains. Invoices are denominated in USD; the contract
converts 1 USD → 1 token unit, adjusted for the token's decimal precision on that chain.

| Token | Arbitrum address | Decimals |
|-------|-----------------|----------|
| USDT | `0xFd086bC7CD5C481DCC9C85ebE478A1C0b69FCbb9` | 6 |
| USDC | `0xaf88d065e77c8cC2239327C5EDb3A432268e5831` | 6 |

Note: USDT on BSC uses 18 decimals, not 6. Amount validation in the backend applies the correct
decimal precision per chain.

## Fees

- **Rate**: 0.8% (80 bps) per transaction.
- **Timing**: deducted atomically at settlement — one on-chain transaction, no separate sweep.
- **Merchant receives**: `amountUsd × (1 - 0.008)` in the chosen stablecoin.

Example: a $100 invoice results in $99.20 to the merchant and $0.80 to the fee collector, settled
in the same transaction.

## Why ButterPay

- **Non-custodial** — funds never touch ButterPay; settlement is direct from payer to merchant via
  on-chain contract, so ButterPay cannot freeze or misappropriate merchant funds.
- **Multi-chain** — one API integration works across all supported chains; adding a new chain
  requires no changes to merchant code.
- **Stripe-compatible** — webhook signing uses the `t=<timestamp>,v1=<hmac>` format and event
  semantics mirror Stripe, so existing webhook handlers and verification libraries work as-is.

## Where to next

- [Getting Started](getting-started.md)
- [Authentication](authentication.md)
- [Webhooks](webhooks.md)
- [API Reference](api/invoices.md)
- [SDK Reference](sdk/core.md)
