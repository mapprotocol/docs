# Plans & Subscriptions API

ButterPay subscriptions use a one-time on-chain `approve(amount × cycles)` (or EIP-2612 permit where
supported) followed by a `subscribe()` transaction. The contract auto-charges the merchant per
period.

A merchant defines a **plan** that specifies the charge amount, interval, and maximum number of
billing cycles. A subscriber visits the hosted subscription page, approves the total spend, and
signs the `subscribe()` transaction. The backend records the resulting subscription and a scheduler
executes recurring charges automatically. Merchants receive webhook events at each stage. See
[Webhooks](../webhooks.md) for payload shapes.

All authenticated endpoints require an `X-Api-Key` header. See
[Authentication](../authentication.md) for details.

---

- [CreatePlan](#createplan)
- [ListPlans](#listplans)
- [GetPlan](#getplan)
- [UpdatePlan](#updateplan)
- [DeletePlan](#deleteplan)
- [RegisterSubscription](#registersubscription)
- [CreateSubscription](#createsubscription)
- [ListSubscriptions](#listsubscriptions)
- [GetSubscription](#getsubscription)
- [CancelSubscription](#cancelsubscription)

---

## CreatePlan

Create a new subscription plan. The plan defines how much is charged, how often, and for how many
cycles. Once created, the plan is immediately usable on the hosted subscription page at
`pay.butterpay.io/subscribe/<id>`.

- **Method**: `POST`
- **Path**: `/v1/subscription-plans`
- **Auth**: `X-Api-Key`
- **Description**: Creates a subscription plan scoped to the authenticated merchant. Returns the
  full plan record. The plan is active by default and can be linked to from the hosted subscription
  page immediately. Use [UpdatePlan](#updateplan) to toggle `active` off and stop new signups
  without affecting existing subscribers.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Human-readable plan name shown on the subscription page, e.g. `"Pro Monthly"`. |
| `amountUsd` | string | Yes | Decimal USD amount charged each interval, e.g. `"9.99"`. Must be positive. |
| `intervalSeconds` | number | Yes | Billing interval in seconds. Must be `>= 86400` (1 day). Common values: `2592000` (30 days), `7776000` (90 days), `31536000` (365 days). |
| `cycles` | number | Yes | Total number of billing cycles before the subscription completes. Range: 1–120. |
| `description` | string | No | Optional description shown to the subscriber. |
| `chain` | string | No | Blockchain to use for charges. Defaults to `"arbitrum"`. |
| `token` | string | No | Token symbol for charges. Defaults to `"USDT"`. |

### Returns

```json
{
  "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Pro Monthly",
  "description": "Full access to all features",
  "amountUsd": "9.99",
  "interval": 2592000,
  "cycles": 12,
  "chain": "arbitrum",
  "token": "USDT",
  "active": true,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/subscription-plans \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Pro Monthly",
    "description": "Full access to all features",
    "amountUsd": "9.99",
    "intervalSeconds": 2592000,
    "cycles": 12,
    "chain": "arbitrum",
    "token": "USDT"
  }'
```

**Response** (HTTP 201):

```json
{
  "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Pro Monthly",
  "description": "Full access to all features",
  "amountUsd": "9.99",
  "interval": 2592000,
  "cycles": 12,
  "chain": "arbitrum",
  "token": "USDT",
  "active": true,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

---

## ListPlans

Return all subscription plans belonging to the authenticated merchant, ordered by creation date
descending.

- **Method**: `GET`
- **Path**: `/v1/subscription-plans`
- **Auth**: `X-Api-Key`
- **Description**: Returns every plan registered to the merchant, including inactive ones. Use the
  `active` field to distinguish plans that accept new subscribers from those that have been
  deactivated. Inactive plans continue to serve existing subscriptions until they complete.

### Parameters

None.

### Returns

```json
{
  "data": [
    {
      "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
      "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
      "name": "Pro Monthly",
      "description": "Full access to all features",
      "amountUsd": "9.99",
      "interval": 2592000,
      "cycles": 12,
      "chain": "arbitrum",
      "token": "USDT",
      "active": true,
      "createdAt": "2026-04-27T12:00:00.000Z",
      "updatedAt": "2026-04-27T12:00:00.000Z"
    }
  ],
  "count": 1
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/subscription-plans \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "data": [...],
  "count": 3
}
```

---

## GetPlan

Retrieve a single plan by ID. This endpoint is **public** — no API key is required. It is used by
the hosted subscription page to display plan details and the merchant's receiving address to the
subscriber before they approve and sign.

- **Method**: `GET`
- **Path**: `/v1/subscription-plans/:id`
- **Auth**: Public
- **Description**: Returns the plan record plus the merchant's name and receiving addresses for the
  plan's chain. Returns `404` if the plan does not exist or has been deactivated (`active: false`).
  The `merchantReceivingAddresses` field is included so the hosted page can construct the on-chain
  `subscribe()` call without a separate authenticated request.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Plan ID. |

### Returns

```json
{
  "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Pro Monthly",
  "description": "Full access to all features",
  "amountUsd": "9.99",
  "interval": 2592000,
  "cycles": 12,
  "chain": "arbitrum",
  "token": "USDT",
  "active": true,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z",
  "merchantName": "Acme Store",
  "merchantReceivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  }
}
```

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/subscription-plans/plan_01hwzq3k5n8ej4v2b7r9abc123
```

**Response** (HTTP 200):

```json
{
  "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Pro Monthly",
  "description": "Full access to all features",
  "amountUsd": "9.99",
  "interval": 2592000,
  "cycles": 12,
  "chain": "arbitrum",
  "token": "USDT",
  "active": true,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z",
  "merchantName": "Acme Store",
  "merchantReceivingAddresses": {
    "arbitrum": "0xMerchantWalletAddress"
  }
}
```

---

## UpdatePlan

Update mutable fields on an existing plan. Only the authenticated merchant who owns the plan may
update it.

- **Method**: `PATCH`
- **Path**: `/v1/subscription-plans/:id`
- **Auth**: `X-Api-Key`
- **Description**: Applies a partial update to the plan. Accepted fields are `name`, `description`,
  and `active`. Setting `active: false` stops the hosted subscription page from accepting new
  signups while keeping all existing subscriptions running to completion. This is the recommended
  way to retire a plan. Returns `404` if the plan does not exist or belongs to a different merchant.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Plan ID. |
| `name` | string | No | New display name. |
| `description` | string | No | New description. |
| `active` | boolean | No | Set to `false` to stop accepting new subscribers. Existing subscribers are unaffected. |

### Returns

The full updated plan record (same shape as [GetPlan](#getplan) minus the merchant fields).

### Example

**Request:**

```bash
curl -X PATCH https://api.butterpay.io/v1/subscription-plans/plan_01hwzq3k5n8ej4v2b7r9abc123 \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -H "Content-Type: application/json" \
  -d '{
    "active": false
  }'
```

**Response** (HTTP 200):

```json
{
  "id": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "name": "Pro Monthly",
  "description": "Full access to all features",
  "amountUsd": "9.99",
  "interval": 2592000,
  "cycles": 12,
  "chain": "arbitrum",
  "token": "USDT",
  "active": false,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T14:00:00.000Z"
}
```

---

## DeletePlan

Permanently delete a plan. Deletion is blocked when the plan has active or past-due subscribers.

- **Method**: `DELETE`
- **Path**: `/v1/subscription-plans/:id`
- **Auth**: `X-Api-Key`
- **Description**: Hard-deletes the plan record. Returns `409` if any subscriptions on this plan
  are in `active` or `past_due` status. In that case, use [UpdatePlan](#updateplan) with
  `active: false` to stop new signups while letting existing subscriptions run out. Returns `404`
  if the plan does not exist or belongs to a different merchant.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Plan ID. |

### Returns

```json
{
  "deleted": true
}
```

### Error responses

| Status | Error | Meaning |
|--------|-------|---------|
| 404 | `not found` | Plan does not exist or belongs to a different merchant. |
| 409 | `Cannot delete: N active subscriber(s) still tied to this plan.` | Live subscribers exist. Deactivate the plan instead. |

### Example

**Request:**

```bash
curl -X DELETE https://api.butterpay.io/v1/subscription-plans/plan_01hwzq3k5n8ej4v2b7r9abc123 \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "deleted": true
}
```

---

## RegisterSubscription

Register a subscription that was just created on-chain. This is a **public** endpoint called by the
hosted subscription page immediately after the subscriber signs the on-chain `subscribe()`
transaction. It records the subscription in ButterPay and fires the `subscription.activated`
webhook.

- **Method**: `POST`
- **Path**: `/v1/plans/:planId/subscribe`
- **Auth**: Public
- **Description**: Called after the subscriber has already approved `amountUsd × cycles` of the
  plan's token to `SubscriptionManager.sol` and signed the `subscribe()` transaction on-chain. The
  request must include the resulting on-chain subscription ID (`onChainId`) so the backend
  scheduler can reference it for future charges. Returns `404` if the plan is inactive or not
  found. On success the backend records the subscription, sets `cyclesCharged: 1` (the first charge
  occurred in the `subscribe()` call), schedules the next charge at `now + intervalSeconds`, and
  fires the `subscription.activated` webhook to the merchant.

**Subscriber flow:**

1. Fetch plan details via [GetPlan](#getplan) (public).
2. Call `token.approve(subscriptionManagerAddress, amountUsd × cycles)` on-chain (one-time; the
   allowance is consumed as charges occur).
3. Call `subscriptionManager.subscribe(planOnChainId, ...)` — this executes the first charge
   immediately and returns an `onChainId`.
4. POST to this endpoint with the `onChainId` and `txHash`.

Webhook events delivered after registration: `subscription.activated` (first charge),
`subscription.charged` (each subsequent period), `subscription.charge_failed` (failed debit),
`subscription.canceled` (on cancellation). See [Webhooks](../webhooks.md).

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `planId` | string | Yes | ButterPay plan ID. Must match the `:planId` path segment. |
| `subscriberAddress` | string | Yes | Subscriber's wallet address. |
| `onChainId` | number | Yes | Subscription ID returned by the `subscribe()` contract call. Used by the scheduler for future charges. |
| `txHash` | string | No | Transaction hash of the `subscribe()` call, for on-chain reference. |

### Returns

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 1,
  "onChainId": 42,
  "status": "active",
  "nextChargeAt": "2026-05-27T12:00:00.000Z",
  "lastChargedAt": "2026-04-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/plans/plan_01hwzq3k5n8ej4v2b7r9abc123/subscribe \
  -H "Content-Type: application/json" \
  -d '{
    "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
    "subscriberAddress": "0xSubscriberWalletAddress",
    "onChainId": 42,
    "txHash": "0xabc123..."
  }'
```

**Response** (HTTP 201):

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 1,
  "onChainId": 42,
  "status": "active",
  "nextChargeAt": "2026-05-27T12:00:00.000Z",
  "lastChargedAt": "2026-04-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

---

## CreateSubscription

Create a subscription record directly via the merchant API, without going through the on-chain
`subscribe()` flow. Intended for merchants who manage their own subscription pages or need to
import existing subscribers.

- **Method**: `POST`
- **Path**: `/v1/subscriptions`
- **Auth**: `X-Api-Key`
- **Description**: Creates a subscription record scoped to the authenticated merchant. Unlike
  [RegisterSubscription](#registersubscription), this endpoint does not require a `planId` —
  billing parameters are specified explicitly in the request body. `onChainId` and `expiresAt` are
  optional; if `onChainId` is omitted the scheduler will not attempt on-chain charges until it is
  set. The `subscription.activated` webhook is fired on creation. Returns `400` if any required
  field is missing.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `subscriberAddress` | string | Yes | Subscriber's wallet address. |
| `chain` | string | Yes | Blockchain identifier, e.g. `"arbitrum"`. |
| `token` | string | Yes | Token symbol, e.g. `"USDT"`. |
| `amount` | string | Yes | Decimal USD amount per cycle, e.g. `"9.99"`. |
| `intervalSeconds` | number | Yes | Billing interval in seconds. |
| `cyclesTotal` | number | Yes | Total number of billing cycles. |
| `onChainId` | number | No | On-chain subscription ID. Required for the scheduler to execute charges. |
| `expiresAt` | string | No | ISO 8601 datetime after which the subscription is considered complete. |

### Returns

The full subscription record (same shape as [RegisterSubscription](#registersubscription)).

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/subscriptions \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..." \
  -H "Content-Type: application/json" \
  -d '{
    "subscriberAddress": "0xSubscriberWalletAddress",
    "chain": "arbitrum",
    "token": "USDT",
    "amount": "9.99",
    "intervalSeconds": 2592000,
    "cyclesTotal": 12,
    "onChainId": 42,
    "expiresAt": "2027-04-27T12:00:00.000Z"
  }'
```

**Response** (HTTP 201):

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc789",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": null,
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 1,
  "onChainId": 42,
  "status": "active",
  "nextChargeAt": "2026-05-27T12:00:00.000Z",
  "lastChargedAt": "2026-04-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-04-27T12:00:00.000Z"
}
```

---

## ListSubscriptions

Return all subscriptions belonging to the authenticated merchant, ordered by creation date
descending. Optionally filter by status.

- **Method**: `GET`
- **Path**: `/v1/subscriptions`
- **Auth**: `X-Api-Key`
- **Description**: Returns up to 100 subscriptions for the merchant. Use the `status` query
  parameter to narrow results. Possible status values: `active`, `past_due`, `completed`,
  `cancelled`.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string (query) | No | Filter by subscription status. One of: `active`, `past_due`, `completed`, `cancelled`. |

### Returns

```json
{
  "data": [
    {
      "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
      "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
      "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
      "subscriberAddress": "0xSubscriberWalletAddress",
      "chain": "arbitrum",
      "token": "USDT",
      "amount": "9.99",
      "interval": 2592000,
      "cyclesTotal": 12,
      "cyclesCharged": 3,
      "onChainId": 42,
      "status": "active",
      "nextChargeAt": "2026-07-27T12:00:00.000Z",
      "lastChargedAt": "2026-06-27T12:00:00.000Z",
      "expiresAt": "2027-04-27T12:00:00.000Z",
      "cancelledAt": null,
      "createdAt": "2026-04-27T12:00:00.000Z",
      "updatedAt": "2026-06-27T12:00:00.000Z"
    }
  ],
  "count": 1
}
```

### Example

**Request:**

```bash
curl "https://api.butterpay.io/v1/subscriptions?status=active" \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "data": [...],
  "count": 5
}
```

---

## GetSubscription

Retrieve a single subscription by ID.

- **Method**: `GET`
- **Path**: `/v1/subscriptions/:id`
- **Auth**: `X-Api-Key`
- **Description**: Returns the full subscription record. Returns `404` if the subscription does not
  exist or belongs to a different merchant.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Subscription ID. |

### Returns

The full subscription record (same shape as [RegisterSubscription](#registersubscription)).

### Example

**Request:**

```bash
curl https://api.butterpay.io/v1/subscriptions/sub_01hwzq3k5n8ej4v2b7r9abc456 \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 3,
  "onChainId": 42,
  "status": "active",
  "nextChargeAt": "2026-07-27T12:00:00.000Z",
  "lastChargedAt": "2026-06-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": null,
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-06-27T12:00:00.000Z"
}
```

---

## CancelSubscription

Cancel an active subscription. The subscription's status is set to `cancelled` and the
`subscription.canceled` webhook is fired. No further charges are attempted by the scheduler.

- **Method**: `POST`
- **Path**: `/v1/subscriptions/:id/cancel`
- **Auth**: `X-Api-Key`
- **Description**: Cancels the subscription and records `cancelledAt`. Returns the updated
  subscription record. Returns `404` if the subscription does not exist or belongs to a different
  merchant. Note: cancelling via this endpoint updates the ButterPay record only. If the
  subscriber's on-chain allowance remains, the on-chain subscription should also be cancelled via
  `SubscriptionManager.sol` to prevent any future charges from the contract directly.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (path) | Yes | Subscription ID. |

### Returns

The updated subscription record with `status: "cancelled"` and `cancelledAt` set.

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 3,
  "onChainId": 42,
  "status": "cancelled",
  "nextChargeAt": null,
  "lastChargedAt": "2026-06-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": "2026-07-01T09:00:00.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-07-01T09:00:00.000Z"
}
```

### Example

**Request:**

```bash
curl -X POST https://api.butterpay.io/v1/subscriptions/sub_01hwzq3k5n8ej4v2b7r9abc456/cancel \
  -H "X-Api-Key: bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx..."
```

**Response** (HTTP 200):

```json
{
  "id": "sub_01hwzq3k5n8ej4v2b7r9abc456",
  "merchantId": "mer_01hwzq3k5n8ej4v2b7r9abc123",
  "planId": "plan_01hwzq3k5n8ej4v2b7r9abc123",
  "subscriberAddress": "0xSubscriberWalletAddress",
  "chain": "arbitrum",
  "token": "USDT",
  "amount": "9.99",
  "interval": 2592000,
  "cyclesTotal": 12,
  "cyclesCharged": 3,
  "onChainId": 42,
  "status": "cancelled",
  "nextChargeAt": null,
  "lastChargedAt": "2026-06-27T12:00:00.000Z",
  "expiresAt": "2027-04-27T12:00:00.000Z",
  "cancelledAt": "2026-07-01T09:00:00.000Z",
  "createdAt": "2026-04-27T12:00:00.000Z",
  "updatedAt": "2026-07-01T09:00:00.000Z"
}
```

---

## See also

- [Authentication](../authentication.md)
- [Webhooks](../webhooks.md)
- [Invoices API](invoices.md)
