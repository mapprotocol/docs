# @butterpay/core

Low-level TypeScript SDK: wallet adapters, payment providers, API client. Use this directly for
full control, or build on top of `@butterpay/react` (planned).

The package exposes three layers you can use independently or together:

- **Wallet layer** — `ExternalWalletAdapter` (browser/MetaMask) and `HDWalletAdapter`
  (server/TG bot, Phase 2).
- **Payment layer** — `CryptoPaymentProvider` for one-time stablecoin payments,
  `SubscriptionProvider` for on-chain recurring charges.
- **API layer** — `ApiClient`, a fetch-based HTTP client for the ButterPay backend.

The `ButterPay` class wires all three together so most integrations need only a single import.

---

## Installation

`@butterpay/core` is not yet published to npm. Reference it from the SDK monorepo directly:

```jsonc
// package.json
{
  "dependencies": {
    "@butterpay/core": "file:../butter-pay-sdk/core"
  }
}
```

Peer dependency: `viem ^2.47.10`.

---

## Quickstart

```ts
import { ButterPay, ExternalWalletAdapter } from "@butterpay/core";

const sdk = new ButterPay({
  apiUrl: "https://api.butterpay.io",
  wallet: new ExternalWalletAdapter(window.ethereum),
});

await sdk.connect();
const balances = await sdk.scanBalances();

const { invoice, txHash } = await sdk.pay({
  amount: "49.99",
  token: "USDC",
  chain: "arbitrum",
  merchantAddress: "0xYourReceivingAddress",
  paymentRouterAddress: "0x4b32bcd3eC4F0a14D7061e0d239eBAd84F77743f",
  serviceFeeBps: 80,
  description: "Order #1234",
  merchantOrderId: "order-001",
  waitForConfirmation: true,
});

console.log(invoice.status); // "confirmed"
console.log(txHash);         // "0xabc..."
```

`sdk.pay()` internally runs the full five-step flow: create invoice → get session token → approve
token → call `PaymentRouter.pay()` → submit txHash to backend for tracking.

---

## Configuration

Pass a `ButterPayConfig` object to the `ButterPay` constructor.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiUrl` | `string` | Yes | ButterPay API base URL, e.g. `"https://api.butterpay.io"`. |
| `apiKey` | `string` | No | Merchant API key (`bp_...`). Required for merchant-only endpoints (create plan, list subscriptions, etc.). Omit for payer-side usage. |
| `wallet` | `WalletAdapter` | Yes | Wallet adapter instance. Pass `ExternalWalletAdapter` for browser usage or `HDWalletAdapter` for server/TG bot (Phase 2). |
| `chains` | `Partial<Record<ChainName, Partial<ChainConfig>>>` | No | Per-chain overrides for RPC URLs, contract addresses, or token lists. Merged on top of `defaultChainConfigs`. See [Custom chain configuration](#custom-chain-configuration). |

---

## Wallet adapters

| Adapter | Use case | Status |
|---------|----------|--------|
| `ExternalWalletAdapter` | Browser wallet (MetaMask, OKX, Rabby, any EIP-1193) | Stable |
| `HDWalletAdapter` | Server-side or Telegram Mini App self-custody | Phase 2 |

### ExternalWalletAdapter

Wraps any EIP-1193 provider. Pass `window.ethereum` or any compatible injected provider.

```ts
import { ExternalWalletAdapter } from "@butterpay/core";

const wallet = new ExternalWalletAdapter(window.ethereum);

// Use with ButterPay
const sdk = new ButterPay({
  apiUrl: "https://api.butterpay.io",
  wallet,
});

