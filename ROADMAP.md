# Odyssey X — Living Roadmap

> **Last updated: 2026-03-24 (Session 8)**
> Goal-driven, not timeline-driven. Ship MVP when ad generation → launch → monitoring pipeline is bulletproof.

---

## MVP Definition

The MVP is ready when a user can:
1. ✅ Connect Shopify + Meta Ads + Loop
2. ✅ Auto-discover existing offers from Meta ad data
3. ✅ See full funnel flow (impressions → clicks → content views → checkout → purchase)
4. ✅ See subscription LTV/churn per product from Loop
5. ✅ Generate production-quality ads with Gemini AI
6. ✅ Approve ads → auto-push to Meta Ads Manager as new ad set (with Sonnet 4.6 copy)
7. ✅ Competitor intelligence: auto-scrape Ad Library, classify ads, niche analytics
8. ⬜ Agent monitors performance daily, kills losers, scales winners (with approval at user's autonomy level)
9. ⬜ Agent proactively plans next batch based on what's working + competitor data
10. ⬜ Guided onboarding flow

---

## Quick Status

| Area | Status | Notes |
|------|--------|-------|
| Integrations (Shopify/Meta/Loop) | ✅ Done | All 3 connected, syncing data |
| Ad Generation (Gemini AI) | ✅ Working | 10 formats, product composition |
| Ad Copywriter (Sonnet 4.6) | ✅ Working | 5 primary texts + 5 headlines per ad, no em dashes |
| Ad Launch Pipeline | ✅ Done | Approve → push to Meta, New Batch / Add to Batch, CBO detection |
| Offer Discovery + Funnel | ✅ Done | Entry points, funnel flow, thumbnails |
| Subscription Metrics (Loop) | ✅ Done | LTV, churn, MRR per selling plan |
| Three Pillars (CPA/AOV/LTV) | ✅ Live | All 3 showing real data |
| Competitor Intelligence | ✅ Done | 185 ads scraped, classified, permanent storage, weekly auto-refresh |
| Niche Analytics | ✅ Done | Top angles, formats, hooks, winner scoring, per-brand breakdown |
| Security Fixes | ✅ Done | RLS, token headers, encryption |
| Watcher (Kill/Scale) | ⬜ Not Started | Next priority |
| Background Autonomy | 🟡 Partial | Weekly competitor scrape + classify auto-scheduled |
| Onboarding Flow | ⬜ Not Started | After features complete |

---

## What's Done (Sessions 3-8)

### Ad System
- [x] Replaced Pillow renderer with Gemini AI full composition (1200→470 lines)
- [x] Product images sent to Gemini for natural composition
- [x] 10 ad format prompts (problem_callout, testimonial, offer, hero_product, us_vs_them, ingredient_breakdown, advertorial, before_after, native_meme, lifestyle)
- [x] Angle selection guardrails (stay grounded, iterate winners, don't invent crazy angles)
- [x] verify_with_user tool for agent decision points
- [x] Ad naming convention: `ODY-{batch}.{num} | {Angle} | {Persona} | {Awareness} | {Format} | {Hook}`

### Ad Copywriter (Session 8)
- [x] Sonnet 4.6 generates 5 primary text + 5 headline variations per ad
- [x] Tailored to angle, persona, awareness level, and format
- [x] No em dashes (global rule for all customer-facing content)
- [x] Persona-matched language and reading level
- [x] Fallback copy if AI generation fails
- [x] Copy generated at launch time (or during ad creation for new batches)

### Ad Launch Pipeline (Session 7-8)
- [x] Full approve → push to Meta flow
- [x] New Batch (creates new ad set) or Add to Batch (adds to existing ODY ad set)
- [x] CBO detection: auto-detects Campaign Budget Optimization, skips ad set budget
- [x] Destination URL copied from reference ad set
- [x] ODY naming convention enforced for ad sets and ads
- [x] Meta IDs stored back in offer_ad_sets / offer_ads tables
- [x] Decline All button on batch cards
- [x] Launch modal with ad set search + CBO indicator
- [x] TOF/MOF/BOF awareness badges on batch cards
- [x] Expanded review with large images, format badges, copy preview
- [x] Click-to-zoom modal with full ad details and copy variations

### Competitor Intelligence System (Session 8) ✅ COMPLETE
- [x] **Database**: competitor_brands, competitor_ads, competitor_landing_pages, competitor_offers tables
- [x] **Niche selection**: 6 niches (Supplements, Skincare, Fitness, Pet, Baby & Kids, Food & Beverage)
- [x] **Auto-seed**: selecting "Supplements" auto-adds 12 brands (AG1, Seed, Ritual, LMNT, Bloom, Onnit, Momentous, Garden of Life, Ancient Nutrition, Thorne, NOW Foods, Transparent Labs)
- [x] **Ad Library scraper**: E2B + Playwright, scrapes static ads sorted by impressions
- [x] **Permanent image storage**: images downloaded to Supabase Storage (never expire)
- [x] **SECURITY DEFINER RPC**: bypasses RLS for inserts/updates from backend
- [x] **Delta detection**: ads missing from scrape marked as "turned_off" with deactivated_at timestamp
- [x] **Re-activation**: turned_off ads that reappear get status reset to active
- [x] **Winner scoring**: longevity + impression rank + active bonus (0-100 scale)
- [x] **Sonnet classification**: each ad tagged with format (PC/TEST/OFFER/HP/etc), awareness (TOF/MOF/BOF), angle, hook
- [x] **Angle consolidation**: similar angles normalized ("Bloating Relief" → "Gut Health", "Morning Ritual" → "Daily Routine")
- [x] **Active / Turned Off tabs**: UI shows both with badges, kill dates, winner scores
- [x] **Auto-schedule**: pg_cron + pg_net → weekly scrape (Monday 6 AM UTC) + auto-classify (Monday 8 AM UTC)
- [x] **185 ads** scraped across 10 brands, **183 classified** with Sonnet
- [x] **Niche intelligence API**: /niche-intelligence returns aggregated analytics
- [x] **Ad generation context**: get_ad_generation_context() converts analytics into agent prompt

### Niche Analytics (Session 8)
- [x] Top angles by frequency + winner rate (Gut Health #1 with 42 ads)
- [x] Format distribution (which ad types dominate)
- [x] Awareness mix (TOF/MOF/BOF split)
- [x] Top hooks (scroll-stoppers competitors keep running)
- [x] Winning combos (format x awareness pairs)
- [x] Per-brand breakdown (individual strategy profiles)
- [x] Prompt context generator for ad creation agent

### Offers System
- [x] Auto-discover offers from Meta Ads by destination URL
- [x] Group by offer landing page (multiple entry points → one offer)
- [x] Funnel crawl: follow CTAs to map entry point → offer page → cart
- [x] Extract og:image thumbnails from landing pages
- [x] Full funnel flow visualization (impressions → landing_page_views → content_views → checkouts → purchases)
- [x] Entry points section with per-EP spend/ROAS/CPA/ad count
- [x] Active/All toggle + sort by spend/ROAS/CPA/name
- [x] Click-to-expand offer detail page
- [x] Delete offers with cascade cleanup
- [x] Import All / individual import / dismiss on discovery

### Loop Subscriptions Integration
- [x] Full integration: connect via API key, encrypted token storage
- [x] Sync all subscriptions (paginated, rate-limit aware)
- [x] Per-selling-plan metrics: LTV, churn rate, MRR, avg duration, active subs
- [x] Manual product/selling plan linking to offers
- [x] LTV:CPA ratio badge + CPA payback period calculation
- [x] Three Pillars LTV now shows real data from Loop
- [x] Settings page: Connect/Disconnect/Sync Now UI

### Security & Stability
- [x] Removed debug endpoints (exposed API key prefixes)
- [x] Encryption key required (app crashes without it)
- [x] Message delete ownership check
- [x] Token decryption graceful error handling
- [x] All .single() → .maybe_single() (10 callsites)
- [x] Meta tokens moved to Authorization header (all endpoints)
- [x] URL path renamed `/products` → `/items` (ad blocker avoidance)
- [x] Multiple RLS fixes across offers, tasks, integrations
- [x] SECURITY DEFINER functions for competitor data writes (bypass RLS safely)

### Team Page
- [x] Three Pillars showing live CPA (Meta) + AOV (Shopify) + LTV (Loop)
- [x] Per-agent stats (ads generated, tasks completed, etc.)
- [x] Connection status indicators

---

## What's Next — Priority Order

### Priority 1: Competitor Intelligence Dashboard (Standalone Page)
The competitor data is currently buried in Settings. It needs its own dedicated page.

**Ideas for the Competitor Intelligence page:**
- [ ] **Top nav: "Intel" or "Competitors" tab** (alongside Chat, Planner, Team, Offers)
- [ ] **Niche Overview dashboard**: pie charts for format distribution, awareness mix, bar chart for top angles
- [ ] **Brand cards**: each competitor as a card showing their ad count, top angle, format preference, winner count
- [ ] **Click into brand → full Ad Library gallery** with Active/Turned Off tabs (move from Settings)
- [ ] **"What's Working" section**: top 10 ads across all competitors sorted by winner score
- [ ] **"Recently Killed" section**: ads that just got turned off (learn from failures too)
- [ ] **"New This Week" section**: fresh ads from the latest scrape
- [ ] **Angle leaderboard**: ranked list of angles across the niche with ad counts, winner rates, and trend arrows
- [ ] **Hook library**: browsable collection of all detected hooks, filterable by angle/format/awareness
- [ ] **"Use This" button**: click on any competitor ad to use it as inspiration for your next batch
- [ ] **Comparison view**: side-by-side your ads vs competitor ads on the same angle
- [ ] **Export to batch**: select multiple competitor ads as inspiration → auto-create a generation batch

### Priority 2: Wire Competitor Intelligence into Ad Generation
Make the agent actually USE the competitor data when creating ads.

- [ ] **Agent receives niche intelligence context** in system prompt when generating ads
- [ ] **Strategy picker** in batch creation: "Based on my winners" / "Based on competitor winners" / "New angle ideas"
- [ ] **Competitor ad picker**: gallery where user selects specific competitor ads as inspiration
- [ ] **Agent references top hooks and angles** in copywriter prompt
- [ ] **"Variation of competitor" mode**: agent analyzes a specific competitor ad and creates a branded variation
- [ ] **All generated ads trace back** to their strategy source (competitor inspiration, own winner, new idea)

### Priority 3: Watcher Agent (Kill/Scale/Monitor)
Daily monitoring that takes action based on user's autonomy level.

- [ ] **Daily health check job**: scan all active ad sets, pull 7-day metrics
- [ ] **Kill rules**:
  - CPA > 2x target after 1000+ impressions + 48 hours → flag/pause
  - ROAS < 0.5x after 72 hours → flag/pause
  - CTR < 0.5% after 1000 impressions → flag/pause
- [ ] **Scale rules**:
  - ROAS > 2.5x for 72+ hours → suggest 20% budget increase
  - CPA < 0.7x target for 5+ days → suggest duplication to new audience
- [ ] **Autonomy levels**:
  - 🔒 Supervised: creates approval item for everything
  - 🟡 Guided: auto-pause bad ads, asks before scaling
  - 🟢 Autonomous: auto-pause + auto-scale within limits, notifies after
  - ⚡ Full Auto: all actions automatic, daily summary report
- [ ] Autonomy level stored in user profile, changeable from settings or chat
- [ ] Statistical significance: require minimum impressions + time before any action
- [ ] "While you were gone" summary of all Watcher actions

### Priority 4: Background Autonomy
Agent creates its own work without being asked.

- [ ] **Daily health check**: auto-created task every morning
  - Scan all active ads, flag issues, summarize performance
  - Create approval items for kill/scale recommendations
  - Update learnings.md with new performance data
- [ ] **Weekly batch planning**: auto-created task every Monday
  - Analyze which angles/formats/personas are winning
  - Draft next batch strategy (angles, formats, personas, hooks)
  - Create "Ready to Generate" task for user to approve
- [x] **Weekly competitor scan**: pg_cron + pg_net auto-scrapes Ad Library
- [x] **Auto-classify**: new ads classified by Sonnet after each scrape
- [ ] **Diff reports**: "3 new ads from AG1 this week, 2 ads killed, 1 running 6+ weeks (winner)"
- [ ] All background work shows in Planner as task cards

### Priority 5: Agent Intelligence Improvements
Make the agent's creative decisions better over time.

- [ ] Agent reads Meta performance data BEFORE creating ads (already partially done)
- [ ] Agent parses ad names using naming convention to correlate angle/format/hook with CPA
- [ ] Agent asks user to verify decisions at key points (product selection, angle choice)
- [ ] User feedback on generated ads (thumbs up/down) → stored → informs future batches
- [ ] Performance-based prompt: "Last batch's Problem Callout got 1.2x ROAS, try Ingredient Breakdown this time"

### Priority 6: Planner & Approval UX
Make the task management and approval flow smooth.

- [ ] Task cards show inline previews (ad thumbnails, report summaries)
- [ ] "While you were gone" banner with pending approvals count
- [ ] Filter tasks by agent, type, date, offer
- [ ] Real-time task movement via Supabase Realtime
- [ ] Proactive "Ideas" section from agent suggestions

### Priority 7: Onboarding Flow
Build AFTER all features work, BEFORE opening signups.

- [ ] Step 1: Sign up (email + password + brand name + URL)
- [ ] Step 2: The Atomic Unit — explain offers concept
- [ ] Step 3: Connect Shopify (OAuth)
- [ ] Step 4: Connect Meta Ads (OAuth)
- [ ] Step 5: Connect Loop (API key — optional)
- [ ] Step 6: Auto-discover offers from Meta + Shopify
- [ ] Step 7: Set niche + auto-add competitors
- [ ] Step 8: Set autonomy level
- [ ] Step 9: Brand brain generated, first recommendations shown
- [ ] Step 10: "Create your first ad batch?" (with competitor intelligence context)

### Priority 8: Pre-Launch Polish
Build the week before opening signups.

- [ ] Rate limiting (5 req/min chat, 3 sandbox, 60 everything else, exempt SSE)
- [ ] Credit enforcement (atomic check + reserve, env toggle for rollback)
- [ ] Error handling in all frontend stores
- [ ] Mobile responsive pass
- [ ] Loading skeletons for all async operations
- [ ] Empty states for all lists/grids
- [ ] Chat message styling (user bubbles, assistant cards)
- [ ] Glass card/button/input reusable components

---

## Known Bugs & Technical Debt

- [ ] Agent sometimes invents unsupported angles — guardrails partially in place
- [ ] Shopify product fetch may miss products (pagination/status filter) — fixed but unverified
- [ ] Chat doesn't render ad images inline in messages
- [ ] No Supabase Realtime subscriptions (polling only)
- [ ] Some offer endpoints still use global `db` client (potential RLS issues)
- [ ] Ad blocker blocks any URL with `/products` in path — renamed to `/items` but check for other instances
- [ ] Loop sync rate limited at 10 req/sec — need exponential backoff
- [ ] offer_metrics_daily only populated on import, not updated daily from Meta
- [ ] Facebook CDN image URLs in old ads (pre-Storage) have expired — need re-scrape
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads (may not have active static ads)
- [ ] RLS blocks direct writes to competitor_ads — all writes must use SECURITY DEFINER RPCs

---

## Integration Status

| Integration | Auth | Data Pull | Data Push | Status |
|-------------|------|-----------|-----------|--------|
| Shopify | ✅ OAuth | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ OAuth | ✅ Campaigns, ads, metrics, content_views | ✅ Launch batch, create ads | ✅ 90% |
| Loop Subs | ✅ API Key | ✅ Subscriptions, LTV, churn, MRR | N/A | ✅ 95% |
| Meta Ad Library | ✅ Public (Playwright) | ✅ Competitor static ads, copy, images | N/A | ✅ 95% |
| Klaviyo | ⬜ Not started | ⬜ | ⬜ | ⬜ 0% (post-MVP) |

---

## Deployment Status

| Service | Platform | Status | Auto-deploy? |
|---------|----------|--------|-------------|
| Frontend | Cloudflare Pages | ✅ Live (demo.runodyssey.io) | Yes (git push) |
| Backend API | Railway (odyssey-x) | ✅ Live (api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | ✅ Deployed | Yes (git push) |
| Database | Supabase | ✅ Live | Manual migrations |
| Image Storage | Supabase Storage | ✅ Live (competitor-ads bucket) | Auto via scraper |

---

## Cron Jobs

| Job | Schedule | What it does |
|-----|----------|--------------|
| weekly-competitor-ad-scrape | Monday 6 AM UTC | Scrapes Ad Library for all competitor brands |
| weekly-classify-competitor-ads | Monday 8 AM UTC | Classifies new unclassified ads with Sonnet |

---

## Session Log

### 2026-03-20 (Sessions 1-3)
- Built 3-page layout, Team page, Planner page, Offers page
- Fixed Railway deployment (path resolution), Cloudflare builds
- Created ROADMAP.md, HANDOFF.md, dev dashboard

### 2026-03-22 (Session 4)
- Fixed duplicate terminal entries, sandbox hanging, font installation
- Started Pillow renderer improvements (7/10 formats updated)

### 2026-03-23 (Sessions 5-6) — MASSIVE session
- **Ad System**: Replaced Pillow with Gemini AI (full composition, product images, 10 formats)
- **Security**: Debug endpoints removed, .single() fixes, Meta token headers, encryption required, RLS fixes
- **Team Page**: Three Pillars wired to live data (CPA/AOV/LTV)
- **Offers System**: Complete rebuild — auto-discover from Meta, funnel crawl, entry point grouping, og:image thumbnails, active/sort/delete, detail page with funnel flow visualization
- **Loop Integration**: Full build — API key auth, subscription sync, selling plan metrics (LTV/churn/MRR), manual linking to offers, Three Pillars LTV
- **Backend**: ~15 commits of fixes (RLS, route ordering, ad blocker workaround, rate limiting)
- **Naming Conventions**: Updated ad naming to include angle + persona at ad level

### 2026-03-23 (Session 7) — Ad Launch Pipeline
- **Ad Launch Pipeline**: Full end-to-end working. Approve batch → select ad set → push to Meta as PAUSED
- **Sonnet 4.6 Copywriter**: 5 primary text + 5 headline variations per ad at launch
- **CBO Detection**: Auto-detects Campaign Budget Optimization, hides budget field
- **New Batch / Add to Batch**: Toggle in launch modal
- **Planner UI**: Decline All, TOF/MOF/BOF badges, expanded review, click-to-zoom

### 2026-03-24 (Session 8) — Competitor Intelligence System
- **Full competitor scraping pipeline**: E2B + Playwright → Ad Library → Supabase Storage (permanent images)
- **185 ads scraped** across 10 supplement brands (AG1, Seed, Ritual, LMNT, Bloom, Onnit, Momentous, Ancient Nutrition, Thorne, NOW Foods)
- **Sonnet classifier**: each ad tagged with format, awareness, angle, hook using our naming framework
- **Angle consolidation**: normalizes similar angles for clean aggregation
- **Delta detection**: tracks ads turned off between scrapes
- **Winner scoring**: longevity + impression rank + active status
- **Niche intelligence API**: aggregated analytics for agent consumption
- **Active / Turned Off tabs** in competitor Ad Library modal
- **Auto-schedule**: pg_cron weekly scrape (Mon 6 AM) + classify (Mon 8 AM)
- **SECURITY DEFINER RPCs**: bulk_insert_competitor_ads + classify_competitor_ad bypass RLS
- **Settings UI**: niche picker, competitor list management, Ad Library gallery per brand
- **Key learning**: Supabase Python SDK only accepts JWT keys (not sb_secret_ format). All competitor writes use direct HTTP to RPC endpoints.
- **Key learning**: E2B sandbox JS evaluation needs ES5 syntax (no arrow functions, no const/let in some Chromium versions). Separate .js file avoids Python triple-quote conflicts.

---

## Architecture Notes

### The Offer as Atomic Unit
```
OFFER (e.g., "Hashi Aid Special")
 ├── Landing Page URL (www.ibdassist.com/pages/hashi-aid-special)
 ├── Entry Points (3 listicles/advertorials/quizzes feeding into it)
 │    ├── Hashi Listicle V2 ($1787 spend, 0.3x ROAS, 40 ads)
 │    ├── Hashi Listicle V1 ($219 spend, 0.5x ROAS, 20 ads)
 │    └── 7 Day Challenge ($46 spend, 0.0x ROAS, 1 ad)
 ├── Selling Plans (from Loop)
 │    ├── HashiAid 1 Bottle Monthly (381 active, $152 LTV, 22% churn)
 │    └── HashiAid 3 Bottle Monthly (5 active, $152 LTV)
 ├── Ad Spend (from Meta) → CPA
 ├── Orders (from Shopify) → AOV
 ├── Subscriptions (from Loop) → LTV
 └── Funnel Flow: 47K impressions → 861 clicks → 664 content views → 194 checkouts → 40 purchases
```

### Competitor Intelligence Architecture
```
NICHE (e.g., "Supplements")
 ├── 12 Competitor Brands (shared across all users in niche)
 │    ├── AG1: 28 ads (Gut Health, Nutrient Gaps, Daily Routine)
 │    ├── Seed: 21 ads (Gut Health dominant)
 │    ├── Ritual: 27 ads (Retail Availability, Quality)
 │    └── ... 7 more brands
 ├── 185 Total Ads (scraped from Meta Ad Library)
 │    ├── Permanent images in Supabase Storage
 │    ├── Each classified: format + awareness + angle + hook
 │    ├── Winner score: 0-100 (longevity + impressions + active)
 │    └── Status: active / turned_off (with deactivation date)
 ├── Niche Analytics (aggregated)
 │    ├── Top angles: Gut Health (42), Nutrient Gaps (10), Sleep (7)
 │    ├── Top formats: OFFER, HP, IB, TEST, LIFE
 │    ├── Awareness mix: TOF 45%, MOF 30%, BOF 25%
 │    └── Winning combos: OFFER x BOF, HP x TOF, IB x MOF
 └── Auto-refresh: Weekly scrape + classify via pg_cron
```

### Three Pillars
| Pillar | Source | Current Value | What Improves It |
|--------|--------|---------------|-----------------|
| CPA | Meta Ads | $51 | Better ads, angles, targeting |
| AOV | Shopify | $45 | Better offers, bundles, upsells |
| LTV | Loop | $326 | Better retention, email flows, product quality |
