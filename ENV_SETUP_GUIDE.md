# LOMPLAY — Environment variable audit & step-by-step setup

**Generated:** 2026-06-02  
**Scope:** PRDs 049–052 and related platform env (Stageforge, Backstage, Gate Validator, indexing, MCP).  
**How to use:** Work through [Step-by-step setup](#step-by-step-setup) in order. Use [Pending variables](#pending-variables-by-app) as a checklist.

All file paths below are **absolute** paths on your machine.

---

## 1. Environment file inventory

| App / service | Template (copy from) | Your local secrets file | Notes |
|---------------|----------------------|-------------------------|--------|
| **Stageforge Pro** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` | Primary app (port 3000) |
| **Backstage** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local` | Admin UI (port 3001) |
| **Gate Validator PWA** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.local` | **File missing — create it** |
| **Supabase Edge (local)** | (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example` Edge section) | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/.env.local` | Edge Functions only |
| **Analytics API** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env` | ClickHouse + Redis |
| **Substreams sink** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/substreams/sink-config/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/substreams/sink-config/.env` | `SUBSTREAMS_API_KEY` |
| **MCP server** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/mcp-server/.env.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/mcp-server/.env` | Organizer agent |
| **Hardhat deploy** | — | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/.env` | Foundry / Sepolia RPC |
| **Production template** | — | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.production` | Not for local dev |
| **Legacy landing** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/landing_app/.envrc.example` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/landing_app/.env.local` | **Not** part of PRD 049–052 |

**Related docs**

