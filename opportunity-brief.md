# Ad Creative Intelligence — Opportunity Brief

## What this document covers

I spent time reverse-engineering Foreplay.co — the leading ad-creative research platform — from the outside. I mapped their product, features, data sources, business numbers, how they built their moat, and where the gaps are. This document is the distilled version: what the product does, what the real numbers look like, and how I think we can build something better by starting focused and growing gradually.

No API specs or code-level details here. This is the business and product case.

---

## The product: what Foreplay actually does

Foreplay is a SaaS tool that helps performance marketers, creative strategists, and agencies research competitor ads, save inspiration, track what's working, analyse their own creative performance, and brief new ads — all in one place.

Think of it as **"Pinterest meets Bloomberg Terminal, but for ads."**

The core workflow is five steps:

1. **Find** — Search a massive library of ads across Meta (Facebook + Instagram), TikTok, and LinkedIn. Filter by format, platform, language, niche, whether the ad is still running, how long it's been running.
2. **Save** — Save ads into organised boards and folders using a Chrome extension (one-click save while browsing ad libraries) or directly from the app. Add tags, notes, ratings.
3. **Track** — Monitor competitor brands 24/7. See when they launch new ads, which ads they keep running (winners), which they kill quickly (losers), how fast they're producing creatives.
4. **Analyse** — Connect your own Meta and TikTok ad accounts to see which of YOUR creatives are performing — spend, CTR, CPA, ROAS, hook rate (do people stop scrolling?), hold rate (do they watch it through?). Compare your performance to competitor activity.
5. **Brief** — Use AI to generate creative briefs based on your saved inspiration, brand profile, and goals. Structured scripts, hooks, shot lists, CTAs — ready to hand to a creator or editor.

Every saved ad gets automatic AI analysis: transcription (for video ads), hook text extraction, emotional tone scoring across 15 dimensions, target persona identification, content categorisation, niche tagging, and funnel stage detection.

---

## The real numbers

### Company

- **Founded:** 2021, Toronto. Started as a simple Chrome extension called Adison.io.
- **Funding:** Bootstrapped — no outside investors. Profitable.
- **Revenue:** Estimated single-digit millions ARR, growing 75%+ year-over-year.
- **Team:** ~11 people. Small and lean.
- **Customers:** Claims include agencies like True Classic, Solawave, HexClad, Ridge Wallet — mostly DTC e-commerce brands and the agencies that serve them.
- **Chrome extension:** 20,000+ users (store listing).
- **G2 rating:** Strong (listed on G2 with positive reviews).

### Product scale

- **Ad corpus:** Marketing claims range from 54.7 million to "200M+" ads — the real number is somewhere in that range, likely 50-100M unique creatives.
- **Ingestion rate:** ~2 million new ads processed per day (from their job posting — this is a publicly stated number).
- **Brands tracked:** 300,000+ brands in the database.
- **Platforms covered:** Meta (Facebook + Instagram), TikTok, LinkedIn. Google/YouTube coverage is limited.

### Pricing (October 2026, annual billing)

| Plan | Monthly price | Key limits |
|---|---|---|
| **Basic** | $49/mo | 1 user, Chrome extension, boards, Discovery search, 2 Spyder brand tracks |
| **Workflow** | $149/mo | 3 users, unlimited boards, 15 Spyder brands, 1 Lens brand, AI briefs, API access |
| **Agency** | $389/mo | Unlimited users, 50 Spyder brands, 10 Lens brands, priority support |

Add-ons: extra Spyder credits, extra Lens brands, API credit top-ups (100k credits for $99/mo up to 500k for $349/mo).

All plans include 10,000 API credits per month (20,000 on annual). 1 credit = 1 ad returned.

### The competitive landscape

