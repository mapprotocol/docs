# Merchants API

The Merchants API covers account registration, authentication, profile management, and API key
lifecycle for ButterPay merchants. Use these endpoints to sign up, obtain a JWT or API key, update
your account settings, and rotate credentials.

Authentication works in two layers. A **JWT** (obtained via `Login` or `SignUp`) is a short-lived
token (24 hours) used for security-sensitive operations such as key rotation and Dashboard access.
An **API key** (`X-Api-Key`) is a long-lived credential used in server-to-server calls like invoice
creation. See [Authentication](../authentication.md) for full details.

---

- [Login](#login)
- [GetCurrentMerchant](#getcurrentmerchant)
- [SignUp](#signup)
- [GetMe](#getme)
- [UpdateMe](#updateme)
- [GenerateApiKey](#generateapikey)
- [RotateApiKey](#rotateapikey)

---

## Login

Authenticate with email and password and receive a signed JWT plus the full merchant record,
including the current API key prefix.

- **Method**: `POST`
- **Path**: `/v1/auth/login`
- **Auth**: Public
- **Description**: Validates the supplied credentials against the stored bcrypt hash. On success
  returns a 24-hour JWT and the merchant object (including the stored API key prefix so the
  Dashboard can display it). Returns `401` for any invalid email/password combination — no
  distinction is made to avoid user-enumeration. Rate-limited to **10 requests per minute per IP**.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `email` | string | Yes | Merchant email address. Normalized to lowercase before lookup. |
| `password` | string | Yes | Account password. |

### Returns

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "merchant": {
    "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
    "name": "Acme Store",
    "email": "hello@acme.io",
    "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
    "serviceFeeBps": 80,
    "webhookUrl": "https://acme.io/webhooks/butter",
    "receivingAddresses": {
      "arbitrum": "0xMerchantWalletAddress",
      "ethereum": "0xMerchantWalletAddress"
    }
  }
}
```

`apiKey` in the response is the stored **prefix** of the key (e.g. `bp_abc1...`), not the
full plaintext key. The full key is only returned once, at generation or rotation time.

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "hello@acme.io",
    "password": "supersecret"
  }'
```

**Response** (HTTP 200):

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "merchant": {
    "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
    "name": "Acme Store",
    "email": "hello@acme.io",
    "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
    "serviceFeeBps": 80,
    "webhookUrl": "https://acme.io/webhooks/butter",
    "receivingAddresses": {
      "arbitrum": "0xMerchantWalletAddress"
    }
  }
}
```

---

## GetCurrentMerchant

Return the merchant associated with the current Bearer JWT. Primarily used by the Dashboard to
hydrate session state after a page reload.

- **Method**: `GET`
- **Path**: `/v1/auth/me`
- **Auth**: `Bearer JWT`
- **Description**: Verifies the JWT in the `Authorization` header, looks up the merchant, and
  returns the full profile including the API key prefix. Returns `401` if the token is missing,
  expired, or belongs to a deactivated merchant.

### Parameters

None. The merchant identity is derived from the JWT.

### Returns

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "serviceFeeBps": 80,
  "webhookUrl": "https://acme.io/webhooks/butter",
  "receivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress",
    "ethereum": "0xMerchantWalletAddress"
  }
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/auth/me \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Response** (HTTP 200):

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "serviceFeeBps": 80,
  "webhookUrl": "https://acme.io/webhooks/butter",
  "receivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  }
}
```

---

## SignUp

Register a new merchant account. On success the account is created and a JWT is issued immediately
so the caller lands directly on the Dashboard without a separate login step.

- **Method**: `POST`
- **Path**: `/v1/merchants`
- **Auth**: Public
- **Description**: Creates a merchant record with the supplied name, email, and password (minimum
  8 characters). The password is hashed with bcrypt (12 rounds) before storage — the plaintext is
  never persisted. An API key is **not** generated at this stage; call
  [GenerateApiKey](#generateapikey) after configuring at least one receiving address. Returns `409`
  if the email is already registered. Rate-limited to **5 requests per minute per IP**.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Display name for the merchant account, e.g. `"Acme Store"`. |
| `email` | string | Yes | Email address used for login. Stored in lowercase. |
| `password` | string | Yes | Account password. Minimum 8 characters. |
| `webhookUrl` | string | No | Default webhook URL for payment notifications. Must pass SSRF validation (HTTPS required in production, private IPs rejected). See [Webhooks](../webhooks.md). |
| `receivingAddresses` | object | No | Map of chain name to wallet address, e.g. `{"arbitrum": "0x..."}`. Can also be configured later via [UpdateMe](#updateme). |
| `serviceFeeBps` | number | No | Custom service fee in basis points. Defaults to `80` (0.80 %). Only configurable at account creation. |

### Returns

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

`token` is a 24-hour JWT. Use it immediately to call [GenerateApiKey](#generateapikey) or browse
the Dashboard. The API key is not included in this response because it has not yet been generated.

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/merchants \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Acme Store",
    "email": "hello@acme.io",
    "password": "supersecret",
    "webhookUrl": "https://acme.io/webhooks/butter",
    "receivingAddresses": {
      "arbitrum": "0xMerchantWalletAddress"
    }
  }'
```

**Response** (HTTP 201):

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

---

## GetMe

Return the authenticated merchant's full profile, including all receiving addresses and the current
webhook URL.

- **Method**: `GET`
- **Path**: `/v1/merchants/me`
- **Auth**: `X-Api-Key`
- **Description**: Looks up the merchant from the hashed API key and returns the stored profile.
  Returns `404` if the merchant record no longer exists (e.g. deactivated).

### Parameters

None. The merchant identity is derived from the `X-Api-Key` header.

### Returns

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "webhookUrl": "https://acme.io/webhooks/butter",
  "receivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress",
    "ethereum": "0xMerchantWalletAddress"
  },
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T12:00:00.000Z"
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/merchants/me \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "webhookUrl": "https://acme.io/webhooks/butter",
  "receivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  },
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T12:00:00.000Z"
}
```

---

## UpdateMe

Update mutable fields on the authenticated merchant's profile. All fields are optional; only
supplied fields are modified.

- **Method**: `PATCH`
- **Path**: `/v1/merchants/me`
- **Auth**: `X-Api-Key`
- **Description**: Applies a partial update to the merchant record. When `webhookUrl` is provided,
  the backend runs SSRF validation before persisting: private-range IPs are rejected and HTTPS is
  required in the production environment. See [Webhooks](../webhooks.md) for full URL rules. Returns
  the updated merchant record.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | No | New display name. |
| `email` | string | No | New email address. |
| `webhookUrl` | string | No | New default webhook URL. Must pass SSRF validation. See [Webhooks](../webhooks.md). |
| `receivingAddresses` | object | No | Updated map of chain name to wallet address. Replaces the existing map entirely. |

### Returns

The full updated merchant record (same shape as [GetMe](#getme)).

### Example

**Request:**

```bash
curl -X PATCH https://api.butterpay.io/v1/merchants/me \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -H "Content-Type: application/json" \
  -d '{
    "webhookUrl": "https://acme.io/webhooks/butter-v2",
    "receivingAddresses": {
      "arbitrum": "0xNewWalletAddress",
      "polygon": "0xNewWalletAddress"
    }
  }'
```

**Response** (HTTP 200):

```json
{
  "id": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Acme Store",
  "email": "hello@acme.io",
  "webhookUrl": "https://acme.io/webhooks/butter-v2",
  "receivingAddresses": {
    "arbitrum": "0xNewWalletAddress",
    "polygon": "0xNewWalletAddress"
  },
  "serviceFeeBps": 80,
  "createdAt": "2026-04-27T12:00:00.000Z"
}
```

---

## GenerateApiKey

Generate the live API key for the account. This is a one-time action — a key can only be generated
when no key currently exists. Subsequent rotations use [RotateApiKey](#rotateapikey).

- **Method**: `POST`
- **Path**: `/v1/merchants/me/generate-key`
- **Auth**: `apiKeyAuth (Bearer or X-Api-Key)` — for first-time generation a brand-new merchant has no API key, so use the `Authorization: Bearer <jwt>` returned by [SignUp](#signup) or [Login](#login)
- **Description**: Generates a new API key, stores only its bcrypt hash, and returns the plaintext
  key exactly once. The key is not stored in plaintext anywhere — if it is lost, rotation is the
  only recovery path. Requires that at least one chain entry is present in `receivingAddresses`
  before a key will be issued. Returns `400` if receiving addresses are not yet configured or if a
  key already exists (use [RotateApiKey](#rotateapikey) in that case).

### Parameters

None.

### Returns

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "message": "Store this key securely. It will not be shown again."
}
```

`apiKey` is the full plaintext key. Copy it immediately — only a short prefix is stored on the
server and shown in subsequent [GetMe](#getme) / [GetCurrentMerchant](#getcurrentmerchant)
responses.

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/merchants/me/generate-key \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "message": "Store this key securely. It will not be shown again."
}
```

---

## RotateApiKey

Replace the current API key with a new one. The old key is invalidated immediately upon rotation.

- **Method**: `POST`
- **Path**: `/v1/merchants/me/rotate-key`
- **Auth**: `Bearer JWT` (Dashboard login — API key is not accepted)
- **Description**: Generates a new API key, invalidates the previous one, and records the rotation
  timestamp. A **180-day cooldown** is enforced between rotations: attempting to rotate before the
  cooldown expires returns `429` with the number of days remaining. Because rotation invalidates the
  current key immediately, this endpoint intentionally requires a JWT (not an API key) to prevent a
  compromised key from being used to rotate itself.

### Parameters

None.

### Returns

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "message": "Store this key securely. It cannot be retrieved again."
}
```

`apiKey` is the full plaintext key. The previous key stops working as soon as this response is
returned. Store the new key before closing the session.

### Error responses

| Status | Error | Meaning |
|--------|-------|---------|
| 401 | `JWT required for key rotation (Dashboard login)` | `Authorization: Bearer` header missing. |
| 401 | `Invalid or expired token` | JWT is malformed or has expired. |
| 429 | `API Key can only be rotated once every 180 days. Try again in N days.` | Cooldown not yet elapsed. |

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/merchants/me/rotate-key \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Response** (HTTP 200):

```json
{
  "apiKey": "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",
  "message": "Store this key securely. It cannot be retrieved again."
}
```

---

## See also

- [Authentication](../authentication.md)
- [Webhooks](../webhooks.md)
- [Invoices API](invoices.md)
