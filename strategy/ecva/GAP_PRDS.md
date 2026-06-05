# Gap PRD Proposals — From Sentiment Analysis

**Generated:** 2026-04-02  
**Context:** 10 pain points identified from LinkedIn Tuboleta thread; 5 require new PRDs to close gaps

---

## PRD-024: ECVA Formalization — AI-Powered Event Intelligence Platform

> **STATUS: CLOSED — feature-frozen 2026-05-19.** PRDs 024a/b/c/d and 028 will not be executed.
> `src/ecva/` scaffolding remains in the codebase as-is. Preserved for historical gap-analysis reference.

**Target repo:** `stageforge-pro`  
**Primary LLM:** Claude Code (UI/wizard), Cursor (Edge Functions/ML pipeline)  
**Gap addressed:** P3 (Monopoly), P7 (Bots/scalpers), P8 (Regulatory vacuum)  
**Priority:** Tier 1 — highest leverage for go-to-market

### Problem Statement

Small and mid-size organizers cannot compete with incumbents (Tuboleta, Ticketmaster) because they lack access to the marketing intelligence, audience analytics, and content generation infrastructure that monopolistic platforms accumulate through their dominant market position. This creates a self-reinforcing cycle: the platform with the most data attracts the most organizers, which generates more data, which makes marketing more efficient, which attracts more consumers, deepening lock-in.

ECVA breaks this cycle by giving **any organizer on LOMPLAY** access to AI-powered marketing intelligence that is **10x more cost-effective** than traditional campaign planning.

### Scope

Formalize the existing ECVA subsystem (`src/ecva/`) as a PRD-governed feature with the following phases:

**Phase 1 — Concept Validation Engine (MVP)**
- Multimodal intake: text, image, audio, video describing event concept
- AI concept modeling: transform raw input into structured event proposal (genre, target audience, venue requirements, pricing hypothesis)
- Market signal scraping: trending artists, competing events, local demand indicators
- Output: Viability score + risk assessment + recommended next steps

**Phase 2 — Programmatic Content Generation**
- Image generation: event posters, social media assets, story cards using licensed artist image/likeness
- Audio generation: teaser clips, radio spots, podcast ad scripts
- Video generation: promotional reels, TikTok-format clips
- All content generated from rights the organizer controls (artist agreements, venue photos, brand assets)
- Output: Multi-platform campaign kit (Instagram, TikTok, X, Facebook, YouTube)

**Phase 3 — Test Campaign Deployment & Signal Collection**
- Automated micro-campaign deployment across social platforms (Meta Ads API, TikTok Ads API)
- Real-time engagement signal collection: impressions, clicks, saves, shares, comments
- Behavior batching: aggregate signals into engagement velocity curves
- Budget optimization: reallocate spend toward highest-performing content/audience segments in real-time

**Phase 4 — Purchase Intent Prediction & ROI Estimation**
- ML model: correlate engagement signals with historical ticket purchase data
- Predict: expected ticket sales by tier, by date, by audience segment
- Estimate: ROI range (pessimistic/expected/optimistic) given budget and market conditions
- Output: **Deterministic event proposal** — "If you invest $X in marketing and price tiers at $Y/$Z, expect N tickets sold with M% margin"

**Phase 5 — Closed-Loop Learning**
- After event execution, compare predictions with actual outcomes
- Feed actuals back into ML model for continuous improvement
- Build LOMPLAY's proprietary dataset: the more events run on the protocol, the better predictions become
- This creates a **network effect** — LOMPLAY's intelligence improves with scale, eventually surpassing incumbent data advantages

### Why This Is the Anti-Monopoly Weapon

Traditional marketing requires:
- Audience research ($5K-$50K via agencies or internal teams)
- Creative production ($2K-$20K per campaign)
- Media buying expertise (specialized staff or agency retainer)
- Performance analytics (custom dashboards, data engineering)

Total: **$10K-$100K+ per event** — prohibitive for small organizers.

ECVA collapses this to:
- Upload your concept (text/audio/video) → AI generates structured proposal
- AI generates all creative assets from your licensed rights
- AI deploys test campaigns with micro-budget ($50-$500)
- AI predicts ROI before you commit full budget
- Total: **$50-$500 per concept validation** — 100x cheaper

This means a local Colombian organizer doing cumbia nights at a 200-person venue gets the **same marketing intelligence quality** as Live Nation planning a stadium tour. That's how you break the cycle.

### Existing Infrastructure (from `src/ecva/`)