// Or use standalone
const address = await wallet.connect();
console.log(address); // "0xAbCd..."
```

The adapter calls `eth_requestAccounts` on `connect()` and `eth_sendTransaction` for on-chain
interactions. It does not implement `signTypedData` — EIP-712 is handled at the provider level by
`CryptoPaymentProvider` when constructing permit signatures.

### HDWalletAdapter (Phase 2)

For environments without an injected wallet — such as Node.js backends and Telegram Mini App
WebViews — `HDWalletAdapter` creates or restores a BIP39/BIP44 HD wallet in-process.

Three construction paths are provided: `HDWalletAdapter.create()` (fresh mnemonic),
`HDWalletAdapter.fromMnemonic(mnemonic)` (import), and `HDWalletAdapter.fromPrivateKey(key)`.
Keystores can be encrypted and decrypted with `encryptToKeystore` / `HDWalletAdapter.fromKeystore`
using PBKDF2 + AES-256-GCM.

Full coverage of `HDWalletAdapter` is deferred to a Phase 2 update. See the
[Types reference — HDWalletConfig / Keystore](#hdwalletconfig) below for the relevant type shapes.

---

## API methods

All public methods on the `ButterPay` class are listed below, grouped by domain. Every method is
async unless noted.

### Wallet

| Method | Signature | Returns | Description |
|--------|-----------|---------|-------------|
| `connect` | `connect()` | `Promise<Address>` | Trigger wallet connection (calls `eth_requestAccounts` on `ExternalWalletAdapter`). Returns the active EVM address. Throws if the user rejects or no accounts are returned. |
| `getAddress` | `getAddress()` | `Address \| null` | Return the currently connected address without triggering any wallet prompt. Returns `null` if the wallet has not been connected yet. (Synchronous.) |
| `scanBalances` | `scanBalances()` | `Promise<BalanceInfo[]>` | Scan USDT and USDC balances for the connected address across all configured chains in parallel. Throws if the wallet is not connected. |

### Payments

| Method | Signature | Returns | Description |
|--------|-----------|---------|-------------|
| `pay` | `pay(params)` | `Promise<{ invoice: Invoice; txHash: Hash }>` | Full one-time payment flow. Creates an invoice, obtains a session token, approves the token if needed, calls `PaymentRouter.pay()`, submits the txHash, and optionally polls for on-chain confirmation. See parameter table below. |
| `getInvoice` | `getInvoice(invoiceId: string)` | `Promise<Invoice>` | Fetch an invoice by ID. Delegates to `ApiClient.getInvoice`. |
| `waitForConfirmation` | `waitForConfirmation(invoiceId: string)` | `Promise<Invoice>` | Poll the API every 5 seconds (up to 5 minutes) until the invoice reaches a terminal status (`"confirmed"` or `"failed"`). Throws on timeout. |

#### `pay()` parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | `string` | Yes | Human-readable USD amount, e.g. `"49.99"`. |
| `token` | `string` | Yes | Token symbol: `"USDT"` or `"USDC"`. |
| `chain` | `ChainName` | Yes | Target chain: `"arbitrum"`, `"ethereum"`, `"bsc"`, `"polygon"`, or `"arbitrumSepolia"`. |
| `merchantAddress` | `Address` | Yes | Merchant receiving wallet address (EVM `0x...`). |
| `paymentRouterAddress` | `Address` | Yes | Deployed `PaymentRouter` contract address on the target chain. |
| `serviceFeeBps` | `number` | Yes | Service fee in basis points, e.g. `80` for 0.8%. |
| `referrer` | `Address` | No | Referrer address for referral fee split. |
| `referrerFeeBps` | `number` | No | Referrer fee in basis points. |
| `description` | `string` | No | Human-readable description stored on the invoice. |
| `merchantOrderId` | `string` | No | Your internal order ID. Returned in webhooks for reconciliation. |
| `metadata` | `Record<string, unknown>` | No | Arbitrary key-value pairs stored on the invoice. |
| `waitForConfirmation` | `boolean` | No | If `true`, polls the API until the invoice is confirmed or failed before resolving. Defaults to `false`. |

### Subscription plans (merchant)

These methods require `apiKey` in the `ButterPayConfig`.

| Method | Signature | Returns | Description |
|--------|-----------|---------|-------------|
| `createPlan` | `createPlan(params)` | `Promise<Plan>` | Create a new subscription plan. `params` accepts `name`, `amountUsd`, `intervalSeconds`, `cycles`, `chain?`, `token?`, `description?`. Requires `apiKey`. |
| `listPlans` | `listPlans()` | `Promise<Plan[]>` | List all active plans for the authenticated merchant. |
| `getPlan` | `getPlan(planId: string)` | `Promise<Plan>` | Fetch a plan by ID. Public endpoint — no `apiKey` required. Used by the `/subscribe/[planId]` page to display plan details. |
| `updatePlan` | `updatePlan(planId: string, updates)` | `Promise<Plan>` | Partially update a plan. Accepted fields: `name`, `description`, `active`. To stop new subscribers without deleting, set `active: false`. |
| `deletePlan` | `deletePlan(planId: string)` | `Promise<{ deleted: boolean }>` | Permanently delete a plan. Rejected with an error if the plan has live subscribers — use `updatePlan({ active: false })` instead. |

### Subscriptions (user flow)

| Method | Signature | Returns | Description |
|--------|-----------|---------|-------------|
| `subscribe` | `subscribe(params)` | `Promise<SubscribeResult>` | Full subscribe flow: fetch plan (unless pre-supplied) → approve `amount × cycles` on-chain → call `SubscriptionManager.subscribe()` → register with backend. Returns txHashes, on-chain subscription ID, and the persisted `Subscription` record. Wallet must be connected. |
| `listSubscriptions` | `listSubscriptions(status?: string)` | `Promise<Subscription[]>` | List subscriptions for the authenticated merchant. Pass a `status` string to filter (`"active"`, `"cancelled"`, etc.). Requires `apiKey`. |
| `getSubscription` | `getSubscription(id: string)` | `Promise<Subscription>` | Fetch a single subscription by ID. |
| `cancelSubscription` | `cancelSubscription(id, opts?)` | `Promise<{ subscription: Subscription; cancelTxHash?: Hash }>` | Cancel a subscription. By default performs both an on-chain `SubscriptionManager.cancel()` call and a backend status update. Pass `{ apiOnly: true }` to only update the backend record (e.g., from the merchant Dashboard where no wallet is connected). |

#### `subscribe()` parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `planId` | `string` | Yes | ID of the plan to subscribe to. |
| `plan` | `Plan` | No | Pre-fetched plan object. If omitted, the SDK fetches it automatically. |
| `subscriptionManagerAddress` | `Address` | No | Override the `SubscriptionManager` contract address. Defaults to the address in `ChainConfig` for the plan's chain. |
| `expiry` | `number` | No | Unix timestamp after which the subscription expires. `0` means no expiry. Defaults to `now + intervalSeconds × cycles`. |

---

## Types reference

Types are exported from `@butterpay/core` and originate in `src/types.ts`.

### Chains

#### ChainName

```ts
type ChainName = "ethereum" | "arbitrum" | "bsc" | "polygon" | "arbitrumSepolia";
```

#### ChainConfig

```ts
interface ChainConfig {
  name: ChainName;
  viemChain: ViemChain;           // from viem — chain definition object
  rpcUrl: string;                 // public RPC endpoint
  paymentRouterAddress: Address;  // deployed PaymentRouter contract
  subscriptionManagerAddress?: Address; // deployed SubscriptionManager (zero if not deployed)
  tokens: TokenConfig[];          // supported stablecoins on this chain
  blockExplorerUrl: string;       // e.g. "https://arbiscan.io"
}
```

#### TokenConfig

```ts
interface TokenConfig {
  symbol: string;   // e.g. "USDT"
  address: Address; // ERC20 contract address
  decimals: number; // e.g. 6
}
```

### Wallet

#### WalletAdapter

The interface implemented by both `ExternalWalletAdapter` and `HDWalletAdapter`.

```ts
interface WalletAdapter {
  /** Connect/unlock the wallet, return the active address */
  connect(): Promise<Address>;