| Product | Monthly price | What it does |
|---|---|---|
| Swipekit | $26–66/mo | Low-cost swipe file with API |
| **Foreplay** | $49–389/mo | Full workflow: save + search + track + analyse + brief |
| Minea | $49–199/mo | Dropshipping-focused ad spy |
| BigSpy | Free–$499/mo | Multi-platform ad database (up to 11 platforms) |
| Panoramata | $99–399/mo | Competitor marketing tracking (ads + email + landing pages) |
| Atria | $129–959/mo | AI creative research + own-account analytics |
| AdSpy | $149/mo | Large Facebook/Instagram database |
| MagicBrief | From $249/mo | Creative analytics + briefs |
| Motion | From $999/mo | Creative analytics for high-spend teams |

**Key insight:** The market is split between cheap "ad spy" tools (just search) and expensive analytics platforms. Foreplay sits in the sweet spot — affordable enough for freelancers, powerful enough for agencies. Nobody owns the India/SEA/LatAm market yet.

---

## Feature-by-feature breakdown: what they built and how it works

### 1. Chrome Extension — the entry point

What it does: adds a "Save" button when you're browsing Meta Ad Library, TikTok Creative Center, or LinkedIn Ad Library. One click saves the ad to your Foreplay board.

How it works: the extension reads what's on the page (ad ID, copy, media URLs, dates) and sends it to their backend. The backend then fetches the full ad record, downloads the media (images/videos), and stores it permanently. If two different users save the same ad, it's stored once — they use the media file's fingerprint (SHA-256 hash) for deduplication.

Why it matters: this is the primary data collection mechanism. Every user who saves an ad is contributing to the shared corpus. More users = more ads in Discovery = more value for everyone. Classic network effect.

### 2. Boards and Library — organisation

What it does: boards and nested folders for saving and organising ads. Tags, star ratings, notes, comments. Share boards publicly or with teammates. Bulk actions. ZIP download of saved media.

How it works: straightforward database storage with folder hierarchy. Each saved ad links to the master ad record (so metadata and AI analysis are shared), but your notes and tags are private to your workspace.

Why it matters: this is where stickiness comes from. Once a team has 500+ ads organised across 20 boards with notes and ratings, they're not switching tools.

### 3. Discovery — the search engine

What it does: search across the entire corpus. Filter by platform, format (video, carousel, image), language, niche, market (B2B/B2C), whether the ad is live, how long it's been running, date range. Sort by newest, oldest, longest running, or most relevant.

How it works: they use Elasticsearch with semantic and vector search (confirmed from their job posting). This means you can search not just by keywords but by meaning — "ads about teeth whitening that use before/after" actually works. Every ad has AI-generated tags, transcripts, and embeddings that power this.

Why it matters: Discovery is the top-of-funnel. Free users can search but need a paid plan to save. The quality of search results is what converts browsers to payers.

### 4. Spyder — competitor tracking

What it does: add a competitor brand and Foreplay monitors their ad activity 24/7. See every new ad they launch, which ones they keep running (winners), which they kill. Velocity charts show how fast they're producing. Rankings show their top performers by longevity.

How it works: a scheduler regularly queries the Meta Ad Library for each tracked brand's Facebook Page ID, compares against the last snapshot, and marks ads as new, running, or stopped. Ads that keep running for weeks get flagged as "evergreen" winners. Ads that disappear quickly are "breakout" tests.

The categories:
- **Breakout**: new ads, recently launched
- **Evergreen**: ads running for a long time (proven winners)
- **Peak**: ads at their highest activity
- **Trend**: ads showing momentum

Coverage is **Meta only** for tracking. This is a significant limitation — no TikTok or LinkedIn tracking.

Credits limit: 15 brands on Workflow ($149/mo), 50 on Agency ($389/mo). Extra brands are paid add-ons.

### 5. Lens — your own creative analytics

What it does: connect your own Meta or TikTok ad account (via OAuth) and see performance data at the creative level. Spend, impressions, CTR, CPA, ROAS, plus custom metrics: **hook rate** (3-second views / impressions — do people stop scrolling?) and **hold rate** (completed views / 3-second views — do they keep watching?).