| Component | Status | Path |
|-----------|--------|------|
| Architecture docs | Complete | `src/ecva/docs/ARCHITECTURE.md` |
| ML model spec | Complete | `src/ecva/docs/ML_MODEL_SPEC.md` |
| Action plan | Complete | `src/ecva/docs/ACTION_PLAN.md` |
| Compliance guidelines | Complete | `src/ecva/docs/COMPLIANCE.md` |
| API reference | Complete | `src/ecva/docs/API_REFERENCE.md` |
| TypeScript types | Complete | `src/ecva/types/` (6 files) |
| Service stubs | Partial | `src/ecva/services/` (7 modules) |
| React components | Partial | `src/ecva/components/` (6 components) |
| Wizard pages | Partial | `src/app/(app)/ecva/wizard/` |
| DB migrations | Partial | 3 Supabase migrations |
| Edge Functions | Partial | 4 functions |
| Multimodal intake | In Progress | `src/ecva/services/intake/` |

### Image & Audio Generation — Technical Feasibility

**Image generation for event marketing is now trivially accessible:**
- OpenAI DALL-E 3 / GPT-4o image: $0.04-$0.08 per image, photorealistic quality
- Stability AI SDXL: self-hosted option, $0 marginal cost after GPU provisioning
- Flux.1: open-source, fine-tunable on artist likeness with LoRA adapters
- **Key capability:** Generate event posters, social cards, and stories that incorporate the artist's image/likeness *without a photoshoot* — using reference images from the organizer's licensed media

**Audio generation:**
- ElevenLabs: voice cloning for radio spots ($0.30/min)
- Suno AI: music generation for teaser clips
- OpenAI TTS: narrated promotional content ($0.015/1K chars)

**Video generation:**
- Runway Gen-3 Alpha: text-to-video for promotional reels ($0.05/sec)
- Kling AI: high-quality video generation
- Luma Dream Machine: accessible video generation

**The ease-of-use equation:** An organizer with a phone camera, 3 photos of the artist, and a 30-second voice description of their event concept can generate a **complete multi-platform marketing campaign** in under 10 minutes. No design skills. No agency. No budget beyond the AI inference cost.

### Connection to On-Chain Data

ECVA becomes exponentially more powerful when connected to LOMPLAY's on-chain data:

1. **Historical sales data** from `PoolContributionsFacet` → train ML models on actual purchase behavior
2. **Tier pricing data** from `LomShowFacet` → understand price elasticity by genre/venue/audience
3. **Marketplace activity** from `MarketplaceFacet` → detect secondary demand as leading indicator
4. **Settlement data** from `LibPoolOperations` → understand true margin by event type
5. **Contractor costs** from `ContractorSettlementFacet` → benchmark production costs
6. **Gas sponsorship data** from `RegistryGasSponsorshipFacet` → optimize when to subsidize vs. charge

No incumbent ticketing platform exposes this data to organizers. LOMPLAY can.

### Acceptance Criteria

- [ ] AC-1: Organizer can upload multimodal concept (text + images + optional audio/video)
- [ ] AC-2: AI generates structured event proposal with viability score
- [ ] AC-3: AI generates minimum 5 image variants for social media (poster, story, feed post, banner, thumbnail)
- [ ] AC-4: AI generates minimum 2 audio assets (teaser clip, narrated promo)
- [ ] AC-5: Organizer can deploy micro test campaign to at least 2 platforms (Meta + TikTok)
- [ ] AC-6: System collects engagement signals and displays real-time analytics dashboard
- [ ] AC-7: ML model produces ROI estimate with confidence interval
- [ ] AC-8: On-chain historical data feeds into prediction model
- [ ] AC-9: All generated content respects artist likeness rights and compliance guidelines

---

## PRD-025: Fiat On-Ramp & Local Payment Integration

**Target repo:** `stageforge-pro`  
**Primary LLM:** Cursor (payment integration), Claude Code (UI)  
**Gap addressed:** P1 (indirectly — makes protocol accessible to Colombian consumers), P3 (removes crypto barrier)  
**Priority:** Tier 1 — table-stakes for Colombian market

### Problem Statement

Colombian consumers buy tickets in COP via PSE, credit cards, Nequi, or Daviplata. LOMPLAY operates in stablecoin (USDC/USDT). Without a seamless COP → USDC bridge, the protocol is inaccessible to 99%+ of the target market.

### Scope

1. **PSE integration** via local partner (e.g., Wompi, Mercado Pago, Bold) — COP in, USDC minted to buyer's smart wallet
2. **Nequi/Daviplata** integration — mobile payment → stablecoin
3. **Credit card** via Stripe or local acquirer with automatic stablecoin conversion
4. **Reverse flow:** stablecoin → COP for organizer cash-out via bank transfer
5. **Price display:** show COP prices to consumers, convert to stablecoin at purchase time (consumer never sees crypto)
6. **Account Kit abstraction:** consumer gets an embedded smart wallet without knowing it's a wallet

