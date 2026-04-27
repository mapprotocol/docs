# Webhooks

ButterPay sends webhooks as HTTP POST requests to your registered endpoint whenever the state of
a payment or subscription changes. Each delivery includes a signed payload so you can verify it
originated from ButterPay before acting on it.

---

## Overview

Register a webhook URL in the dashboard (Settings → Webhook URL) or by calling
`PATCH /v1/merchants/me`. When a relevant event fires, the backend serializes a JSON payload,
signs it with your webhook secret, and POSTs it to that URL.

**Headers sent on every delivery**

| Header | Value |
|--------|-------|
| `Content-Type` | `application/json` |
| `X-ButterPay-Signature` | `t=<unix_timestamp>,v1=<hex_hmac>` |
| `X-ButterPay-Event` | Event name, e.g. `payment.confirmed` |

ButterPay considers a delivery successful when your endpoint returns any `2xx` status code within
10 seconds. Non-`2xx` responses and connection errors both trigger the retry schedule described
in [Retry policy](#retry-policy).

**Idempotency**: network conditions may cause the same event to be delivered more than once,
particularly when retries fire close together. Use the webhook log `id` field (available via
`GET /v1/webhooks/logs`) as a deduplication key in your handler. Processing the same `id` twice
should be a no-op.

---

## Event types

| Event | When it fires |
|-------|---------------|
| `payment.initiated` | Customer has submitted a transaction on-chain; confirmation is pending |
| `payment.confirmed` | Required on-chain confirmations reached and amount verified |
| `payment.failed` | On-chain transaction reverted, or confirmed amount fell below the invoice amount |
| `payment.expired` | Invoice TTL elapsed before a transaction was detected |
| `subscription.activated` | New subscription created; first charge collected on-chain |
| `subscription.charged` | Recurring charge succeeded for an existing subscription |
| `subscription.charge_failed` | Recurring charge attempt failed; subscription moves to `past_due` |
| `subscription.canceled` | Subscription canceled by the merchant or via API |

> `payment.test` is a synthetic event fired only by `POST /v1/webhooks/test`. It is never
> emitted as part of real payment processing.

---

## Payload shape

Every event shares the same top-level envelope. Fields that are not applicable to a given event
are omitted.

```json
{
  "event": "payment.confirmed",
  "invoiceId": "inv_01hwz4m8y3g9c5d7f8h0j2kn",
  "merchantOrderId": "order-9182",
  "amountUsd": "49.99",
  "tokenAmount": "49.99",
  "serviceFee": "0.40",
  "merchantReceived": "49.59",
  "paymentMethod": "crypto",
  "chain": "arbitrum",
  "token": "USDT",
  "txHash": "0xabc123...",
  "eventVerified": true,
  "timestamp": "2026-04-27T14:32:01.000Z"
}
```

**Envelope fields**

| Field | Type | Description |
|-------|------|-------------|
| `event` | string | Event name (see table above) |
| `invoiceId` | string | ButterPay invoice ID — present on all payment events |
| `merchantOrderId` | string | Your own order reference, if set when creating the invoice |
| `amountUsd` | string | Invoice amount in USD |
| `tokenAmount` | string | On-chain amount in the stablecoin's native units |
| `serviceFee` | string | ButterPay fee deducted (0.8%) |
| `merchantReceived` | string | Net amount received after fee |
| `paymentMethod` | string | Always `"crypto"` for on-chain payments |
| `chain` | string | Chain identifier, e.g. `"arbitrum"` |
| `token` | string | `"USDT"` or `"USDC"` |
| `txHash` | string | On-chain transaction hash |
| `eventVerified` | boolean | Whether the `PaymentProcessed` contract event was found and verified |
| `subscriptionId` | string | Present on all `subscription.*` events |
| `planId` | string | Subscription plan ID, if the subscription was created from a plan |
| `subscriberAddress` | string | Customer wallet address |
| `cyclesCharged` | number | Number of billing cycles completed so far |
| `cyclesTotal` | number | Total billing cycles for the subscription |
| `timestamp` | string | ISO 8601 timestamp of the event |

### Example: `payment.confirmed`

```json
{
  "event": "payment.confirmed",
  "invoiceId": "inv_01hwz4m8y3g9c5d7f8h0j2kn",
  "merchantOrderId": "order-9182",
  "amountUsd": "49.99",
  "tokenAmount": "49.99",
  "serviceFee": "0.40",
  "merchantReceived": "49.59",
  "paymentMethod": "crypto",
  "chain": "arbitrum",
  "token": "USDT",
  "txHash": "0xabc123def456...",
  "eventVerified": true,
  "timestamp": "2026-04-27T14:32:01.000Z"
}
```

### Example: `subscription.charged`

```json
{
  "event": "subscription.charged",
  "subscriptionId": "sub_01hwz9p4q5r6s7t8u9v0w1xy",
  "planId": "plan_01hwz2a3b4c5d6e7f8g9h0jk",
  "subscriberAddress": "0xCustomerWalletAddress",
  "amountUsd": "19.99",
  "chain": "arbitrum",
  "token": "USDT",
  "cyclesCharged": 3,
  "cyclesTotal": 12,
  "timestamp": "2026-04-27T14:32:01.000Z"
}
```

---

## Signature verification

Every delivery includes an `X-ButterPay-Signature` header in the format:

```
X-ButterPay-Signature: t=1745763121,v1=a3f2c1d9...
```

This follows the same convention as Stripe's webhook signatures. If you already have Stripe
webhook verification logic, the algorithm is identical.

**Algorithm**

1. Split the header value on `,` to extract `t` (Unix timestamp, seconds) and `v1` (hex HMAC).
2. Construct the signed content: `<t>.<raw_request_body>` — concatenate the timestamp string,
   a literal `.`, and the **raw, unmodified request body bytes**.
3. Compute `HMAC-SHA256(webhook_secret, signed_content)` and encode it as lowercase hex.
4. Compare your computed HMAC with the `v1` value using a constant-time comparison function.
5. Reject the request if `Math.abs(Date.now() / 1000 - t) > 300` (5-minute replay window).

Your webhook secret is available at `GET /v1/webhooks/secret` (requires `X-Api-Key`).

> **Critical**: compute the HMAC over the raw request body bytes, not a re-serialized JSON
> object. Any whitespace or key-ordering difference will produce a different HMAC and cause
> verification to fail.

### Node.js / Express

```js
const crypto = require("crypto");

app.post(
  "/webhooks/butterpay",
  express.raw({ type: "application/json" }), // preserve raw bytes
  (req, res) => {
    const webhookSecret = process.env.BUTTERPAY_WEBHOOK_SECRET;
    const sigHeader = req.headers["x-butterpay-signature"];

    if (!sigHeader) {
      return res.status(400).send("Missing signature header");
    }

    // Parse t=... and v1=...
    const parts = Object.fromEntries(
      sigHeader.split(",").map((p) => p.split("="))
    );
    const timestamp = parts["t"];
    const receivedHmac = parts["v1"];

    if (!timestamp || !receivedHmac) {
      return res.status(400).send("Malformed signature header");
    }

    // Replay-window check (5 minutes)
    const nowSecs = Math.floor(Date.now() / 1000);
    if (Math.abs(nowSecs - parseInt(timestamp, 10)) > 300) {
      return res.status(400).send("Timestamp outside replay window");
    }

    // Compute expected HMAC over "<timestamp>.<rawBody>"
    const signedContent = `${timestamp}.${req.body}`;
    const expectedHmac = crypto
      .createHmac("sha256", webhookSecret)
      .update(signedContent)
      .digest("hex");

    // Constant-time comparison
    const expected = Buffer.from(expectedHmac, "hex");
    const received = Buffer.from(receivedHmac, "hex");

    if (
      expected.length !== received.length ||
      !crypto.timingSafeEqual(expected, received)
    ) {
      return res.status(401).send("Signature mismatch");
    }

    const event = JSON.parse(req.body);
    // Handle event ...
    return res.sendStatus(200);
  }
);
```

### Python / Flask

```py
import hashlib
import hmac
import json
import os
import time

from flask import Flask, abort, request

app = Flask(__name__)

@app.route("/webhooks/butterpay", methods=["POST"])
def butterpay_webhook():
    webhook_secret = os.environ["BUTTERPAY_WEBHOOK_SECRET"].encode()
    sig_header = request.headers.get("X-ButterPay-Signature", "")

    if not sig_header:
        abort(400, "Missing signature header")

    # Parse t=... and v1=...
    parts = dict(p.split("=", 1) for p in sig_header.split(","))
    timestamp = parts.get("t")
    received_hmac = parts.get("v1")

    if not timestamp or not received_hmac:
        abort(400, "Malformed signature header")

    # Replay-window check (5 minutes)
    if abs(time.time() - int(timestamp)) > 300:
        abort(400, "Timestamp outside replay window")

    # Compute expected HMAC over "<timestamp>.<rawBody>"
    raw_body = request.get_data()  # raw bytes — do NOT call request.json first
    signed_content = f"{timestamp}.".encode() + raw_body
    expected_hmac = hmac.new(webhook_secret, signed_content, hashlib.sha256).hexdigest()

    # Constant-time comparison
    if not hmac.compare_digest(expected_hmac, received_hmac):
        abort(401, "Signature mismatch")

    event = json.loads(raw_body)
    # Handle event ...
    return "", 200
```

---

## Retry policy

If an initial delivery fails, the backend schedules up to three retries at the following
intervals after each failed attempt:

| Attempt | Delay after previous failure |
|---------|------------------------------|
| 1st retry | 10 seconds |
| 2nd retry | 60 seconds |
| 3rd retry | 300 seconds (5 minutes) |

After the third retry fails, the delivery is marked `failed` and no further attempts are made.
The `nextRetryAt` field in the webhook log is set to `null` once all retries are exhausted.

The URL is re-validated immediately before every retry attempt (including the original send).
If the URL no longer passes validation at retry time — for example because DNS now resolves to
a private address — the attempt is skipped without counting as a failure.

Retrieve delivery history and retry status with:

```bash
curl https://api.butterpay.io/v1/webhooks/logs \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

The response includes `attempts`, `statusCode`, `success`, `nextRetryAt`, and the raw
`response` body returned by your endpoint (truncated to 500 characters).

---

## URL requirements

ButterPay validates your webhook URL both at registration time and immediately before each
send (including retries). The dual-validation approach narrows the DNS rebind window that
would otherwise exist between a passing registration check and the actual HTTP call.

**Protocol**

- In production (`NODE_ENV=production`): HTTPS is required. HTTP URLs are rejected.
- In development: HTTP is permitted.

**Blocked IP ranges**

The backend resolves the hostname with a DNS lookup and rejects the URL if the resolved address
falls in any of the following ranges:

| Range | Classification |
|-------|---------------|
| `10.0.0.0/8` | RFC 1918 private |
| `172.16.0.0/12` | RFC 1918 private |
| `192.168.0.0/16` | RFC 1918 private |
| `127.0.0.0/8` | Loopback |
| `169.254.0.0/16` | Link-local / AWS IMDS |
| `100.64.0.0/10` | CGNAT (RFC 6598) |
| `224.0.0.0/4` | Multicast |
| `240.0.0.0/4` | Reserved |
| `0.0.0.0/8` | "This network" |
| `::1` | IPv6 loopback |
| `fc00::/7` | IPv6 ULA (covers `fc00::` and `fd00::`) |
| `fe80::/10` | IPv6 link-local |
| `ff00::/8` | IPv6 multicast |
| `::ffff:0:0/96` | IPv4-mapped IPv6 (e.g. `::ffff:127.0.0.1`) |

Any URL whose hostname resolves to an address in these ranges is rejected with a `400` error
at registration time. At send time, the delivery is silently skipped with a warning logged.

---

## Idempotency

The retry schedule means the same event payload may arrive at your endpoint more than once.
Design your handler to be idempotent.

The recommended deduplication key is the webhook log `id` returned by `GET /v1/webhooks/logs`.
Each initial delivery attempt creates one log record; retries update that same record rather
than creating new ones. Storing the log `id` and skipping processing when you see a repeated
value is sufficient for most use cases.

For payment events, the combination of `invoiceId` + `event` is also unique per invoice
lifecycle (an invoice can only be `confirmed` or `failed` once), and can serve as a simpler
deduplication strategy if you do not wish to query the logs endpoint.

---

## Testing webhooks

`POST /v1/webhooks/test` sends a synthetic `payment.test` payload to your currently configured
webhook URL. The request is authenticated with your API key and the payload is signed with your
webhook secret, so your verification logic runs exactly as it would for a real event.

```bash
curl -X POST https://api.butterpay.io/v1/webhooks/test \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

**Response**

```json
{
  "success": true,
  "statusCode": 200,
  "url": "https://your-server.example.com/webhooks/butterpay",
  "response": "OK"
}
```

If your URL is not configured or fails validation, the endpoint returns a `400` before any
delivery is attempted:

```json
{
  "error": "No webhook URL configured. Set one in Settings first."
}
```

The synthetic payload uses `invoiceId: "inv_test_000000000000"` and
`txHash: "0x000...000"`. It is safe to receive this in production as long as your handler
checks the `event` field before performing business logic.

---

## Troubleshooting

**Signature mismatch**

The most common cause is parsing the JSON body before computing the HMAC. Any JSON library may
produce different whitespace or key ordering than the original payload. Always pass the raw
request body bytes to the HMAC function. In Express, use `express.raw({ type: "application/json" })`
on your route instead of `express.json()`. In Flask, call `request.get_data()` before calling
`request.json`.

**401 Unauthorized on webhook logs or test endpoint**

Your API key may have been rotated or revoked. Retrieve a new key from the dashboard
(Settings → API Key → Rotate) and update your server's environment variables.

**URL rejected at registration**

Registration fails with `400` if:

- The URL uses HTTP in production (HTTPS is required).
- The hostname resolves to a private, loopback, link-local, or CGNAT address.
- The hostname cannot be resolved at all.

Use a public HTTPS endpoint. For local development, tools such as
[ngrok](https://ngrok.com) or [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
expose a local server under a public HTTPS URL.

**Retries exhausted — delivery marked failed**

Check `GET /v1/webhooks/logs` to see the `response` field from your server and the final
`statusCode`. Common causes:

- Your server returned a non-`2xx` status.
- Your server took longer than 10 seconds to respond (the per-request timeout).
- A TLS certificate error prevented the connection.

Fix the underlying issue, then handle any missed events by replaying the payloads from the
logs response or by querying the affected invoices via `GET /v1/invoices`.