How it works: they pull data from the Meta Marketing API and TikTok Business API using the customer's own authorised tokens. Data is synced nightly and on-demand, stored in ClickHouse (a columnar analytics database), and joined to creative media. They partner with Triple Whale / Orcabase for the Meta account connection.

This data is **private per workspace** — your performance data never enters the public Discovery corpus.

Credits limit: 1 Lens brand on Workflow, 10 on Agency.

### 6. AI Features

What it does across the product:
- **Automatic transcription** of every video ad (speech-to-text)
- **AI analysis** on every saved ad: hook text, emotional drivers (15 scores like urgency, fear, curiosity, social proof), target persona, content type (testimonial, UGC, product demo, etc.), funnel stage, niche
- **AI briefs**: select inspiration ads + set your brand profile and goals → get structured scripts with hooks, angles, shot lists, CTAs
- **Brief variations**: generate multiple versions from one brief
- **Brand descriptions**: AI-generated summaries from a brand's ad portfolio

How it works: server-side pipeline. Audio extracted → speech-to-text → LLM processes transcript + ad metadata → structured tags and scores. Done once per unique creative and reused for all users who save it. Briefs use prompt templates with the brand profile and selected ads as context.

### 7. AI-Agent Integration (MCP + API)

What it does: lets AI assistants (Claude, ChatGPT, Cursor, Perplexity) search ads, pull transcripts, analyse brands — all through natural language. Also a traditional REST API with keys for developers building their own tools.

Launched June 2026. This is a significant forward-looking move — it positions Foreplay as infrastructure, not just a UI.

### 8. Other features

- **Experts directory**: vetted freelancers and agencies listed on Foreplay (lead generation for agencies)
- **Agency listings**: AI-assisted profile creation for agencies
- **Briefs status board**: track briefs through Draft → Review → Approved → In Production
- **Launchpad**: onboarding checklist for new users
- **Team workspaces**: invites, roles, shared boards
- **Public share links**: share boards and ads without requiring a Foreplay account
- **Referral program**: users earn PayPal payouts for referrals
- **Embed system**: embed saved ads on external sites

---

## Where Foreplay is weak

After studying the product deeply, these are the real gaps:

### 1. Platform coverage is narrow
Spyder (competitor tracking) is **Meta only**. No TikTok tracking, no LinkedIn tracking, no Google/YouTube tracking. For agencies running multi-platform campaigns, this is a blind spot.

### 2. No regional focus
The product is US/English-centric. No regional pricing for India, Southeast Asia, or Latin America — markets where digital ad spend is growing fastest. No native-language search (Hindi, Arabic, Bahasa, Portuguese, Spanish). An agency in Mumbai or Jakarta pays the same $149/mo as one in New York.

### 3. Data coverage gaps outside the EU
Thanks to the EU Digital Services Act, official APIs exist for EU-delivered ads. But for US/global commercial ads, Foreplay depends on their proprietary scraping + user saves. The exact method of their large-scale data collection is unclear, and Meta's 2025 terms changes make it legally riskier.

### 4. Performance data requires Triple Whale
Lens (creative analytics) works through Triple Whale / Orcabase as a middleman for Meta account connections. This adds a dependency, and Triple Whale has its own pricing and terms.

### 5. Outdated frontend technology
Vue 2 (end of life December 2023), Firebase SDK v8 (from 2021). The 7 MB initial JavaScript load on the Discovery page is slow on mobile. This matters less to users directly but signals technical debt that slows their feature velocity.

### 6. Google/YouTube blind spot
Google Ads Transparency Center has a free public BigQuery dataset with every EEA advertiser's creative stats, impression bands, and targeting flags since March 2023. Foreplay doesn't surface any of this. It's free data that nobody in the market is using well.

### 7. No production bridge
The workflow stops at briefs. There's no connection to where the creative actually gets made — no Figma/Canva integration, no shot-list-to-creator handoff, no asset management. The brief sits in Foreplay and the production happens elsewhere.