  /** Disconnect/lock */
  disconnect(): Promise<void>;

  /** Get connected address, null if not connected */
  getAddress(): Address | null;

  /** Check if connected */
  isConnected(): boolean;

  /** Sign and send a transaction, return tx hash */
  sendTransaction(tx: TransactionRequest): Promise<Hash>;

  /** Sign typed data (EIP-712) — optional */
  signTypedData?(params: SignTypedDataParams): Promise<Hash>;

  /** Adapter type identifier */
  readonly type: "hd" | "walletconnect" | "tonconnect" | "external";
}
```

#### TransactionRequest

```ts
interface TransactionRequest {
  to: Address;
  data?: `0x${string}`;
  value?: bigint;
  chainId?: number;
  gas?: bigint;
}
```

### Payment

#### PaymentMethod

```ts
type PaymentMethod = "crypto" | "fiat";
```

#### PayParams

Parameters passed to `CryptoPaymentProvider.pay()` (the low-level provider, not `ButterPay.pay()`).

```ts
interface PayParams {
  invoiceId: string;
  chain: ChainName;
  token: string;
  amount: string;                       // human-readable decimal
  merchantAddress: Address;
  paymentRouterAddress: Address;
  invoiceIdBytes32: `0x${string}`;      // keccak256(invoiceId) — bytes32 for contract
  serviceFeeBps: number;
  referrer?: Address;
  referrerFeeBps?: number;
  deadline: number;                     // unix timestamp (30-min window typical)
}
```

#### PayResult

```ts
interface PayResult {
  txHash: Hash;
  chain: ChainName;
  status: "submitted" | "confirmed" | "failed";
}
```

### API objects

#### Invoice

```ts
interface Invoice {
  id: string;
  merchantId: string;
  merchantName?: string;
  merchantOrderId?: string;
  merchantReceivingAddresses?: Record<string, string>;
  amount: string;           // decimal string, e.g. "49.990000000000000000"
  token: string;            // e.g. "USDT"
  chain: string;            // e.g. "arbitrum"
  status: string;           // "created" | "initiated" | "confirmed" | "failed" | "expired" | "refunded"
  paymentMethod?: string;
  payerAddress?: string;
  txHash?: string;
  serviceFee?: string;
  merchantReceived?: string;
  description?: string;
  expiresAt?: string;       // ISO 8601
  redirectUrl?: string;
  createdAt: string;        // ISO 8601
}
```

#### BalanceInfo

```ts
interface BalanceInfo {
  chain: ChainName;
  token: string;        // e.g. "USDT"
  balance: string;      // human-readable decimal, e.g. "100.500000"
  rawBalance: bigint;   // on-chain raw value (before dividing by decimals)
}
```

### Subscriptions

#### Plan

```ts
interface Plan {
  id: string;
  merchantId: string;
  merchantName?: string;
  merchantReceivingAddresses?: Record<string, string>;
  name: string;
  description?: string;
  amountUsd: string;    // decimal string, e.g. "9.99"
  interval: number;     // billing interval in seconds, e.g. 2592000 (30 days)
  cycles: number;       // total number of billing cycles
  chain: string;
  token: string;
  active: boolean;
  createdAt: string;    // ISO 8601
  updatedAt?: string;   // ISO 8601
}
```

#### Subscription

```ts
interface Subscription {
  id: string;
  merchantId: string;
  planId?: string;
  subscriberAddress: string;
  chain: string;
  token: string;
  amount: string;         // per-cycle amount, decimal string
  interval: number;       // seconds between charges
  cyclesTotal: number;
  cyclesCharged: number;
  onChainId?: number;     // ID in the SubscriptionManager contract
  status: "active" | "cancelled" | "expired" | "completed" | "past_due";
  nextChargeAt?: string;  // ISO 8601
  lastChargedAt?: string; // ISO 8601
  expiresAt?: string;     // ISO 8601
  cancelledAt?: string;   // ISO 8601
  createdAt: string;      // ISO 8601
}
```

#### SubscribeParams

Parameters for `SubscriptionProvider.subscribe()` (the low-level provider, not `ButterPay.subscribe()`).

```ts
interface SubscribeParams {
  plan: Plan;
  subscriberAddress: Address;           // must match connected wallet
  subscriptionManagerAddress: Address;  // SubscriptionManager contract on the plan's chain
  expiry?: number;                      // unix timestamp; 0 = no expiry
}
```

#### SubscribeResult

Returned by both `ButterPay.subscribe()` and `SubscriptionProvider.subscribe()`.

```ts
interface SubscribeResult {
  subscription: Subscription;   // persisted backend record
  onChainId: number;            // ID in the SubscriptionManager contract
  approveTxHash?: Hash;         // undefined if allowance was already sufficient
  subscribeTxHash: Hash;        // on-chain subscribe() tx
  chain: ChainName;
}
```

### HD wallet

#### HDWalletConfig

```ts
interface HDWalletConfig {
  password?: string;   // password for encrypting the keystore
  mnemonic?: string;   // pre-existing BIP39 mnemonic to import
}
```

#### Keystore

Encrypted wallet backup produced by `HDWalletAdapter.encryptToKeystore()`.

```ts
interface Keystore {
  ciphertext: string;  // AES-256-GCM encrypted mnemonic (hex)
  salt: string;        // PBKDF2 salt (hex)
  iv: string;          // AES-GCM IV (hex)
  version: number;     // format version — currently 1
}
```

---

## Custom chain configuration

Pass a `chains` override map to the `ButterPay` constructor to replace RPC endpoints, contract
addresses, or token lists for any chain. This is the primary mechanism for pointing the SDK at a
testnet or a self-deployed set of contracts.

```ts
import { ButterPay, ExternalWalletAdapter, defaultChainConfigs } from "@butterpay/core";