- `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/project_docs/LOCAL_DEV_SETUP.md`
- `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/project_docs/LOCAL_DEV_SETUP.md`
- `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/skills/env-and-config/SKILL.md`
- Root platform commands: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/Makefile` → delegates to `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/Makefile`

---

## 2. Pending variables by app

Legend: **Missing** = in `.env.example` but not in your local file. **Empty** = key exists, value blank. **Placeholder** = dummy value; replace before using that feature. **Fix** = set but wrong shape.

### 2.1 `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`

#### Already OK for minimum local platform (no action unless you change stack)

Core Supabase, Anvil protocol, Privy, Alchemy, deployer wallets, local analytics URL, organizer agent MCP, most feature toggles you already set.

#### Fix now (wrong or risky)

| Variable | Issue | What to set |
|----------|--------|-------------|
| `PROTOCOL_ACTION_SERVICE_URL` | Points at Supabase **dashboard** URL, not the API | Local: `http://127.0.0.1:54321/functions/v1/protocol-actions` — see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/mcp-server/.env` for a working example |
| `EDGE_SUPABASE_ANON_KEY` | **Missing**; Edge runtime strips `SUPABASE_*` from env-file | Copy same JWT as `SUPABASE_ANON_KEY` in this file (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/functions/_shared/supabase-env.ts`) |

#### Empty (only needed if you use mainnet chain picker)

- `NEXT_PUBLIC_MAINNET_RPC_URL`
- `NEXT_PUBLIC_MAINNET_REGISTRY_ADDRESS`
- `OPENAI_API_KEY` (optional; you use `AI_GATEWAY_API_KEY` today)
- `INDEXER_WEBHOOK_SECRET` — **required for PRD-050 / PRD-052 indexer mirrors** (key present, no value)

#### Placeholder (Stripe MPP + x402 — do not use until replaced)

- `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
- `MPP_PLATFORM_RELAYER_ADDRESS`, `MPP_PLATFORM_RELAYER_PRIVATE_KEY`, `MPP_MARKETPLACE_DIAMOND_ADDRESS`
- `X402_PAYEE_ADDRESS` (`0x000…000`)

#### Missing — optional / feature-specific

**Analytics & AI**

- `ANALYTICS_API_KEY` — only if analytics API enforces auth
- `GOOGLE_GENERATIVE_AI_API_KEY` — preferred for Next.js routes (you have `AI_GATEWAY_API_KEY`; add this name or alias in code paths that read `GOOGLE_GENERATIVE_AI_API_KEY`)
- `GEMINI_API_KEY` in Stageforge root (you have it under `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/.env.local` for Edge)

**Email**

- `RESEND_FROM_EMAIL` — you use `EMAIL_FROM` instead; applicant emails use `RESEND_FROM_EMAIL` in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/src/lib/email/notifyApplicant.ts` — **add `RESEND_FROM_EMAIL` or duplicate your sender**

**Webhooks / listeners**

- `WEBHOOK_EVENT_SECRET`, `EVENT_LISTENER_TOKEN`, `EVENT_LISTENER_ORGANIZER_PRIVATE_KEY`
- `ONRAMP_WEBHOOK_SECRET`, `OFFRAMP_WEBHOOK_SECRET`
- `AGENT_ORCHESTRATOR_TOKEN`

**Feature flags** (defaults exist in code if unset; set explicitly to match `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example`)

- `NEXT_PUBLIC_FEATURE_PASSKEYS`, `NEXT_PUBLIC_FEATURE_SMART_SESSIONS`, `NEXT_PUBLIC_FEATURE_ECVA`
- `NEXT_PUBLIC_LAUNCH_AT_SERIES_LEVEL`, `NEXT_PUBLIC_X402_UI_HINT`
- `NEXT_PUBLIC_USE_MOCKS`, `NEXT_PUBLIC_PERF_SAMPLE_RATE`
- `PLAYWRIGHT_BASE_URL`, `CI`

**PRD-031 Ticket QR** (Backstage has partial setup; Stageforge missing)

- `ENABLE_TICKET_QR`, `NEXT_PUBLIC_ENABLE_TICKET_QR`, `NEXT_PUBLIC_ENTRY_BASE_URL`, `TICKET_QR_SIGNING_SECRET`
- `SCANNER_SESSION_SECRET`, `GATE_OPERATOR_PIN`, `GATE_OPERATOR_DEFAULT_ID`, `GATE_ATTENDANCE_RELAYER_PRIVATE_KEY`

**PRD-021 MPP / Stripe extras**

- `MPP_USDC_ADDRESS`, `MPP_RELAYER_VIA_SAFE`, `MPP_RELAYER_SAFE_ADDRESS`, `MPP_RELAYER_ROLES_MODIFIER_ADDRESS`, `MPP_RELAYER_ROLE_KEY`, `STRIPE_ENABLED`
- `NEXT_PUBLIC_ALCHEMY_GAS_MANAGER_POLICY_ID` (you have `NEXT_PUBLIC_ALCHEMY_POLICY_ID`)

**PRD-025 Fiat ramp**

- `NEXT_PUBLIC_COINBASE_ONRAMP_APP_ID`

**PRD-050 / PRD-052 indexer** (required for mirror webhooks + `make smoke-*`)

- `INDEXER_WEBHOOK_SECRET` (generate — see Step 6)
- `STAGEFORGE_URL` — default `http://localhost:3000` for forwarders
- `VENDOR_POS_SHOW_PROXIES` — optional filter
- All `SMOKE_VENDOR_POS_*` and `SMOKE_RESALE_*` — **only** for smoke scripts in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/scripts/`

**Misc**

- `PROTOCOL_OPERATOR_FUND_WEI`, `NEXT_PUBLIC_BACKSTAGE_APPLICATIONS_URL`

#### Extra keys in your local file (not in `.env.example` — OK to keep)

`EMAIL_FROM`, `MARKETPLACE_QUOTE_RATES_JSON`, `MCP_KEY_HASH_PEPPER`, `NEXT_PUBLIC_APP_URL`, `NEXT_PUBLIC_PLAYWRIGHT_TEST`, `NEXT_PUBLIC_PROTOCOL_LOCAL_STABLECOIN_2`, `X402_MAX_TIMEOUT_SECONDS`

---

### 2.2 `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local`

| Status | Variable | Action |
|--------|----------|--------|
| Missing | `NEXT_PUBLIC_ALCHEMY_GAS_POLICY_ID` | Copy from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` → `NEXT_PUBLIC_ALCHEMY_POLICY_ID` (same Gas Manager policy UUID) |
| Optional missing | `CLOUDFLARE_TUNNEL_TOKEN` | Only if using Cloudflare tunnel (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.example`) |
| **Fix** | `TICKET_QR_SIGNING_SECRET` | Value is literal `$(openssl rand -hex 32)` — **dotenv does not run shell**. Generate: `openssl rand -hex 32` and paste 64 hex chars. Must **match** Stageforge once you set it there. |

---

### 2.3 `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/`

| Status | Variable | Action |
|--------|----------|--------|
| **No local file** | `VITE_STAGEFORGE_API_ORIGIN` | Create `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.local` with `VITE_STAGEFORGE_API_ORIGIN=http://localhost:3000` (copy from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.example`) |

---

### 2.4 `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/.env.local`

No separate `.env.example`. Current file has `ES256_SERVICE_ROLE_KEY`, `GEMINI_API_KEY`, `LLM_PROVIDER` — **sufficient for local Edge AI**.

Align `ES256_SERVICE_ROLE_KEY` with `SUPABASE_SERVICE_ROLE_KEY` in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` after every `supabase stop` / `supabase start`.

---

### 2.5 Indexing stack

**Analytics API** — `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env`

| Status | Variables |
|--------|-----------|
| Missing vs example | `CACHE_TTL_ORGANIZER_SHOWS`, `CACHE_TTL_SHOW_INSURANCE_POOL`, `CACHE_TTL_SHOW_METADATA`, `CACHE_TTL_SHOW_REVENUE_TIERS` (copy defaults from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env.example`) |
| Empty OK locally | `CLICKHOUSE_PASSWORD` (empty = no password on local ClickHouse) |
| Set | `SEPOLIA_RPC_URL`, ClickHouse host, CORS — good for Sepolia analytics |

**Substreams sink** — `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/substreams/sink-config/.env`

| Status | Variables |
|--------|-----------|
| Missing vs example | `ON_MODULE_HASH_MISMATCH`, `UNDO_BUFFER_SIZE` |
| Set | `SUBSTREAMS_API_KEY`, endpoint, ClickHouse — OK for Sepolia indexing |

Stageforge expects analytics at `NEXT_PUBLIC_ANALYTICS_API_URL` / `NEXT_ANALYTICS_API_URL` = `http://localhost:3003` but analytics `.env` has `PORT=3001` — confirm which port your `make platform` actually binds (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/Makefile` platform targets).

---

### 2.6 `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/mcp-server/.env`

| Status | Variables |
|--------|-----------|
| Placeholder | `SEPOLIA_RPC_URL`, `MAINNET_RPC_URL` (still `YOUR_KEY`) — OK if `NETWORK=localhost` |
| Missing vs example | `LOMPLAY_MCP_API_KEY`, `LOMPLAY_MCP_URL`, `ORGANIZER_AGENT_ENABLED`, `NEXT_PUBLIC_ORGANIZER_AGENT_ENABLED`, `SSE_PORT` — copy from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` if you use HTTP MCP + organizer agent |

`PROTOCOL_ACTION_SERVICE_URL` here is **correct** (`http://127.0.0.1:54321/functions/v1/protocol-actions`) — mirror that into Stageforge.

---

## 3. Step-by-step setup

Run commands from the directory shown. Replace nothing in git — only edit your `*.env.local` / `*.env` files.

### Step 0 — Choose what you need

| Goal | Minimum files to configure |
|------|----------------------------|
| **A. Local platform** | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`, `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local` |
| **B. + Gate scanning** | + `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.local`, ticket QR secrets on A + B |
| **C. + Indexer mirrors (050/052)** | + `INDEXER_WEBHOOK_SECRET` in Stageforge, indexing forwarders |
| **D. + Analytics charts** | + `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env`, enable `NEXT_PUBLIC_ANALYTICS_ENABLED=true` in Stageforge |
| **E. + Card payments (MPP)** | + real Stripe + relayer vars in Stageforge |
| **F. + x402 HTTP rail** | + real `X402_PAYEE_ADDRESS` |

---

### Step 1 — Start local Supabase and copy API keys

**Where values come from:** Supabase CLI after `supabase start` (local project in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/`).

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro
npx supabase status -o env
```

Copy into **both**:

- `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`
- `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local`

| Variable | Source |
|----------|--------|
| `NEXT_PUBLIC_SUPABASE_URL` | `API_URL` → `http://127.0.0.1:54321` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | `ANON_KEY` (JWT) |
| `SUPABASE_SERVICE_ROLE_KEY` | `SERVICE_ROLE_KEY` (JWT, ES256 locally) |
| `SUPABASE_URL` | Same as public URL (Stageforge only) |
| `SUPABASE_ANON_KEY` | Same as anon JWT (Stageforge only) |
| `EDGE_SUPABASE_API_URL` | `http://127.0.0.1:54321` |
| `EDGE_SUPABASE_ANON_KEY` | Same as anon JWT |
| `ES256_SERVICE_ROLE_KEY` | Same as service role JWT → also `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/.env.local` |
| `DATABASE_URL` | `postgresql://postgres:postgres@127.0.0.1:54322/postgres` |
| `PROTOCOL_ACTION_SERVICE_TOKEN` | Same as service role (local dev) |

**Boot everything (recommended):**

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY
make platform
```

Logs: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.logs/` (when using platform script).

**Health check:**

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY
make platform-status
```

---

### Step 2 — Deploy contracts on Anvil and sync addresses

**Where values come from:** `make deploy-local` / `make sync-addresses` in Stageforge (writes registry + stablecoin into env).

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro
make deploy-local
```

Confirm in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`:

| Variable | Typical source |
|----------|----------------|
| `NEXT_PUBLIC_PROTOCOL_REGISTRY_ADDRESS` | Deploy broadcast / `make sync-addresses` |
| `NEXT_PUBLIC_PROTOCOL_LOCAL_STABLECOIN` | ERC20Mock USDC from deploy |
| `DEPLOYER_WALLET_ADDRESS` | Anvil account #9 or your operator |
| `PROTOCOL_OPERATOR_WALLETS` | Comma-separated operator EOAs |

Mirror into `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local`:

| Backstage variable | Stageforge equivalent |
|--------------------|------------------------|
| `NEXT_PUBLIC_PROTOCOL_REGISTRY_ADDRESS` | same |
| `DEPLOYER_ADDRESS` | `DEPLOYER_WALLET_ADDRESS` |
| `PROTOCOL_OPERATOR_WALLETS` | same |
| `NEXT_PUBLIC_PROTOCOL_LOCAL_STABLECOIN` | same |
| `NEXT_PUBLIC_CHAIN_ID` | `NEXT_PUBLIC_PROTOCOL_CHAIN_ID` (31337) |
| `NEXT_PUBLIC_RPC_URL` | `NEXT_PUBLIC_PROTOCOL_RPC_URL` |

---

### Step 3 — Privy + Alchemy (smart wallets)

| Variable | Where to get it |
|----------|-----------------|
| `NEXT_PUBLIC_PRIVY_APP_ID`, `NEXT_PUBLIC_PRIVY_CLIENT_ID` | [Privy Dashboard](https://dashboard.privy.io) → your app → Settings |
| `NEXT_PUBLIC_ALCHEMY_API_KEY` | [Alchemy Dashboard](https://dashboard.alchemy.com) → Apps → API Key |
| `NEXT_PUBLIC_ALCHEMY_POLICY_ID` / `NEXT_PUBLIC_ALCHEMY_GAS_MANAGER_POLICY_ID` | Alchemy → Gas Manager → Policies |
| `NEXT_PUBLIC_ALCHEMY_GAS_POLICY_ID` | Same policy UUID → Backstage only |

Set on Stageforge + Backstage local files. You already have real Privy/Alchemy values in both files.

---

### Step 4 — Fix protocol-actions URL (Stageforge + MCP)

In `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` set:

```dotenv
PROTOCOL_ACTION_SERVICE_URL=http://127.0.0.1:54321/functions/v1/protocol-actions
```

(Production: `https://<project-ref>.supabase.co/functions/v1/protocol-actions` from Supabase Dashboard → Edge Functions.)

Match `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/mcp-server/.env`.

---

### Step 5 — Gate Validator PWA (PRD-038)

Create `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.local`:

```dotenv
VITE_STAGEFORGE_API_ORIGIN=http://localhost:3000
```

Run dev server from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/` per that package’s README.

---

### Step 6 — Ticket QR (PRD-031)

**Generate secret (once, share across apps):**

```bash
openssl rand -hex 32
```

| File | Variables |
|------|-----------|
| `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` | `TICKET_QR_SIGNING_SECRET=<64-hex>`, `ENABLE_TICKET_QR=true`, `NEXT_PUBLIC_ENABLE_TICKET_QR=true`, `NEXT_PUBLIC_ENTRY_BASE_URL=http://localhost:3000` |
| `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local` | Same `TICKET_QR_SIGNING_SECRET` (replace the `$(openssl …)` literal) |

Optional gate APIs (Stageforge): `SCANNER_SESSION_SECRET`, `GATE_OPERATOR_PIN`, `GATE_ATTENDANCE_RELAYER_PRIVATE_KEY` — see comments in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example` (PRD-038 section).

---

### Step 7 — Indexer webhooks (PRD-050 vendor POS, PRD-052 resale)

**Where value comes from:** you invent a long random secret; forwarders must send `Authorization: Bearer <secret>`.

```bash
openssl rand -base64 48
```

Add to `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`:

```dotenv
INDEXER_WEBHOOK_SECRET=<paste-secret>
STAGEFORGE_URL=http://localhost:3000
```

Forwarder code lives under `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/` (npm scripts `vendor-pos:forward`, `resale-offers:forward` — see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example` PRD-050/052 blocks).