### Acceptance Criteria

- [ ] AC-1: Consumer can buy a ticket with COP (PSE, card, or Nequi) without interacting with crypto
- [ ] AC-2: Ticket appears in their account; ERC-1155 is minted behind the scenes
- [ ] AC-3: Organizer can cash out to a Colombian bank account in COP
- [ ] AC-4: Exchange rate is displayed at purchase time with clear breakdown
- [ ] AC-5: No wallet setup required for consumer (embedded via Account Kit)

---

## PRD-026: Regulatory Compliance Toolkit (Ley 1493)

**Target repo:** `hardhat_contracts` (smart contract compliance) + `backstage` (admin tooling) + `stageforge-pro` (organizer-facing)  
**Primary LLM:** Cursor (contracts/compliance logic), Claude Code (UI), Copilot (docs/tests)  
**Gap addressed:** P8 (Regulatory vacuum), P3 (Monopoly — by becoming a licensed alternative)  
**Priority:** Tier 1 — legal requirement for Colombian market

### Problem Statement

Colombia's Ley 1493 de 2011 (Ley de Espectaculos Publicos) requires that event ticket revenue be custodied by a licensed third party — not the organizer directly. This is the structural reason Tuboleta exists. LOMPLAY's smart contract escrow functionally satisfies the custodian role, but needs a legal wrapper.

Additionally, the law mandates:
- PULEP (Planilla Unica de Liquidacion y Pago) — tax reporting per event
- Contribucion Parafiscal Cultural — parafiscal contribution on ticket sales
- Event permits and municipal registration

### Scope

