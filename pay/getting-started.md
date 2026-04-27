# Getting Started

Run an end-to-end ButterPay integration in 10 minutes.

## Prerequisites

- Node.js 18 or later
- An EVM-compatible test wallet with a known address (MetaMask or any wallet that exposes an Arbitrum address)

---

## 1. Sign up

Create a merchant account. The response includes a JWT that you will use in the next two steps.

```bash
curl -X POST https://api.butterpay.io/v1/merchants \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Acme Store",
    "email": "you@example.com",
    "password": "s3cur3passw0rd"
  }'
```

**Response**

```json
{
  "id": "mer_01hwz3k9x2f8b4c6e7g9h0j1km",
  "name": "Acme Store",
  "email": "you@example.com",
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T10:00:00.000Z",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

Save the `token` value. It is your JWT and is valid for the current session.

> The signup endpoint rate-limits to 5 requests per minute per IP.

---

## 2. Configure receiving address

Before generating an API key you must set at least one receiving address — this is the wallet where
settlement funds land. The endpoint accepts `Authorization: Bearer <token>` from the JWT returned
in step 1, or an existing `X-Api-Key`.

```bash
curl -X PATCH https://api.butterpay.io/v1/merchants/me \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "receivingAddresses": {
      "arbitrum": "0xYourArbitrumWalletAddress"
    }
  }'
```

**Response** — the updated merchant object, including the `receivingAddresses` field.

You can include additional chains when they become live. For now only `arbitrum` is in production.

---

## 3. Generate API key

`POST /v1/merchants/me/generate-key` issues your secret API key. The plain-text value is returned
exactly once — the database stores only a hash. Copy it immediately.

```bash
curl -X POST https://api.butterpay.io/v1/merchants/me/generate-key \
  -H "Authorization: Bearer <token>"
```

**Response**

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

All subsequent server-to-server calls use `X-Api-Key: <apiKey>` instead of the JWT.

> To rotate a compromised key use `POST /v1/merchants/me/rotate-key` with a JWT (Dashboard login
> only). Rotation invalidates the previous key immediately.

---

## 4. Create your first invoice

Invoices are USD-denominated. The customer selects a chain and token at payment time, so you do
not need to specify either upfront.

```bash
curl -X POST https://api.butterpay.io/v1/invoices \
  -H "X-Api-Key: <apiKey>" \
  -H "Content-Type: application/json" \
  -d '{
    "amountUsd": "49.99",
    "merchantOrderId": "order_001",
    "description": "Pro plan — monthly",
    "webhookUrl": "https://your-server.example.com/webhooks/butterpay",
    "redirectUrl": "https://your-server.example.com/thank-you"
  }'
```

**Body fields**

| Field | Required | Description |
|---|---|---|
| `amountUsd` | Yes | Invoice amount as a decimal string, e.g. `"49.99"` |
| `merchantOrderId` | No | Your internal order ID; returned on the webhook |
| `description` | No | Shown on the hosted payment page |
| `webhookUrl` | No | Overrides the merchant-level default for this invoice |
| `redirectUrl` | No | Where to send the customer after successful payment |
| `metadata` | No | Arbitrary key/value object stored on the invoice |

**Response**

```json
{
  "id": "inv_01hwz4m8y3g9c5d7f8h0j2kn",
  "amount": "49.99",
  "description": "Pro plan — monthly",
  "status": "pending",
  "chain": null,
  "expiresAt": "2026-04-27T11:00:00.000Z",
  "createdAt": "2026-04-27T10:00:00.000Z",
  "redirectUrl": "https://your-server.example.com/thank-you",
  "merchantName": "Acme Store",
  "merchantOrderId": "order_001",
  "webhookUrl": "https://your-server.example.com/webhooks/butterpay"
}
```

The `id` field is the invoice identifier used to build the payment URL.

---

## 5. Share the payment link

Construct the hosted payment URL from the invoice `id`:

```
https://pay.butterpay.io/pay/<invoice.id>
```

Example:

```
https://pay.butterpay.io/pay/inv_01hwz4m8y3g9c5d7f8h0j2kn
```

Send this URL to your customer by email, redirect them to it, or embed it as a button. The payment
page is fully hosted — the customer connects a wallet, picks a supported chain and token (USDC or
USDT on Arbitrum), and signs the on-chain transaction. Settlement is direct from the customer's
wallet to your configured receiving address; ButterPay never holds funds.

---

## 6. Handle the webhook

When payment is confirmed on-chain, ButterPay POSTs a `payment.confirmed` event to the
`webhookUrl` you configured. The request includes two headers:

- `X-ButterPay-Signature` — `t=<unix_timestamp>,v1=<hmac_sha256_hex>`
- `X-ButterPay-Event` — event name, e.g. `payment.confirmed`

The HMAC is computed over `"<timestamp>.<rawBody>"` using your merchant webhook secret. Always
verify before acting on the payload.

Minimal Express handler:

```js
import express from "express";
import crypto from "crypto";

const app = express();

