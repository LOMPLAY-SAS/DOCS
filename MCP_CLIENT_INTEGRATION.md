# MCP Client Integration

Connect any MCP-compatible agent to LOMPLAY's gateway (`@lomplay/mcp-server`, `:3100`).

For the **browser-only, no-terminal walkthrough** (ChatGPT / Claude.ai / Gemini, key or
OAuth, operator tunnel setup), see [`CONNECT_YOUR_LLM.md`](CONNECT_YOUR_LLM.md) — this
document is for **desktop/programmatic clients** (Claude Desktop, MCP Inspector, a custom
SDK client) and the **OAuth token-lifecycle contract** every client needs regardless of
transport.

## 1. Obtain an API key (bearer auth)

Create a key in Stageforge: **Settings → Integrations → MCP** (organizer) or **My account
→ AI agent access** (buyer), or via API:

```http
POST /api/organizer/mcp-credentials
Authorization: Bearer <supabase-session>
x-org-id: <organization-uuid>
Content-Type: application/json

{
  "name": "My agent",
  "domains": ["show_lifecycle", "door_operations"],
  "expires_in_days": 30
}
```

`domains` (not `allowed_tools`) is how you scope a key — one or more capability domains
from `persona-capabilities.yml`'s `domain` field; the resolved tool set is the
intersection of those domains' tools and whatever role you currently hold. `expires_in_days`
is **mandatory**, 1–90 — there is no "never expires" option, and a request without it is
rejected with 400, not silently defaulted. `allowed_tools` is still accepted as an
additional intersection on top of `domains`, but `"*"`/wildcard is always refused.

Response includes `secret` **once** — store it securely.

## 2. Claude Desktop (stdio)

```json
{
  "mcpServers": {
    "lomplay": {
      "command": "node",
      "args": ["/path/to/mcp-server/dist/index.js"],
      "env": {
        "NETWORK": "sepolia",
        "TRANSPORT": "stdio",
        "MCP_DEV_BYPASS_ORG": "true",
        "MCP_DEV_ORG_ID": "<your-org-uuid>",
        "MCP_DEV_ALLOWED_TOOLS": "*"
      }
    }
  }
}
```

Use dev bypass only locally. Production stdio should use real keys via an HTTP wrapper if
needed — `MCP_DEV_*` vars are refused at boot under `NODE_ENV=production`
(`config/prodSafety.ts`).

## 3. Remote HTTP (Streamable HTTP)

Server: `npm run start:http` (see
[`hardhat_contracts/hardhat/mcp-server/docs/MCP_DEPLOYMENT.md`](../hardhat_contracts/hardhat/mcp-server/docs/MCP_DEPLOYMENT.md)).

> **Browser LLM connectors** (Claude.ai / ChatGPT / Gemini) connect from the vendor's
> servers, so they need a **public HTTPS** URL — `127.0.0.1:3100` is unreachable. Put the
> gateway behind a public front (Cloudflare Tunnel) and set `MCP_PUBLIC_URL` so RFC 9728
> discovery advertises the reachable origin. Full operator walkthrough:
> [`CONNECT_YOUR_LLM.md`](CONNECT_YOUR_LLM.md). The examples below use the local URL for
> desktop clients (Claude Desktop, Cursor, MCP Inspector).

### MCP Inspector (browser)

1. Start the server: `cd hardhat_contracts/hardhat/mcp-server && npm run build && npm run start:http`
2. Open Inspector (e.g. `npx @modelcontextprotocol/inspector` → UI at `http://localhost:6274`)
3. **Transport type:** choose **Streamable HTTP** (or **HTTP**) — **not** STDIO
4. **URL:** `http://127.0.0.1:3100/mcp` — **port 3100** (mcp-server), **not** `3000` (Stageforge) or `3001` (Backstage)
5. **Authentication:** expand **Authentication** → Bearer token → paste your `mcp_live_…` key
6. Connect, then list tools and call `create_show` with `dryRun: true`