---

## How I'd build this — a gradual approach

I don't think the right move is to clone Foreplay feature-for-feature. That's 14 months of building before reaching parity against a team that has a 5-year head start and millions of saved ads.

Instead, I'd pick a wedge and win it, then expand.

### Phase 1: MVP (months 0–4) — "Save, search, and track — done right"

**What to build:**
- Auth, team workspaces, roles and invites
- Chrome extension (save from Meta Ad Library, TikTok Creative Center, LinkedIn Ad Library)
- Boards, folders, tags, ratings, notes, share links
- Media pipeline: download → permanent storage → poster frames → transcript → AI tags
- Search v1: search over our own saved ads + opt-in community contributions + **official Meta Ad Library API data** (EU/UK ads — this is a legitimate, free data source)
- Basic brand tracking via scheduled Ad Library API queries
- Billing with Stripe, 7-day trial, credit-based metering

**What NOT to build yet:** Lens (creative analytics), briefs, MCP/API, agency marketplace.

**Data strategy from day one:**
- EU/UK ads from official APIs (Meta, TikTok Commercial Content API, Google Transparency Center BigQuery dataset, Snapchat Ads Gallery API)
- User-initiated saves via the extension (never automated scraping)
- Uploads from agencies who want their own ads in the system
- Every ad source clearly labelled

**Differentiation in the MVP:**
- Cross-platform from day one (Meta + TikTok + LinkedIn + Google/YouTube via Transparency Center)
- Regional pricing for India/SEA/LatAm ($15–30/mo entry tier)
- Native language search (Hindi, Arabic, Bahasa at minimum)
- Clean, fast modern UI (React/Next.js, not a 7 MB Vue 2 bundle)

**Target:** 50 private beta users by week 12, public launch by week 16.

### Phase 2: Intelligence (months 5–8) — "Track and analyse"

**What to add:**
- Advanced competitor tracking: alerts on new ads, scaling detection, velocity charts, longevity rankings, creative variant grouping
- Creative analytics (Lens-equivalent): OAuth to Meta Marketing API and TikTok Business API for the customer's own accounts. Creative-level dashboards — spend, CTR, CPA, ROAS, hook rate, hold rate
- AI briefs: brand profiles + saved inspiration → structured scripts, hooks, shot lists
- Public REST API with API keys, usage metering, and developer docs
- Mobile share-sheet saving (save ads from any app on your phone)

**Target:** 500+ paying users, $50k+ MRR.

### Phase 3: AI-native workflow (months 9–14) — "The creative operating system"

**What to add:**
- MCP server with OAuth (works with Claude, ChatGPT, Cursor, Codex)
- Semantic and multimodal search ("find ads that look like this", "find ads with a similar hook")
- Google Ads Transparency Center deep integration (cross-network advertiser activity)
- Production bridge: brief → storyboard → shot list → creator handoff (marketplace or integrations with Figma, Frame.io, Google Drive)
- Agency/experts marketplace
- Enterprise features: SSO/SAML, audit logs, EU data residency

**Target:** 2,000+ paying users, break into the agency segment.

---

## The economics

### Infrastructure costs (estimated from real vendor pricing, October 2026)

| Scale | Monthly infra cost | Revenue at $99 ARPU | Gross margin |
|---|---|---|---|
| 100 users | ~$550–950 | $9,900 | 90–94% |
| 1,000 users | ~$3,200–5,200 | $99,000 | 94–97% |
| 10,000 users | ~$22,000–32,000 | $990,000 | 96–97% |

The biggest variable costs are AI processing (transcription + tagging per creative), search infrastructure, and media storage. All of these benefit from deduplication — if 100 users save the same viral ad, it's processed and stored once.

Media storage stays cheap because Cloudflare R2 has zero egress fees. Doing the same on AWS S3 or Google Cloud Storage would add $0.08–0.12 per GB of egress, which matters at scale.

