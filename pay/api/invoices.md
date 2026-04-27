# Invoices API

The Invoices API is the core of ButterPay. It covers the full lifecycle of a one-time payment: create
an invoice from your server, share the payment link with your customer, let the payer submit an
on-chain transaction via the SDK, and receive confirmation via webhook. Additional endpoints expose
transaction history, aggregate summaries, CSV exports, live token balances, and refund status
management.

All authenticated endpoints require an `X-Api-Key` header unless noted otherwise. See
[Authentication](../authentication.md) for how to obtain and rotate keys.

---

- [CreateInvoice](#createinvoice)
- [GetInvoice](#getinvoice)
- [IssueSessionToken](#issuesessiontoken)
- [SubmitTransaction](#submittransaction)
- [ListTransactions](#listtransactions)
- [GetTransactionsSummary](#gettransactionssummary)
- [ExportTransactionsCsv](#exporttransactionscsv)
- [GetBalances](#getbalances)
- [RefundInvoice](#refundinvoice)

---

## CreateInvoice

Create a new USD-denominated invoice. The invoice is immediately payable via the hosted payment
page at `pay.butterpay.io/pay/<id>`. Token and chain are resolved by the payer at payment time;
you can optionally pre-set a preferred chain.

- **Method**: `POST`
- **Path**: `/v1/invoices`
- **Auth**: `X-Api-Key`
- **Description**: Creates an invoice scoped to the authenticated merchant. Returns the full
  invoice record including pre-computed `serviceFee` and `merchantReceived` amounts. The invoice
  expires 30 minutes after creation if no payment is initiated.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amountUsd` | string | Yes | Decimal USD amount. Must be a positive number, e.g. `"10.00"`. Supports up to 18 fractional digits. |
| `chain` | string | No | Preferred blockchain: `"arbitrum"`, `"ethereum"`, `"bsc"`, `"polygon"`. Displayed as default on the payment page; payer can change it. Defaults to `"ethereum"`. |
| `description` | string | No | Human-readable description shown to the payer on the payment page, e.g. `"Order #1234"`. |
| `merchantOrderId` | string | No | Your own order ID. Stored on the invoice for reconciliation; returned in webhooks. Max 255 characters. |
| `metadata` | object | No | Arbitrary JSON key-value pairs stored on the invoice. Not shown to the payer. Useful for tagging invoices with internal data. |
| `redirectUrl` | string | No | URL to redirect the payer to after a successful payment on the hosted page. |
| `webhookUrl` | string | No | Per-invoice webhook override. If set, payment events for this invoice are delivered here instead of the merchant's default webhook URL. |

### Returns

Full invoice record (same shape as the authenticated `GET /v1/invoices/:id`):

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantOrderId": "order_001",
  "amount": "10.000000000000000000",
  "token": "USD",
  "chain": "arbitrum",
  "status": "created",
  "description": "Order #1234",
  "metadata": { "userId": "usr_42" },
  "redirectUrl": "https://example.com/thank-you",
  "webhookUrl": null,
  "serviceFee": "0.080000000000000000",
  "merchantReceived": "9.920000000000000000",
  "referrerFee": null,
  "payerAddress": null,
  "txHash": null,
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "confirmedAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

### Invoice status values

| Status | Meaning |
|--------|---------|
| `created` | Invoice created, awaiting payment |
| `initiated` | Payer has submitted a transaction; awaiting on-chain confirmation |
| `confirmed` | Payment confirmed on-chain |
| `failed` | On-chain confirmation failed (e.g. wrong amount, reverted) |
| `expired` | Invoice expired before payment was initiated |
| `refunded` | Merchant has marked the invoice as refunded (API-side flag; see [RefundInvoice](#refundinvoice)) |

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/invoices \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -H "Content-Type: application/json" \
  -d '{
    "amountUsd": "10.00",
    "merchantOrderId": "order_001",
    "description": "Order #1234",
    "redirectUrl": "https://example.com/thank-you"
  }'
```

**Response** (HTTP 201):

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantOrderId": "order_001",
  "amount": "10.000000000000000000",
  "token": "USD",
  "chain": "ethereum",
  "status": "created",
  "description": "Order #1234",
  "metadata": null,
  "redirectUrl": "https://example.com/thank-you",
  "webhookUrl": null,
  "serviceFee": "0.080000000000000000",
  "merchantReceived": "9.920000000000000000",
  "referrerFee": null,
  "payerAddress": null,
  "txHash": null,
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "confirmedAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

After creating an invoice, share the payment link:
`https://pay.butterpay.io/pay/inv_01hwzq3k5n8ej4v2b7r9fcx6ds`

---

## GetInvoice

Retrieve a single invoice by ID. This endpoint is **public** — no authentication is required for
a minimal response. When called with a valid `X-Api-Key` that belongs to the invoice's owner
merchant, the full record is returned.

- **Method**: `GET`
- **Path**: `/v1/invoices/:id`
- **Auth**: Public (unauthenticated) or `X-Api-Key` (owner only, for full record)
- **Description**: Returns the invoice identified by `id`. Without an API key the response omits
  sensitive merchant and settlement fields (see below). The payment page SDK calls this endpoint
  unauthenticated to display invoice details and determine where to send funds.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Invoice ID, e.g. `inv_01hwzq3k5n8ej4v2b7r9fcx6ds`. |

### Returns

#### Without `X-Api-Key` (public subset)

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "amount": "10.000000000000000000",
  "description": "Order #1234",
  "status": "created",
  "chain": "arbitrum",
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "redirectUrl": "https://example.com/thank-you",
  "merchantName": "Acme Store",
  "merchantReceivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress",
    "ethereum": "0xMerchantWalletAddress"
  }
}
```

`merchantReceivingAddresses` is included in the public response so the payment SDK knows the
on-chain destination. This is read-only data; no sensitive merchant information is exposed.

#### With valid `X-Api-Key` matching invoice owner (full record)

Adds the following fields on top of the public subset:

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "amount": "10.000000000000000000",
  "description": "Order #1234",
  "status": "confirmed",
  "chain": "arbitrum",
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "redirectUrl": "https://example.com/thank-you",
  "merchantName": "Acme Store",
  "merchantReceivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  },
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantOrderId": "order_001",
  "serviceFee": "0.080000000000000000",
  "merchantReceived": "9.920000000000000000",
  "referrerFee": null,
  "metadata": { "userId": "usr_42" },
  "payerAddress": "0xPayerWalletAddress",
  "txHash": "0xabc123...on-chain-tx-hash",
  "webhookUrl": null,
  "token": "USDT"
}
```

The additional fields (`merchantId`, `merchantOrderId`, `serviceFee`, `merchantReceived`,
`referrerFee`, `metadata`, `payerAddress`, `txHash`, `webhookUrl`, `token`) are only returned when
the `X-Api-Key` header is present and matches the invoice's owning merchant. Any other API key, or
no key at all, returns the public subset only.

### Example

**Unauthenticated (public) request:**

```bash
curl https://api.butterpay.io/v1/invoices/inv_01hwzq3k5n8ej4v2b7r9fcx6ds
```

**Response:**

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "amount": "10.000000000000000000",
  "description": "Order #1234",
  "status": "created",
  "chain": "arbitrum",
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "redirectUrl": "https://example.com/thank-you",
  "merchantName": "Acme Store",
  "merchantReceivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  }
}
```

**Authenticated (owner) request:**

```bash
curl https://api.butterpay.io/v1/invoices/inv_01hwzq3k5n8ej4v2b7r9fcx6ds \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** includes all fields listed in [full record](#with-valid-x-api-key-matching-invoice-owner-full-record) above.

---

## IssueSessionToken

Issue a short-lived session token bound to a specific invoice and payer address. The session token
is required to submit a transaction via [SubmitTransaction](#submittransaction). This endpoint is
**public** and does not require an API key.

- **Method**: `POST`
- **Path**: `/v1/invoices/:id/session`
- **Auth**: Public (no authentication required)
- **Description**: Validates that the invoice exists and is in the `created` state and has not
  expired, then returns a signed JWT (`sessionToken`) that binds the payer wallet address to this
  specific invoice for 30 minutes. The SDK calls this after the user connects their wallet.
  Rate-limited to 20 requests per minute per IP.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Invoice ID. |
| `payerAddress` | string (body) | Yes | The on-chain address of the wallet that will send the payment, e.g. `"0xAbCd..."`. Must be a valid EVM address. |

### Returns

```json
{
  "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

The `sessionToken` is a signed JWT containing `invoiceId` and `payerAddress`. It expires 30
minutes after issuance. Pass it in the body of `POST /v1/invoices/:id/tx`.

### Error responses

| Status | Error | Meaning |
|--------|-------|---------|
| 400 | `payerAddress is required` | Body missing `payerAddress`. |
| 404 | `Invoice not found or not payable` | Invoice does not exist or is not in `created` status. |
| 410 | `Invoice expired` | Invoice's `expiresAt` is in the past. |

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/invoices/inv_01hwzq3k5n8ej4v2b7r9fcx6ds/session \
  -H "Content-Type: application/json" \
  -d '{"payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12"}'
```

**Response:**

```json
{
  "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpbnZvaWNlSWQiOiJpbnZfMDFod3pxM2s1bjhlajR2MmI3cjlmY3g2ZHMiLCJwYXllckFkZHJlc3MiOiIweEFiQ2RFZjEyMzQ1Njc4OTBBYkNkRWYxMjM0NTY3ODkwQWJDZEVmMTIiLCJpYXQiOjE3NDU3NTIwMDAsImV4cCI6MTc0NTc1MzgwMH0.signature"
}
```

---

## SubmitTransaction

Submit the on-chain transaction hash after the payer has signed and broadcast the payment
transaction. Requires a valid session token from [IssueSessionToken](#issuesessiontoken).

- **Method**: `POST`
- **Path**: `/v1/invoices/:id/tx`
- **Auth**: `sessionToken` (in request body — not a header)
- **Description**: Validates the session token, verifies it matches the invoice ID and payer
  address in the request body, then marks the invoice as `initiated` and starts on-chain
  tracking. Returns a tracker ID and current status. Rate-limited to 5 requests per minute per IP.
  The invoice status will advance to `confirmed` or `failed` asynchronously once the transaction
  reaches the required number of confirmations.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Invoice ID. |
| `sessionToken` | string (body) | Yes | Session token obtained from `POST /v1/invoices/:id/session`. |
| `txHash` | string (body) | Yes | On-chain transaction hash, e.g. `"0xabc123..."`. |
| `payerAddress` | string (body) | Yes | Payer wallet address. Must match the address used when obtaining the session token. |
| `toAddress` | string (body) | Yes | The merchant receiving address funds were sent to. |
| `chain` | string (body) | Yes | Chain on which the transaction was submitted, e.g. `"arbitrum"`. |
| `token` | string (body) | Yes | Token used for payment: `"USDT"` or `"USDC"`. |

### Returns

```json
{
  "txId": "tx_01hwzq3k5n8ej4v2b7r9zzz999",
  "status": "tracking"
}
```

`txId` is the internal transaction tracker ID. The invoice status transitions to `initiated`
immediately; it will be updated to `confirmed` or `failed` once the backend verifies the
on-chain event.

### Error responses

| Status | Error | Meaning |
|--------|-------|---------|
| 400 | `invoice is <status>, expected created` | Invoice is not in `created` status (already paid, expired, etc.). |
| 400 | `txHash, payerAddress, chain, and token are required` | One or more required body fields are missing. |
| 403 | `sessionToken is required` | Body does not contain `sessionToken`. |
| 403 | `Invalid or expired session` | Session token is expired or its signature is invalid. |
| 403 | `Session does not match invoice` | Session token was issued for a different invoice ID. |
| 403 | `Payer address mismatch` | `payerAddress` in the body does not match the address in the session token. |
| 404 | `invoice not found` | Invoice ID does not exist. |

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/invoices/inv_01hwzq3k5n8ej4v2b7r9fcx6ds/tx \
  -H "Content-Type: application/json" \
  -d '{
    "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "txHash": "0xabc123def456789012345678901234567890123456789012345678901234567890",
    "payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12",
    "toAddress": "0xMerchantWalletAddress",
    "chain": "arbitrum",
    "token": "USDT"
  }'
```

**Response:**

```json
{
  "txId": "tx_01hwzq3k5n8ej4v2b7r9zzz999",
  "status": "tracking"
}
```

---

## ListTransactions

List all invoices (transactions) for the authenticated merchant, with optional filters. Ordered
by `createdAt` descending (newest first). Supports offset-based pagination.

- **Method**: `GET`
- **Path**: `/v1/transactions`
- **Auth**: `X-Api-Key`
- **Description**: Returns all invoices belonging to the authenticated merchant. Supports
  filtering by status, chain, token, and date range. Use `limit` and `offset` to paginate
  through large result sets.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string | No | Filter by invoice status: `created`, `initiated`, `confirmed`, `failed`, `expired`, or `refunded`. |
| `chain` | string | No | Filter by chain, e.g. `"arbitrum"`. |
| `token` | string | No | Filter by token, e.g. `"USDT"` or `"USDC"`. |
| `from` | string | No | ISO 8601 datetime. Return only invoices created at or after this time, e.g. `"2026-04-01T00:00:00Z"`. |
| `to` | string | No | ISO 8601 datetime. Return only invoices created at or before this time, e.g. `"2026-04-30T23:59:59Z"`. |
| `limit` | string | No | Maximum number of results to return. Defaults to `50`. Maximum `10000` (for bulk export use [ExportTransactionsCsv](#exporttransactionscsv)). |
| `offset` | string | No | Number of results to skip for pagination. Defaults to `0`. |

### Returns

```json
{
  "data": [
    {
      "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
      "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
      "merchantOrderId": "order_001",
      "amount": "10.000000000000000000",
      "token": "USDT",
      "chain": "arbitrum",
      "status": "confirmed",
      "payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12",
      "txHash": "0xabc123...",
      "serviceFee": "0.080000000000000000",
      "merchantReceived": "9.920000000000000000",
      "referrerFee": null,
      "description": "Order #1234",
      "metadata": null,
      "redirectUrl": null,
      "webhookUrl": null,
      "expiresAt": "2026-04-27T12:30:00.000Z",
      "confirmedAt": "2026-04-27T12:05:43.000Z",
      "createdAt": "2026-04-27T12:00:00.000Z",
      "updatedAt": "2026-04-27T12:05:43.000Z"
    }
  ],
  "count": 1
}
```

`count` reflects the number of records returned in this page, not the total matching count.

### Example

**Request — confirmed transactions for April 2026:**

```bash
curl "https://api.butterpay.io/v1/transactions?status=confirmed&from=2026-04-01T00:00:00Z&to=2026-04-30T23:59:59Z&limit=50&offset=0" \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response:**

```json
{
  "data": [
    {
      "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
      "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
      "merchantOrderId": "order_001",
      "amount": "10.000000000000000000",
      "token": "USDT",
      "chain": "arbitrum",
      "status": "confirmed",
      "payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12",
      "txHash": "0xabc123def456...",
      "serviceFee": "0.080000000000000000",
      "merchantReceived": "9.920000000000000000",
      "referrerFee": null,
      "description": null,
      "metadata": null,
      "redirectUrl": null,
      "webhookUrl": null,
      "expiresAt": "2026-04-27T12:30:00.000Z",
      "confirmedAt": "2026-04-27T12:05:43.000Z",
      "createdAt": "2026-04-27T12:00:00.000Z",
      "updatedAt": "2026-04-27T12:05:43.000Z"
    }
  ],
  "count": 1
}
```

---

## GetTransactionsSummary

Get aggregate totals for confirmed transactions in a date range. Useful for reconciliation dashboards
and monthly revenue reports.

- **Method**: `GET`
- **Path**: `/v1/transactions/summary`
- **Auth**: `X-Api-Key`
- **Description**: Returns aggregate totals (count, gross volume, service fees, net received)
  for all `confirmed` invoices in the specified date range. If no date range is given, totals
  cover all time.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | string | No | ISO 8601 datetime. Start of the date range, e.g. `"2026-04-01T00:00:00Z"`. |
| `to` | string | No | ISO 8601 datetime. End of the date range, e.g. `"2026-04-30T23:59:59Z"`. |

### Returns

```json
{
  "totalCount": 42,
  "totalAmount": "1250.000000000000000000",
  "totalServiceFee": "10.000000000000000000",
  "totalMerchantReceived": "1240.000000000000000000"
}
```

All monetary values are strings representing decimal numbers with up to 18 fractional digits.
`totalCount` is an integer.

### Example

**Request:**

```bash
curl "https://api.butterpay.io/v1/transactions/summary?from=2026-04-01T00:00:00Z&to=2026-04-30T23:59:59Z" \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response:**

```json
{
  "totalCount": 42,
  "totalAmount": "1250.000000000000000000",
  "totalServiceFee": "10.000000000000000000",
  "totalMerchantReceived": "1240.000000000000000000"
}
```

---

## ExportTransactionsCsv

Download transaction history as a CSV file. Equivalent to `GET /v1/transactions` but returns
a downloadable file with up to 10,000 rows.

- **Method**: `GET`
- **Path**: `/v1/transactions/export`
- **Auth**: `X-Api-Key`
- **Description**: Returns a `text/csv` file attachment named
  `butterpay-YYYY-MM-DD.csv` containing all matching transactions for the authenticated merchant.
  Supports the same `status`, `from`, and `to` filters as `ListTransactions`. Up to 10,000 rows
  are included in a single export.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | string | No | ISO 8601 datetime. Include only invoices created at or after this time. |
| `to` | string | No | ISO 8601 datetime. Include only invoices created at or before this time. |
| `status` | string | No | Filter by status, e.g. `confirmed`. |

### Returns

Response is a CSV file with `Content-Type: text/csv; charset=utf-8` and a
`Content-Disposition: attachment; filename="butterpay-2026-04-27.csv"` header.

CSV columns (in order):

| Column | Description |
|--------|-------------|
| `id` | Invoice ID |
| `merchant_order_id` | Your order ID |
| `amount_usd` | Gross USD amount |
| `token` | Token used: `USDT` or `USDC` |
| `chain` | Chain name |
| `status` | Invoice status |
| `tx_hash` | On-chain transaction hash |
| `payer_address` | Payer wallet address |
| `service_fee` | ButterPay fee (0.8%) |
| `merchant_received` | Net amount after fee |
| `created_at` | ISO 8601 creation timestamp |
| `confirmed_at` | ISO 8601 confirmation timestamp (blank if not confirmed) |

### Example

**Request:**

```bash
curl "https://api.butterpay.io/v1/transactions/export?status=confirmed&from=2026-04-01T00:00:00Z" \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -o butterpay-export.csv
```

**Response** (CSV body):

```
id,merchant_order_id,amount_usd,token,chain,status,tx_hash,payer_address,service_fee,merchant_received,created_at,confirmed_at
inv_01hwzq3k5n8ej4v2b7r9fcx6ds,order_001,10.000000000000000000,USDT,arbitrum,confirmed,0xabc123...,0xAbCdEf...,0.080000000000000000,9.920000000000000000,2026-04-27T12:00:00.000Z,2026-04-27T12:05:43.000Z
```

---

## GetBalances

Retrieve live on-chain token balances for all receiving addresses configured on the merchant account.
Useful for monitoring settlement and knowing when to sweep funds.

- **Method**: `GET`
- **Path**: `/v1/balances`
- **Auth**: `X-Api-Key`
- **Description**: Reads the merchant's configured receiving addresses and queries live on-chain
  token balances across all supported chains. Returns an empty `balances` array with a
  setup prompt if no receiving addresses have been configured yet.

### Parameters

None.

### Returns

```json
{
  "balances": [
    {
      "chain": "arbitrum",
      "address": "0xMerchantWalletAddress",
      "token": "USDT",
      "balance": "1240.920000",
      "decimals": 6
    },
    {
      "chain": "arbitrum",
      "address": "0xMerchantWalletAddress",
      "token": "USDC",
      "balance": "0.000000",
      "decimals": 6
    }
  ]
}
```

If no receiving addresses are configured, the response is:

```json
{
  "balances": [],
  "message": "No receiving addresses configured. Set them in Settings."
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/balances \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response:**

```json
{
  "balances": [
    {
      "chain": "arbitrum",
      "address": "0xMerchantWalletAddress",
      "token": "USDT",
      "balance": "1240.920000",
      "decimals": 6
    }
  ]
}
```

---

## RefundInvoice

Mark a confirmed invoice as refunded. This is a bookkeeping operation — it updates the invoice
status in the ButterPay system. It does **not** initiate an on-chain refund transaction; the
merchant must execute the on-chain transfer to the payer separately.

- **Method**: `POST`
- **Path**: `/v1/invoices/:id/refund`
- **Auth**: `X-Api-Key`
- **Description**: Sets a `confirmed` invoice's status to `refunded`. Only invoices in `confirmed`
  status can be marked as refunded. The invoice must belong to the authenticated merchant.
  After marking an invoice as refunded, the `payment.refunded` webhook event (if configured)
  is not triggered automatically — refund webhooks must be managed manually or via a future
  webhook event. The updated invoice record is returned.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Invoice ID. Must belong to the authenticated merchant and be in `confirmed` status. |

### Returns

Full invoice record with `status` updated to `"refunded"`:

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantOrderId": "order_001",
  "amount": "10.000000000000000000",
  "token": "USDT",
  "chain": "arbitrum",
  "status": "refunded",
  "payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12",
  "txHash": "0xabc123def456...",
  "serviceFee": "0.080000000000000000",
  "merchantReceived": "9.920000000000000000",
  "referrerFee": null,
  "description": "Order #1234",
  "metadata": null,
  "redirectUrl": null,
  "webhookUrl": null,
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "confirmedAt": "2026-04-27T12:05:43.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T13:00:00.000Z"
}
```

### Error responses

| Status | Error | Meaning |
|--------|-------|---------|
| 400 | `only confirmed invoices can be refunded` | Invoice is not in `confirmed` status. |
| 404 | `not found` | Invoice does not exist or belongs to a different merchant. |

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/invoices/inv_01hwzq3k5n8ej4v2b7r9fcx6ds/refund \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response:**

```json
{
  "id": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantOrderId": "order_001",
  "amount": "10.000000000000000000",
  "token": "USDT",
  "chain": "arbitrum",
  "status": "refunded",
  "payerAddress": "0xAbCdEf1234567890AbCdEf1234567890AbCdEf12",
  "txHash": "0xabc123def456...",
  "serviceFee": "0.080000000000000000",
  "merchantReceived": "9.920000000000000000",
  "referrerFee": null,
  "description": "Order #1234",
  "metadata": null,
  "redirectUrl": null,
  "webhookUrl": null,
  "expiresAt": "2026-04-27T12:30:00.000Z",
  "confirmedAt": "2026-04-27T12:05:43.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T13:00:00.000Z"
}
```

---

## See also

- [Webhooks](../webhooks.md) — event catalog, payload schemas, signature verification, and retry schedule
- [Authentication](../authentication.md) — API key generation, Bearer JWT, sessionToken modes, and rotation
- [Plans & Subscriptions API](plans-subscriptions.md) — recurring billing via EIP-2612 permit-based subscriptions
