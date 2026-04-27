# Authentication

ButterPay uses three credential types depending on the context. This guide covers how each is
obtained, how to use it, and when to prefer one over another.

---

## Overview

| Type | Header / location | Use case | Lifetime |
|------|-------------------|----------|----------|
| Bearer JWT | `Authorization: Bearer <token>` | Dashboard sessions, API access | 24 h |
| API key | `X-Api-Key: <key>` | Server-to-server requests | Until rotated |
| sessionToken | Request body field | Payment-page transaction submission | 30 min |

Most server integrations use the API key exclusively after the initial setup. Bearer JWTs are
primarily used during onboarding (configuring addresses, generating the API key) and for
security-sensitive operations such as key rotation.

---

## Bearer JWT

### Obtaining a token

Call `POST /v1/auth/login` with your merchant credentials.

```bash
curl -X POST https://api.butterpay.io/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "you@example.com",
    "password": "s3cur3passw0rd"
  }'
```

**Response**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "merchant": {
    "id": "mer_01hwz3k9x2f8b4c6e7g9h0j1km",
    "name": "Acme Store",
    "email": "you@example.com",
    "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
    "serviceFeeBps": 80,
    "webhookUrl": "https://your-server.example.com/webhooks/butterpay",
    "receivingAddresses": { "arbitrum": "0xYourAddress" }
  }
}
```

The `token` field is a signed JWT. It expires 24 hours after issuance. Pass it in the
`Authorization` header of subsequent requests:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Verifying the current session

`GET /v1/auth/me` returns the authenticated merchant object and can be used to confirm that a
token is still valid. This endpoint accepts Bearer JWT only.

```bash
curl https://api.butterpay.io/v1/auth/me \
  -H "Authorization: Bearer <token>"
```

The response is the same merchant shape shown above. A `401` means the token has expired or is
invalid — re-authenticate via `/v1/auth/login`.

> The login endpoint is rate-limited to 10 requests per minute per IP.

---

## API key (X-Api-Key)

API keys are intended for server-to-server calls: creating invoices, querying invoice status,
and any backend automation. They do not expire on a schedule — they remain valid until you
rotate them.

Once you have an API key, pass it in the `X-Api-Key` header:

```bash
curl -X POST https://api.butterpay.io/v1/invoices \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{ "amountUsd": "49.99" }'
```

The database stores a SHA-256 hash of the key plus the first 12 characters as a searchable
prefix. The plain-text key is returned only once, at generation time — it cannot be retrieved
later. If you lose it, you must rotate.

---

## API key generation

`POST /v1/merchants/me/generate-key` issues your API key for the first time.

**Pre-condition**: at least one chain must have a receiving address configured. If no addresses
are present the endpoint returns a `400` error: `"Configure a receiving address first before
generating an API Key."` Configure addresses with `PATCH /v1/merchants/me` before calling this
endpoint (see [Getting Started](getting-started.md)).

This endpoint uses `apiKeyAuth` middleware, which accepts either Bearer JWT or an existing
`X-Api-Key`. For a brand-new merchant account the API key does not yet exist, so use the Bearer
JWT from login.

```bash
curl -X POST https://api.butterpay.io/v1/merchants/me/generate-key \
  -H "Authorization: Bearer <token>"
```

**Response**

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "message": "Store this key securely. It will not be shown again."
}
```

Copy the `apiKey` value immediately and store it in a secrets manager or environment variable.
The API will never return the plain-text key again. If you call this endpoint a second time it
returns a `400`: `"API Key already exists. Use the Rotate button to generate a new one."`

---

## API key rotation

`POST /v1/merchants/me/rotate-key` replaces the current API key with a new one.

This endpoint requires **Bearer JWT only**. An `X-Api-Key` is explicitly rejected:

```
401 JWT required for key rotation (Dashboard login)
```

This restriction is intentional: a compromised API key cannot be used to rotate itself.

**180-day cooldown**: rotation is limited to once every 180 days. Attempting to rotate earlier
returns a `429` error indicating how many days remain.

The old key is invalidated immediately when the rotation succeeds. Update all services that use
the old key before making this call.

```bash
curl -X POST https://api.butterpay.io/v1/merchants/me/rotate-key \
  -H "Authorization: Bearer <token>"
```

**Response**

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "message": "Store this key securely. It will not be shown again."
}
```

Store the new key exactly as you stored the original. The previous key no longer authenticates
from this point.

---

## sessionToken (payment page only)

The sessionToken is a short-lived JWT issued by the hosted payment page when a customer
connects their wallet. It binds a specific `(invoiceId, payerAddress)` pair, preventing a
third party from submitting a transaction for an invoice using a different payer address.

`POST /v1/invoices/:id/session` issues the token. The request is unauthenticated (public
endpoint) and rate-limited to 20 requests per minute per IP.

```bash
curl -X POST https://api.butterpay.io/v1/invoices/inv_01hwz4m8y3g9c5d7f8h0j2kn/session \
  -H "Content-Type: application/json" \
  -d '{ "payerAddress": "0xCustomerWalletAddress" }'
```

**Response**

```json
{
  "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

The token expires 30 minutes after issuance. It must be included in the body of
`POST /v1/invoices/:id/tx` to submit an on-chain transaction.

In practice this flow is handled entirely by the ButterPay hosted payment page
(`pay.butterpay.io/pay/<id>`). Most merchants do not need to implement it unless they are
building a custom payment UI.

---

## How `apiKeyAuth` accepts both credentials

Endpoints marked as requiring authentication use the `apiKeyAuth` middleware. Its resolution
order is:

1. If an `Authorization: Bearer <token>` header is present, the middleware validates it as a
   JWT and loads the corresponding merchant.
2. If the JWT is absent or fails validation, the middleware falls back to the `X-Api-Key`
   header and looks up the merchant by hashed key.
3. If neither resolves to an active merchant, the request is rejected with `401`.

The `POST /v1/merchants/me/rotate-key` endpoint is an exception: it bypasses `apiKeyAuth` and
uses a custom preHandler that requires a Bearer JWT and rejects any other form of
authentication. The per-endpoint documentation specifies which credentials are accepted.

---

## Production environment requirements

The backend performs fail-fast checks at startup when `NODE_ENV=production`. Missing either of
the following variables causes the process to exit before accepting requests:

| Variable | Purpose |
|----------|---------|
| `JWT_SECRET` | Signs all JWTs — login tokens, session tokens, and any other JWT issued by the API |
| `CORS_ORIGIN` | Comma-separated list of allowed origins (e.g. `https://app.butterpay.io,https://pay.butterpay.io`) |

In development, `JWT_SECRET` defaults to `dev-jwt-secret-change-in-production` and CORS
reflects all origins. These defaults must never be used in a production deployment.

---

## Security best practices

- Never embed an API key in client-side code (browser JavaScript, mobile apps, or public
  repositories). Treat it as a server-side secret.
- Rotate API keys every 6 to 12 months as a matter of routine, and immediately if a key is
  suspected to be compromised.
- Prefer short-lived JWTs in browser contexts. The 24-hour expiry is designed for dashboard
  sessions — do not persist JWTs in localStorage for long periods.
- Before sending a request, validate that the API key starts with the expected prefix
  (`bp_`). This catches accidentally truncated keys before they result in a `401` at
  the server.