**Smoke tests (optional):**

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro
make smoke-vendor-pos-indexer
make smoke-resale-offers-indexer
```

Scripts: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/scripts/smoke-vendor-pos-indexer-mirror.mjs`, `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/scripts/smoke-resale-offers-indexer.mjs`

---

### Step 8 — PRD-052 marketplace quotes (multi-stablecoin)

You already set `MARKETPLACE_QUOTE_RATES_JSON` in Stageforge. Optional overrides in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example`:

- `MARKETPLACE_CHAINLINK_FEEDS_JSON` — Chainlink feed addresses per chain
- `MARKETPLACE_QUOTE_TTL_SEC`, `MARKETPLACE_QUOTE_SLIPPAGE_BPS`, etc.

Decision doc: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/PRD/handoffs/AUDIT-052-remediation-product-multicoin-decision.md`

---

### Step 9 — Stripe MPP (PRD-021 Phase 6)

Only if you need card checkout.

| Variable | Where to get it |
|----------|-----------------|
| `STRIPE_SECRET_KEY`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` | [Stripe Dashboard](https://dashboard.stripe.com/test/apikeys) → Developers → API keys |
| `STRIPE_WEBHOOK_SECRET` | Stripe CLI: `stripe listen` or Dashboard → Webhooks → signing secret |
| `MPP_MARKETPLACE_DIAMOND_ADDRESS` | `make deploy-local` output or Sepolia deploy logs in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/hardhat/` |
| `MPP_PLATFORM_RELAYER_ADDRESS`, `MPP_PLATFORM_RELAYER_PRIVATE_KEY` | Wallet you control (Anvil test key for local only) |
| `MPP_USDC_ADDRESS` | Default in `.env.example` for Base Sepolia |