const sdk = new ButterPay({
  apiUrl: "http://localhost:3000",
  wallet: new ExternalWalletAdapter(window.ethereum),
  chains: {
    // Override arbitrum to use testnet contracts
    arbitrum: {
      rpcUrl: "https://sepolia-rollup.arbitrum.io/rpc",
      paymentRouterAddress: "0x2bb7f9678c6FC1F2538172F5621087a9D44F9D63",
      subscriptionManagerAddress: "0x51Aaf344ee7b3d35e8347afbDA777e45c7441cd6",
      tokens: [
        { symbol: "USDT", address: "0x536BB419E953eC88f92f6fB23b9331071BF127db", decimals: 6 },
        { symbol: "USDC", address: "0xb8BC61289E64db67b7AC5887406dEf512Ec36A81", decimals: 6 },
      ],
    },
  },
});
```

Overrides are merged shallowly — only the keys you provide replace the defaults from
`defaultChainConfigs`. Unspecified fields (e.g., `viemChain`, `blockExplorerUrl`) retain their
default values.

To inspect or extend the defaults directly:

```ts
import { defaultChainConfigs } from "@butterpay/core";

// defaultChainConfigs is a Record<ChainName, ChainConfig>
// Chains: ethereum, arbitrum, bsc, polygon, arbitrumSepolia
console.log(defaultChainConfigs.arbitrum.paymentRouterAddress);
// → "0x4b32bcd3eC4F0a14D7061e0d239eBAd84F77743f"
```

---

## Server-side usage

On the server, use `ApiClient` directly — no wallet or on-chain interaction is needed. Pass
`apiKey` to authenticate merchant-only endpoints.

```ts
import { ApiClient } from "@butterpay/core";

