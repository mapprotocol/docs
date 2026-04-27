# Webhooks Management API

These endpoints manage webhook configuration — delivery logs, signing secret retrieval, test
dispatches, and secret rotation. For event semantics, payload shapes, signature verification, and
retry policy, see [Webhooks](../webhooks.md).

All endpoints require authentication. `ListLogs`, `GetSecret`, and `SendTest` accept an API key
(`X-Api-Key` or `Authorization: Bearer <jwt>`). `RotateSecret` is intentionally restricted to a short-lived
**Bearer JWT** because secret rotation is a security-sensitive operation.

---

- [ListLogs](#listlogs)
- [GetSecret](#getsecret)
- [SendTest](#sendtest)
- [RotateSecret](#rotatesecret)

---

## ListLogs

Return paginated webhook delivery logs for the authenticated merchant.

- **Method**: `GET`
- **Path**: `/v1/webhooks/logs`
- **Auth**: `apiKeyAuth (Bearer or X-Api-Key)`
- **Description**: Fetches webhook delivery records scoped to the current merchant, ordered newest
  first. Each record shows the target URL, HTTP status received, number of delivery attempts, and
  whether the delivery ultimately succeeded. Use this to debug missed events or audit delivery
  history.

### Parameters

Query string parameters:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | integer | No | Maximum records to return. Defaults to `50`, capped at `100`. |
| `offset` | integer | No | Number of records to skip for pagination. Defaults to `0`. |

### Returns

```json
{
  "data": [
    {
      "id": "whl_01hwzq3k5n8ej4v2b7r9xyz789",
      "invoiceId": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
      "event": "payment.confirmed",
      "url": "https://acme.io/webhooks/butter",
      "statusCode": 200,
      "attempts": 1,
      "success": true,
      "nextRetryAt": null,
      "response": "ok",
      "createdAt": "2026-04-27T12:05:00.000Z"
    }
  ],
  "count": 1
}
```

`nextRetryAt` is `null` when `success` is `true` or when no further retries are scheduled.
`statusCode` is `0` when the delivery failed with a network error (no HTTP response received).

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/webhooks/logs?limit=20&offset=0 \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "data": [
    {
      "id": "whl_01hwzq3k5n8ej4v2b7r9xyz789",
      "invoiceId": "inv_01hwzq3k5n8ej4v2b7r9fcx6ds",
      "event": "payment.confirmed",
      "url": "https://acme.io/webhooks/butter",
      "statusCode": 200,
      "attempts": 1,
      "success": true,
      "nextRetryAt": null,
      "response": "ok",
      "createdAt": "2026-04-27T12:05:00.000Z"
    }
  ],
  "count": 1
}
```

---

## GetSecret

Retrieve the current webhook signing secret and signature verification instructions.

- **Method**: `GET`
- **Path**: `/v1/webhooks/secret`
- **Auth**: `apiKeyAuth (Bearer or X-Api-Key)`
- **Description**: Returns the merchant's active webhook secret alongside a machine-readable
  description of the signature format. Use the verification steps in the response to implement
  or verify your signature-checking code. Returns `null` for `webhookSecret` if no secret has been
  generated yet — call `RotateSecret` to create one.

### Parameters

None. Merchant identity is derived from the API key.

### Returns

```json
{
  "webhookSecret": "whsec_a1b2c3d4e5f6...",
  "signatureFormat": "t=<unix_timestamp>,v1=<hmac_hex>",
  "signatureAlgorithm": "HMAC-SHA256",
  "signedContent": "<timestamp>.<json_body>",
  "verificationSteps": [
    "1. Extract t and v1 from X-ButterPay-Signature header",
    "2. Compute: hmac_sha256(webhook_secret, t + '.' + raw_body)",
    "3. Compare computed HMAC with v1 (constant-time)",
    "4. Verify t is within 300 seconds of current time (replay protection)"
  ]
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/webhooks/secret \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "webhookSecret": "whsec_a1b2c3d4e5f6...",
  "signatureFormat": "t=<unix_timestamp>,v1=<hmac_hex>",
  "signatureAlgorithm": "HMAC-SHA256",
  "signedContent": "<timestamp>.<json_body>",
  "verificationSteps": [
    "1. Extract t and v1 from X-ButterPay-Signature header",
    "2. Compute: hmac_sha256(webhook_secret, t + '.' + raw_body)",
    "3. Compare computed HMAC with v1 (constant-time)",
    "4. Verify t is within 300 seconds of current time (replay protection)"
  ]
}
```

---

## SendTest

Dispatch a synthetic `payment.test` event to the merchant's configured webhook URL.

- **Method**: `POST`
- **Path**: `/v1/webhooks/test`
- **Auth**: `apiKeyAuth (Bearer or X-Api-Key)`
- **Description**: Immediately POSTs a test payload to the `webhookUrl` stored on the merchant
  record. The payload is signed with the merchant's webhook secret using the standard
  `X-ButterPay-Signature` scheme, so your handler can validate the signature exactly as it would
  for a live event. Returns `400` if no webhook URL is configured or the URL fails validation.
  Returns `502` if the URL is reachable but the delivery throws a network error (e.g. connection
  refused, timeout after 10 seconds).

### Parameters

None.

### Synthetic payload

The test POST body sent to your endpoint has the following fixed shape:

```json
{
  "event": "payment.test",
  "invoiceId": "inv_test_000000000000",
  "merchantOrderId": "test-order",
  "amountUsd": "1.00",
  "token": "USDT",
  "chain": "arbitrumSepolia",
  "txHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp": "2026-04-27T12:00:00.000Z"
}
```

The request also carries two headers your handler can inspect:

| Header | Value |
|--------|-------|
| `X-ButterPay-Signature` | HMAC-SHA256 signature in the standard `t=...,v1=...` format |
| `X-ButterPay-Event` | `payment.test` |

### Returns

```json
{
  "success": true,
  "statusCode": 200,
  "url": "https://acme.io/webhooks/butter",
  "response": "ok"
}
```

`success` reflects whether your endpoint returned a `2xx` status. `response` contains the raw
response body (truncated at the transport layer if very long). On network failure the response is
`502` with `"success": false` and an `"error"` field instead of `"response"`.

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/webhooks/test \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200 — delivery succeeded):

```json
{
  "success": true,
  "statusCode": 200,
  "url": "https://acme.io/webhooks/butter",
  "response": "ok"
}
```

**Response** (HTTP 200 — delivery returned non-2xx from your server):

```json
{
  "success": false,
  "statusCode": 500,
  "url": "https://acme.io/webhooks/butter",
  "response": "Internal Server Error"
}
```

---

## RotateSecret

Generate a new webhook signing secret, immediately replacing the current one.

- **Method**: `POST`
- **Path**: `/v1/webhook-secret/rotate`
- **Auth**: `Bearer JWT only`
- **Description**: Generates a cryptographically random webhook secret and persists it. The
  previous secret is **invalidated immediately** — any in-flight webhook deliveries still signed
  with the old secret will fail signature verification on your end once you update your handler.
  The new secret is returned **once** in this response and cannot be retrieved again in plaintext
  (subsequent calls to `GetSecret` will return the stored value, but after rotation you should
  treat the value from this response as the source of truth and store it securely). Requires a
  short-lived JWT rather than an API key because secret rotation is a security-sensitive operation.

### Parameters

None. Merchant identity is derived from the JWT.

### Returns

```json
{
  "webhookSecret": "whsec_9z8y7x6w5v4u...",
  "message": "Store this secret securely. It cannot be retrieved again."
}
```

> **Important:** Save `webhookSecret` immediately on receipt. There is no endpoint to recover the
> plaintext value after this response is discarded. If you lose it, rotate again.

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/webhook-secret/rotate \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Response** (HTTP 200):

```json
{
  "webhookSecret": "whsec_9z8y7x6w5v4u...",
  "message": "Store this secret securely. It cannot be retrieved again."
}
```

Returns `401` if the `Authorization` header is absent, the token is expired, or the merchant
account is inactive. An API key (`X-Api-Key`) is not accepted for this endpoint.

---

## See also

- [Webhooks](../webhooks.md) — events, payload shapes, signing, and retry policy
- [Authentication](../authentication.md) — JWT and API key lifecycle
