---
marp: true
theme: gaia
size: 16:9
paginate: true
style: |
  section { font-size: 26px; }
  h1 { font-size: 2.1em; margin-bottom: 0.4em; }
  h2 { font-size: 1.35em; font-weight: 600; color: #333; }
  strong { color: #1a1a1a; }
  section.lead h1 { font-size: 2.4em; }
---

<!-- _class: lead -->

# LOMPLAY

## Programmable infrastructure for live events, ticketing, and organizer economics

_Confidential — [Founder fill-in: company legal name]_

---

# The problem

- Ticketing and event ops sit on **fragmented**, decades-old stacks
- Settlement is **opaque**; partners lack a shared source of truth
- Hard to compose **new economics** (insurance, pools, contractor terms) per show
- Fans and regulators expect **provable** inventory and audit trails — without losing UX

---

# Why now

- **Stablecoins and wallet UX** are ready for real-world commerce rails
- **Account abstraction** lowers friction for mainstream buyers and staff flows
- Promoters need **programmable payouts** tied to on-chain rules, not spreadsheets
- **AI agents** need **machine-actionable** protocols — APIs and tools, not PDFs _(MCP / structured ops)_

---

# Our solution

**Protocol + applications**

- **On-chain:** Inventory, access rules, economics, and settlement-oriented logic in **modular, upgradeable** contracts (EIP-2535 Diamond pattern)
- **Off-chain:** **Stageforge Pro** (promoters, buyers) and **Backstage** (platform / tenant ops) on modern web and wallet rails
- **Money:** Crypto-native settlement plus **fiat pathways** (cards, bank, rails) where the product requires them — integrated as first-class flows, not bolt-ons

---

# Product — organizers & fans (**Stageforge Pro**)

- Event and **marketplace** surfaces for discovery and purchase
- **Smart-wallet** and treasury-style flows for holding and moving funds
- **Top-up / off-ramp** patterns for fiat ↔ crypto where configured
- **Operational consoles** for series, launches, and on-chain checklists alongside Supabase-backed ops data

---

# Product — platform & gate (**Backstage**)

- **Multi-tenant** administration and observability for the protocol rollout
- **Gate-ops** and **staff roles** aligned to on-chain attendance and access facets
- **Reconciliation-friendly** views for payment rails (e.g. marketplace purchase / settlement monitoring)
- Shared ABI and hook discipline with the **same** contract surface promoters use — less drift, faster audits

---

# Protocol architecture (conceptual)

```
                    [ Diamond proxy — single entry address ]
                                    |
      +-----------+-----------+-----+-----+-----------+-----------+
      |           |           |           |           |           |
 Registry    Organizer      Show      Minter   Marketplace  Vault /
 economics   facets         facets     (ERC1155)  / attendance  yield /
 & access                  (LomShow)             / pool / etc.   pools
```

- **Facets** swap without migrating user-facing proxy addresses — **controlled upgradeability**
- **Vertical facets** beyond “mint ticket”: yield vaults, pools, insurance, contractor settlement, attendance — **one stack**, not ten vendors

---

# Defensibility (capabilities, not hype)

- **Full vertical integration:** contracts, indexer/analytics direction, and consumer + ops UIs in one program of record
- **Modular protocol:** add or replace **business slices** (facets) as the category evolves
- **Breadth:** ticketing plus **economics, gating, insurance, and settlement-shaped** primitives competitors rarely ship as one system
- **Agent-shaped surface:** protocol exposed to **tools and resources** for LLM-driven workflows (direction: MCP server, generative UI)

---

# What we’re building next *(themes from product roadmap)*

- **Access control / gate program:** validator experiences, rotating credentials, offline-tolerant ops *(per access-control PRD track)*
- **Wallet & rails hardening:** KMS, webhooks, reconciliation — production-grade **fiat → on-chain** paths
- **ECVA / event intelligence:** AI-assisted concept validation, campaigns, analytics *(feature-frozen as of 2026-05-19 — current scaffolding shipped, no further evolution planned)*
- **Compliance toolkit:** jurisdiction-specific exports and audit artifacts *(e.g. regulatory PRD themes — [Founder fill-in: markets])*

_Planned vs shipped: align wording with your fundraising narrative._

---

# Business model *(hypothesis — [Founder fill-in])*

- **Protocol or marketplace fees** on primary / secondary flows
- **SaaS or seat-based** pricing for professional promoters and venues
- **Payments margin** or partnership economics on fiat rails where applicable
- **Enterprise / white-label** organizer deployments *(optional strand — [Founder fill-in])*

_All numbers, splits, and segments: replace with your model._

---

# Traction

**Engineering and integration depth (qualitative — verify before sending):**

- Modular **Diamond** smart-contract system with broad facet coverage and **automated test discipline** (Hardhat / Foundry in contract repo)
- **Stageforge Pro** and **Backstage** wired to the same protocol ABIs and operational patterns
- Active **PRD / coverage program** culture — shipping surfaces with explicit acceptance criteria

**Commercial — [Founder fill-in]:** design partners, pilots, LOIs, GMV, revenue _(do not leave blank in the PDF you send to Sequoia)._

---

# Team & ask

**Team — [Founder fill-in]:** names, roles, relevant operating history (live events, fintech, crypto infra).

**Round — [Founder fill-in]:** stage, amount, instrument.

**Use of funds — [Founder fill-in]:** hires, audits, go-to-market, liquidity, chain deployment.

**Contact — [Founder fill-in]:** email / link.

---

<!-- _class: lead -->

# Thank you

## [Founder fill-in: one-line closing + contact]

_LOMPLAY — programmable events & ticketing infrastructure_