**Common mistake:** putting `http://127.0.0.1:3100/mcp` in the **Command** box with
**STDIO** transport makes the Inspector proxy try to spawn that URL as a process
(`/stdio?command=http...`) and return **500 SSE error**.

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StreamableHTTPClientTransport } from "@modelcontextprotocol/sdk/client/streamableHttp.js";

const transport = new StreamableHTTPClientTransport(
  new URL("http://127.0.0.1:3100/mcp"),
  {
    requestInit: {
      headers: {
        Authorization: `Bearer ${process.env.LOMPLAY_MCP_API_KEY}`,
        "x-actor-user-id": process.env.ACTOR_USER_ID ?? "",
      },
    },
  }
);

const client = new Client({ name: "my-agent", version: "1.0.0" });
await client.connect(transport);
const tools = await client.listTools();
```

## 4. OAuth clients — the full grant lifecycle

A client that discovers OAuth support (`grant_types_supported` on
`/.well-known/oauth-authorization-server` includes `"refresh_token"`) should implement
the whole lifecycle below, not just the initial code exchange — a client that only
handles step 4 will silently break the moment the user's first access token expires.

| Step | Call | Notes |
|---|---|---|
| 1. Discover | `GET {issuer}/.well-known/oauth-protected-resource`, then `{issuer}/.well-known/oauth-authorization-server` | RFC 9728 → RFC 8414. |
| 2. Register | `POST /api/oauth/register` `{redirect_uris: [...]}` | RFC 7591 DCR, unauthenticated, rate-limited server-side. Returns a `client_id` (a signed, stateless token — no client secret; `token_endpoint_auth_method: "none"`). |
| 3. Authorize | Browser navigation to `/oauth/authorize?...&code_challenge=...&code_challenge_method=S256` | PKCE **required**, S256 only. User signs in (if needed), picks a persona, approves. Redirects back with `?code=...`. |
| 4. Token (first exchange) | `POST /api/oauth/token` `{grant_type: "authorization_code", code, code_verifier, client_id, redirect_uri}` | Returns `access_token` (1h), `refresh_token`. **The code is single-use** — a second exchange of the same code returns `400 invalid_grant`, even seconds later. |
| 5. Refresh | `POST /api/oauth/token` `{grant_type: "refresh_token", refresh_token}` | Returns a **new** `access_token` and a **rotated** `refresh_token` — the old refresh token stops working the instant the new one is issued. Always store the refresh token you get back, not the one you sent. |
| 6. Revoke | `POST /api/oauth/revoke` `{token, token_type_hint: "refresh_token" \| "access_token"}` | RFC 7009. Always returns `200`, whether or not the token was valid — never use the response to probe validity. A revoked grant is denied gateway-side within ~30s even if its access token hasn't expired yet. |

Every access token embeds `credential_id: "grant:<uuid>"` — invocations made under it are
attributed to that grant (not the user id) in `mcp_tool_invocations`, so a specific
connector's activity can be audited or revoked independently of the user's other grants.

## 5. Stageforge service client (PRD-010)

Stageforge Agent mode uses the same HTTP endpoint with a service-issued org key and
forwards `x-org-id` / `x-actor-user-id` from the signed-in organizer session.

## Tool parity + verification

The full chain-relayer tool ↔ Stageforge UI ↔ protocol-surface table, and the
verification smoke commands, live in
[`hardhat_contracts/hardhat/mcp-server/docs/MCP_CLIENT_INTEGRATION.md`](../hardhat_contracts/hardhat/mcp-server/docs/MCP_CLIENT_INTEGRATION.md#tool-parity-matrix)
— kept there, not duplicated here, so it never drifts from whoever last touched the tool
set. `PERSONA-CAPABILITIES.md` (repo root, generated from `persona-capabilities.yml`) is
the source of truth for the **full** tool list including app-api (non-chain) tools.