Enable: `MPP_ENABLED=true`, `NEXT_PUBLIC_MPP_ENABLED=true`, optionally `STRIPE_ENABLED=true` for `make stripe-listen` (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/Makefile`).

---

### Step 10 — x402 HTTP rail (PRD-021)

| Variable | Where to get it |
|----------|-----------------|
| `X402_FACILITATOR_URL` | Default `https://x402.org/facilitator` or Coinbase CDP (see comments in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example`) |
| `X402_PAYEE_ADDRESS` | Your settlement wallet on the network in `X402_NETWORK` (e.g. `eip155:84532` = Base Sepolia) |

You have `X402_ENABLED=true` but payee is zero address — fix before testing purchases.

---

### Step 11 — AI (PRD-013, organizer agent)

| Variable | File | Where to get it |
|----------|------|-----------------|
| `GOOGLE_GENERATIVE_AI_API_KEY` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` | [Google AI Studio](https://aistudio.google.com/apikey) |
| `GEMINI_API_KEY` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/.env.local` | Same key (Edge Functions) |
| `GOOGLE_GENERATIVE_AI_API_KEY` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local` | Same (Dynamic UI builder) |

Set `NEXT_PUBLIC_ENABLE_AI=true` on Stageforge when ready.

---

### Step 12 — Email (Resend)

| Variable | Where to get it |
|----------|-----------------|
| `RESEND_API_KEY` | [Resend](https://resend.com) → API Keys (you already have a key in Stageforge) |
| `RESEND_FROM_EMAIL` | Verified sender domain in Resend (add to Stageforge; you use `EMAIL_FROM` for other templates) |

---

### Step 13 — Substreams / analytics (optional Sepolia pipeline)

| Variable | File | Where to get it |
|----------|------|-----------------|
| `SUBSTREAMS_API_KEY` | `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/substreams/sink-config/.env` | [substreams.dev](https://substreams.dev) |
| `SEPOLIA_RPC_URL` | analytics `.env` | Alchemy/Infura (you use Alchemy in analytics `.env`) |
| ClickHouse | Docker / local install | Defaults in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env.example` |

Turn on UI: `NEXT_PUBLIC_ANALYTICS_ENABLED=true` in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`.

---

### Step 14 — Restart after any `.env` change

Next.js and Vite **do not** hot-reload env vars.

```bash
cd /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY
make platform-stop
make platform
```

Or restart individual apps from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/` and `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/`.

---

## 4. Feature checklists (copy/paste)

### A — Minimum local platform

- [ ] `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local` — Supabase JWTs + `EDGE_SUPABASE_ANON_KEY`
- [ ] `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/backstage/.env.local` — same Supabase keys + matching registry/RPC
- [ ] Fix `PROTOCOL_ACTION_SERVICE_URL` in Stageforge (Step 4)
- [ ] `make platform` from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/`
- [ ] Open `http://localhost:3000` and `http://localhost:3001`

### B — Gate Validator + ticket QR

- [ ] Create `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/gate-validator/.env.local`
- [ ] Shared `TICKET_QR_SIGNING_SECRET` on Stageforge + Backstage (real hex, not `$(openssl …)`)
- [ ] `ENABLE_TICKET_QR` / `NEXT_PUBLIC_ENABLE_TICKET_QR` on both apps

### C — PRD-050 / PRD-052 indexer mirrors

- [ ] `INDEXER_WEBHOOK_SECRET` in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`
- [ ] Forwarders under `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/` use same secret
- [ ] Optional: `make smoke-vendor-pos-indexer` / `make smoke-resale-offers-indexer` in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/`

### D — Analytics UI

- [ ] ClickHouse + `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/hardhat_contracts/indexing/analytics-api/.env` running
- [ ] `NEXT_PUBLIC_ANALYTICS_ENABLED=true` in Stageforge
- [ ] Port alignment: Stageforge `NEXT_PUBLIC_ANALYTICS_API_URL` vs analytics `PORT`

### E — Stripe MPP

- [ ] Replace all `sk_test_...` / `pk_test_...` / `whsec_...` placeholders in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`
- [ ] Real relayer + `MPP_MARKETPLACE_DIAMOND_ADDRESS`
- [ ] `MPP_ENABLED=true`, `NEXT_PUBLIC_MPP_ENABLED=true`

### F — x402

- [ ] Non-zero `X402_PAYEE_ADDRESS`
- [ ] `X402_NETWORK` matches facilitator + wallet chain

### G — Stripe wallet top-up (PRD-025b)

- [ ] `STRIPE_SECRET_KEY` + `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` (test mode) in `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local`
- [ ] `WALLET_STRIPE_TOPUP_ENABLED=true`, `NEXT_PUBLIC_WALLET_STRIPE_TOPUP_ENABLED=true`
- [ ] `make platform` from repo root — auto-starts Stripe listener and syncs `STRIPE_WEBHOOK_SECRET` (no separate `make stripe-listen` terminal)
- [ ] Optional: `make stripe-listen` for foreground debugging only

### H — Dev off-ramp simulator (PRD-025 / contractor withdraw)

- [ ] `WALLET_DEV_OFFRAMP_ENABLED=true`, `NEXT_PUBLIC_WALLET_DEV_OFFRAMP_ENABLED=true` in `stageforge-pro/.env.local`
- [ ] Restart Stageforge after enabling (or full `make platform`)
- [ ] Provider portal → **Wallet balance** → **Manage** → **Withdraw** → **Dev bank payout (local)**
- [ ] Verify **Transaction history** shows **Fiat received: … EUR → Bank account ••••…**
- [ ] For Coinbase on Base Sepolia: copy `stageforge-pro/.env.base-sepolia.example`, set `NEXT_PUBLIC_COINBASE_ONRAMP_APP_ID` (or `NEXT_PUBLIC_ONRAMP_APP_ID`), tunnel `POST /api/wallet/offramp/webhook/coinbase`

---

## 5. Pitfalls

1. **`NEXT_PUBLIC_*` is public** — never put service role, webhook secrets, or private keys there.
2. **Dotenv does not run `$(command)`** — fix Backstage `TICKET_QR_SIGNING_SECRET`.
3. **Edge strips `SUPABASE_*` from `--env-file`** — use `EDGE_SUPABASE_*` and `ES256_SERVICE_ROLE_KEY` (see `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/supabase/functions/_shared/supabase-env.ts`).
4. **Stageforge + Backstage must share** registry address and Supabase project for local dev.
5. **Anvil reset** — re-run `make deploy-local` from `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/` and refresh addresses in both `.env.local` files.

---

## 6. Re-run audit (optional)

From repo root, compare example vs local (requires Python 3):

```bash
python3 - <<'PY'
# Paste the audit script from your agent session, or re-diff manually with:
# diff <(grep -E '^[A-Z]' /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.example | cut -d= -f1 | sort) \
#      <(grep -E '^[A-Z]' /Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/.env.local | cut -d= -f1 | sort)
PY
```

---

*PRD references: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/PRD/` (active/completed). Handoff prompts: `/Users/nicolasguascasantamaria/Documents/GitHub/LOMPLAY/stageforge-pro/PRD/handoffs/AUDIT-052-remediation-prompts-TAILORED.md`.*