1. **On-chain PULEP generation** — auto-generate tax reporting data from `PoolContributionsFacet` records
2. **Parafiscal contribution calculation** — add facet or library that computes and withholds the cultural contribution
3. **Audit export** — Backstage feature to export on-chain records in SIC/DIAN-compatible format
4. **Licensed custodian partnership** — legal entity that wraps LOMPLAY's smart contract escrow under Colombian law
5. **Consumer protection automation** — if event is cancelled, automatic full refund (including service fee equivalent) triggered by admin action in Backstage
6. **SIC complaint template generation** — consumer-facing tool that auto-generates complaint documents from purchase data (inspired by Laura Navarro's "Claude in 2 minutes" comment)

### Why Technology Solves the Regulatory Gap

The LinkedIn thread showed that SIC complaints take 2+ years and are often ineffective. LOMPLAY can:
- **Make the complaint unnecessary** by automating refunds on cancellation
- **Make the complaint trivial** when needed, by generating pre-filled documents from on-chain data
- **Make compliance verifiable** by providing regulators with real-time on-chain audit trails instead of opaque spreadsheets

### Acceptance Criteria

- [ ] AC-1: Backstage can export PULEP-compatible tax data per event
- [ ] AC-2: Parafiscal cultural contribution is computed and displayed in organizer dashboard
- [ ] AC-3: Event cancellation triggers automatic full refund flow (admin-initiated, no consumer action required)
- [ ] AC-4: Consumer can generate SIC complaint template from their purchase history
- [ ] AC-5: Audit trail is exportable in format acceptable to Colombian regulatory bodies

---

## PRD-027: Consumer UX Abstraction Layer

**Target repo:** `stageforge-pro`  
**Primary LLM:** Claude Code (UI), Cursor (Account Kit integration)  
**Gap addressed:** P4 (Customer service), P6 (Ticket delivery), P3 (Adoption barrier)  
**Priority:** Tier 1 — critical for consumer adoption

### Problem Statement

Blockchain ticketing has a UX problem. Consumers don't understand wallets, gas fees, or NFTs — nor should they have to. The protocol must be **invisible**. A consumer should experience: browse events → select tickets → pay (COP) → receive confirmation → show QR at door. Zero blockchain vocabulary.

### Scope

1. **Zero-wallet onboarding** — Account Kit embedded wallet created silently during purchase flow
2. **QR-based entry** — consumer shows QR code at venue; scanner verifies ERC-1155 ownership on-chain
3. **Purchase confirmation** — email + SMS + app notification with event details (no wallet addresses, no tx hashes)
4. **"My Tickets" view** — simple card UI showing events, dates, venues — not NFT collection
5. **Transfer flow** — "Send ticket to friend" via phone number or email, not wallet address
6. **Refund flow** — one-button refund request; money returns to original payment method (COP)
7. **Support chat** — AI-powered support that reads on-chain state to resolve issues instantly (no "please DM us your purchase data")

### Why This Turns Tuboleta's Weakness Into LOMPLAY's Strength

The #4 and #5 pain points from the thread (customer service failures, delayed refunds) are **inherently solvable** when the system knows the truth. Tuboleta's customer service fails because:
- Agents can't find purchases in fragmented systems
- Refund approval requires manual escalation through multiple departments
- Consumer must prove they purchased (no self-evident ownership)

LOMPLAY's on-chain architecture means:
- The system **knows** you own the ticket (ERC-1155 balance check)
- The system **knows** the event was cancelled (on-chain state)
- The system **can execute** the refund without human intervention
- The AI support chat can answer "Where's my ticket?" by reading the blockchain in real-time

### Acceptance Criteria

- [ ] AC-1: Consumer completes purchase without seeing any blockchain terminology
- [ ] AC-2: Consumer receives email/SMS confirmation identical to traditional ticketing
- [ ] AC-3: Consumer can show QR at venue for entry verification
- [ ] AC-4: Consumer can transfer ticket to another person via phone number
- [ ] AC-5: Consumer can request refund with one button; money returns in COP within 48 hours
- [ ] AC-6: AI support chat resolves "Where's my ticket?" queries by reading on-chain state

---

## PRD-028: Competitive Intelligence & Market Disruption Engine

**Target repo:** `stageforge-pro` (ECVA extension) + `backstage` (admin analytics)  
**Primary LLM:** Claude Code (UI/analytics), Cursor (data pipeline)  
**Gap addressed:** P3 (Monopoly), P7 (Bots/scalpers), P8 (Regulatory vacuum)  
**Priority:** Tier 2 — builds on PRD-024 (ECVA)

### Problem Statement

Incumbent platforms (Tuboleta, Ticketmaster) accumulate competitive advantages through data asymmetry: they know what events sell, at what prices, in what markets, with what demographics — and organizers don't. This information asymmetry is the **real moat**, not the technology platform itself.

LOMPLAY can invert this by making market intelligence a public good within the protocol.

### Scope

1. **Aggregate on-chain analytics** — anonymized, protocol-wide event performance data
   - Average ticket sales velocity by genre/city/venue size
   - Price elasticity curves by event type
   - Seasonal demand patterns
   - Contractor cost benchmarks
2. **Competitive event monitoring** — scrape public event listings (Tuboleta, eTicket, Eventbrite, social media) to map the competitive landscape
3. **Demand forecasting** — predict untapped demand by correlating social media trends, streaming data (Spotify, YouTube), and search volume with event supply
4. **Organizer benchmarking** — anonymous percentile ranking vs. similar organizers (revenue, margin, fill rate, customer satisfaction)
5. **Scalper detection** — on-chain pattern analysis to identify bulk purchasing and resale manipulation
6. **Regulatory reporting** — auto-generated market reports that could be submitted to SIC to demonstrate anticompetitive practices by incumbents

### The Flywheel

```
More organizers on LOMPLAY
    → More on-chain event data
    → Better ECVA predictions
    → Higher organizer ROI
    → More organizers attracted
    → Deeper data pool
    → Even better predictions
    → LOMPLAY intelligence surpasses incumbents
```

This is how you break a monopoly with technology: not by out-spending the incumbent on venues and exclusivity deals, but by making their **data advantage obsolete** through a superior intelligence layer.

### Acceptance Criteria

- [ ] AC-1: Organizer dashboard shows anonymized protocol-wide benchmarks
- [ ] AC-2: Competitive event calendar shows upcoming events from other platforms (scraped)
- [ ] AC-3: Demand forecasting module suggests untapped event concepts for organizer's market
- [ ] AC-4: Scalper detection flags suspicious purchasing patterns in Backstage
- [ ] AC-5: Quarterly market report auto-generated for regulatory submission

---

## Summary: PRD Gap Coverage

| PRD | Title | Pain Points Addressed | Priority |
|-----|-------|----------------------|----------|
| 024 | ECVA Formalization | P3, P7, P8 | Tier 1 |
| 025 | Fiat On-Ramp | P1 (indirectly), P3 | Tier 1 |
| 026 | Regulatory Compliance Toolkit | P3, P8 | Tier 1 |
| 027 | Consumer UX Abstraction | P3, P4, P6 | Tier 1 |
| 028 | Competitive Intelligence Engine | P3, P7, P8 | Tier 2 |

**After implementation of all 5 PRDs:** All 10 pain points are fully addressed by technology. P3 (Monopoly) — the hardest problem — is attacked from **4 angles simultaneously** (ECVA marketing intelligence, fiat accessibility, regulatory compliance as competitive advantage, competitive intelligence engine).