### Team needed

| Phase | Core team |
|---|---|
| MVP (0–4 months) | 1 tech lead, 1 full-stack dev, 1 data/search engineer, 0.5 ML contractor, 0.5 designer, 1 PM/founder |
| v2 (5–8 months) | Add 1 frontend dev, promote ML to full-time, add 1 growth/content person |
| v3 (9–14 months) | Add 1 more data engineer, 1 more frontend dev, 1 support person |

With AI coding assistants, a strong founding team of 2–3 technical people can realistically ship the MVP. The data/search engineer is the hardest hire — this person builds the ingestion pipeline, search index, and media processing that make the product work.

---

## Risks and how to manage them

| Risk | How real is it | What to do about it |
|---|---|---|
| **Platform terms / access revocation** (Meta, TikTok could restrict API access) | Medium-high | Use only official APIs and user-initiated saves. Never scrape. Multi-source design so no single platform is fatal. Legal review of each data source before launch. |
| **Data coverage gaps** (non-EU commercial ads aren't in official APIs) | High | Be transparent about coverage regions. Position on EU/UK + user-contributed + Google Transparency Center. Coverage grows with users. |
| **Copyright in stored creatives** | Medium | Store for analysis/reference. Honour takedown requests. Don't bulk-redistribute media via the API. Show ads in context with source links. |
| **Foreplay and others respond competitively** | High | Don't compete on corpus size (they win). Compete on coverage breadth (cross-platform), regional focus (India/SEA/LatAm pricing and languages), and AI depth (better tagging, better briefs, agent-first). |
| **Meta Marketing API review delays** (needed for Lens-equivalent) | High | Start the review process in month 2. Launch analytics features later. Offer CSV import as fallback. |
| **Chrome Web Store policy rejection** | Medium | Minimal permissions, clear single purpose, privacy policy, no remote code execution. Follow the rules. |
| **AI costs scaling with saves** | Medium | Deduplicate: process once per unique creative. Use the cheapest model that meets quality bar for bulk tagging. Reserve expensive models for briefs and complex analysis. |

---

## Where we can win that Foreplay can't easily follow

1. **Cross-network from day one.** Meta + TikTok + LinkedIn + Google/YouTube in one search, with clear source badges. Foreplay's tracking is Meta-only and their architecture makes adding platforms slow.

2. **Regional pricing and languages.** India at $15–20/mo with Hindi search is a market Foreplay won't chase until it's big enough to matter — and by then we own it.

3. **EU reach intelligence.** The DSA gives us reach estimates, targeting summaries, and delivery dates for EU ads. Most swipe tools ignore this data entirely. We can rank ads by estimated reach, not just longevity.

4. **Agent-first design.** MCP, API, and deep links on every object from v2. Make the product as useful inside Claude/ChatGPT as it is in the browser.

5. **Production bridge.** Brief → storyboard → script → asset checklist → handoff to creators. Close the gap between "I found inspiration" and "the ad is made."

6. **Privacy-first trust.** No public source maps, minimal third-party trackers, EU hosting option, published sub-processor list. This matters to agencies handling client data and to enterprise sales.

---

## Bottom line

The ad creative intelligence market is real, growing, and has room for a well-positioned entrant. Foreplay proved the model works — bootstrapped to millions in ARR with an 11-person team. Their weaknesses are structural: narrow platform coverage, no regional focus, aging technology, and a data acquisition strategy that faces increasing legal pressure.

I don't think we should try to out-Foreplay Foreplay. I think we should build the version that works for the markets and workflows they're ignoring — cross-platform, multilingual, regionally priced, AI-native — and grow from there.

The infrastructure costs are low. The margins are high. The technology is well-understood. The question isn't whether this can be built — it's whether we can acquire users fast enough to build the data flywheel before incumbents close the gaps.

I believe we can, starting with the wedge markets where nobody is competing today.