// Capture raw body — signature is over the raw bytes, not the parsed object
app.use("/webhooks/butterpay", express.raw({ type: "application/json" }));

app.post("/webhooks/butterpay", (req, res) => {
  const sigHeader = req.headers["x-butterpay-signature"];
  if (!sigHeader) return res.status(400).send("Missing signature");

  // Parse t= and v1= from the header
  const parts = Object.fromEntries(
    sigHeader.split(",").map((p) => p.split("="))
  );
  const timestamp = parts["t"];
  const receivedHmac = parts["v1"];

  if (!timestamp || !receivedHmac) {
    return res.status(400).send("Malformed signature header");
  }

  // Reject replays older than 5 minutes
  const age = Math.floor(Date.now() / 1000) - parseInt(timestamp, 10);
  if (age > 300) return res.status(400).send("Timestamp too old");

  // Recompute HMAC
  const secret = process.env.BUTTERPAY_WEBHOOK_SECRET;
  const signedContent = `${timestamp}.${req.body.toString("utf8")}`;
  const expected = crypto
    .createHmac("sha256", secret)
    .update(signedContent)
    .digest("hex");

  // Constant-time comparison to prevent timing attacks
  const expectedBuf = Buffer.from(expected, "hex");
  const receivedBuf = Buffer.from(receivedHmac, "hex");
  if (
    expectedBuf.length !== receivedBuf.length ||
    !crypto.timingSafeEqual(expectedBuf, receivedBuf)
  ) {
    return res.status(401).send("Invalid signature");
  }

  const payload = JSON.parse(req.body.toString("utf8"));
  const { event, invoiceId, merchantOrderId } = payload;

  console.log(`Verified webhook: event=${event} invoice=${invoiceId} order=${merchantOrderId}`);

  // Fulfill the order here
  res.sendStatus(200);
});

app.listen(3000);
```

---

## Complete copy-paste script

The script below performs steps 1–4 end-to-end: sign up, configure a receiving address, generate
an API key, and create an invoice. Copy it to `script.mjs` and run `node script.mjs`.

```js
// script.mjs — ButterPay end-to-end quickstart
// Usage: node script.mjs
// Requires Node.js 18+

const BASE = process.env.BUTTERPAY_API ?? "https://api.butterpay.io";

async function post(path, body, headers = {}) {
  const res = await fetch(`${BASE}${path}`, {
    method: "POST",
    headers: { "Content-Type": "application/json", ...headers },
    body: JSON.stringify(body),
  });
  if (!res.ok) {
    const err = await res.text();
    throw new Error(`POST ${path} → ${res.status}: ${err}`);
  }
  return res.json();
}

async function patch(path, body, headers = {}) {
  const res = await fetch(`${BASE}${path}`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json", ...headers },
    body: JSON.stringify(body),
  });
  if (!res.ok) {
    const err = await res.text();
    throw new Error(`PATCH ${path} → ${res.status}: ${err}`);
  }
  return res.json();
}

async function main() {
  // Step 1: Sign up
  console.log("1. Creating merchant account...");
  const signup = await post("/v1/merchants", {
    name: "Acme Store",
    email: `acme+${Date.now()}@example.com`,
    password: "s3cur3passw0rd",
  });
  const { token, id: merchantId } = signup;
  console.log(`   Merchant ID: ${merchantId}`);

  const bearerHeaders = { Authorization: `Bearer ${token}` };

  // Step 2: Configure receiving address
  console.log("2. Configuring receiving address...");
  await patch(
    "/v1/merchants/me",
    {
      receivingAddresses: {
        arbitrum: "0x1234567890abcdef1234567890abcdef12345678",
      },
    },
    bearerHeaders
  );
  console.log("   Receiving address set.");

  // Step 3: Generate API key
  console.log("3. Generating API key...");
  const keyResult = await post("/v1/merchants/me/generate-key", {}, bearerHeaders);
  const apiKey = keyResult.apiKey;
  console.log(`   API key: ${apiKey}`);
  console.log("   Store this securely — it will not be shown again.");

  const apiKeyHeaders = { "X-Api-Key": apiKey };

  // Step 4: Create an invoice
  console.log("4. Creating invoice...");
  const invoice = await post(
    "/v1/invoices",
    {
      amountUsd: "49.99",
      merchantOrderId: "order_001",
      description: "Pro plan — monthly",
      webhookUrl: "https://your-server.example.com/webhooks/butterpay",
      redirectUrl: "https://your-server.example.com/thank-you",
    },
    apiKeyHeaders
  );
  console.log(`   Invoice ID: ${invoice.id}`);
  console.log(`   Payment URL: https://pay.butterpay.io/pay/${invoice.id}`);
}

main().catch((err) => {
  console.error(err.message);
  process.exit(1);
});
```

---

## Next steps

- [Authentication](authentication.md) — full auth modes: JWT vs API key, key rotation, session tokens
- [Webhooks](webhooks.md) — full event reference, retry policy, and webhook logs
- [API Reference](api/invoices.md) — complete invoice endpoint reference