const api = new ApiClient({
  baseUrl: "https://api.butterpay.io",
  apiKey: process.env.BUTTERPAY_API_KEY,
});

// Create an invoice; redirect the user to the hosted payment page
const invoice = await api.createInvoice({
  amountUsd: "49.99",
  merchantOrderId: "order-001",
  description: "Pro plan — monthly",
  redirectUrl: "https://your-site.example.com/thank-you",
  webhookUrl: "https://your-site.example.com/webhooks/butterpay",
});

const payUrl = `https://pay.butterpay.io/pay/${invoice.id}`;
```

You can also use `ButterPay` on the server by passing a no-op stub as the wallet and relying on
`apiKey` for authentication. However, for pure server-side work, instantiating `ApiClient`
directly is simpler and avoids the dependency on a wallet adapter.

### Managing plans and subscriptions server-side

```ts
// Create a recurring plan
const plan = await api.createPlan({
  name: "Premium Monthly",
  amountUsd: "9.99",
  intervalSeconds: 30 * 24 * 60 * 60, // 30 days
  cycles: 12,
  chain: "arbitrum",
  token: "USDT",
});

const subscribeUrl = `https://pay.butterpay.io/subscribe/${plan.id}`;

// Fetch subscribers
const subscribers = await api.listSubscriptions("active");

// Pause a plan (stop new sign-ups without deleting)
await api.updatePlan(plan.id, { active: false });
```

### ApiClient constructor

```ts
import { ApiClient, type ApiClientConfig } from "@butterpay/core";

const api = new ApiClient({
  baseUrl: "https://api.butterpay.io",  // required
  apiKey: "bp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx...",               // optional; adds X-Api-Key header to every request
});
```

The client sends `X-Api-Key: <apiKey>` on every request when `apiKey` is set. Errors from the
API surface as thrown `Error` instances with the server-provided message.

---

## See also

- [API Reference](../api/invoices.md)
- [Authentication](../authentication.md)
- [Webhooks](../webhooks.md)
