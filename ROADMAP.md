# Odyssey X — Living Roadmap

> **Last updated: 2026-03-23 (Session 7)**
> Goal-driven, not timeline-driven. Ship MVP when ad generation → launch → monitoring pipeline is bulletproof.

---

## MVP Definition

The MVP is ready when a user can:
1. ✅ Connect Shopify + Meta Ads + Loop
2. ✅ Auto-discover existing offers from Meta ad data
3. ✅ See full funnel flow (impressions → clicks → content views → checkout → purchase)
4. ✅ See subscription LTV/churn per product from Loop
5. 🟡 Generate production-quality ads with Gemini AI
6. ✅ Approve ads → auto-push to Meta Ads Manager as new ad set
7. ⬜ Agent monitors performance daily, kills losers, scales winners (with approval at user's autonomy level)
8. ⬜ Agent proactively plans next batch based on what's working
9. ⬜ Guided onboarding flow

---

## Quick Status

| Area | Status | Notes |
|------|--------|-------|
| Integrations (Shopify/Meta/Loop) | ✅ Done | All 3 connected, syncing data |
| Ad Generation (Gemini AI) | ✅ Working | Needs prompt refinement |
| Offer Discovery + Funnel | ✅ Done | Entry points, funnel flow, thumbnails |
| Subscription Metrics (Loop) | ✅ Done | LTV, churn, MRR per selling plan |
| Three Pillars (CPA/AOV/LTV) | ✅ Live | All 3 showing real data |
| Security Fixes | ✅ Done | RLS, token headers, encryption |
| Ad Launch to Meta | ✅ Done | Full pipeline: approve batch → launch to Meta with ODY naming |
| Watcher (Kill/Scale) | ⬜ Not Started | Next priority |
| Background Autonomy | ⬜ Not Started | Daily health checks, weekly planning |
| Onboarding Flow | ⬜ Not Started | After features complete |

---

## What's Done (Sessions 3-6)

### Ad System
- [x] Replaced Pillow renderer with Gemini AI full composition (1200→470 lines)
- [x] Product images sent to Gemini for natural composition
- [x] 10 ad format prompts (problem_callout, testimonial, offer, hero_product, us_vs_them, ingredient_breakdown, advertorial, before_after, native_meme, lifestyle)
- [x] Angle selection guardrails (stay grounded, iterate winners, don't invent crazy angles)
- [x] verify_with_user tool for agent decision points
- [x] Ad naming convention: `ODY-{batch}.{num} | {Angle} | {Persona} | {Awareness} | {Format} | {Hook}`

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

### Loop Subscriptions Integration (NEW)
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

### Team Page
- [x] Three Pillars showing live CPA (Meta) + AOV (Shopify) + LTV (Loop)
- [x] Per-agent stats (ads generated, tasks completed, etc.)
- [x] Connection status indicators

---

## What's Next — Priority Order

### Priority 1: Ad Launch Pipeline (Agent → Meta) ✅ DONE
When user approves a batch, agent auto-pushes to Meta Ads Manager.

- [x] **Approval → auto-launch flow**: approve batch → creates ad set in existing winning campaign → uploads images → creates ads (PAUSED)
- [x] Wire Planner approval cards to backend approve/reject endpoints
- [x] On approve: call `/api/integrations/meta/launch-batch` with approved asset IDs
- [x] User selects reference ad set to copy targeting/pixel from (launch modal with search)
- [x] Naming convention enforced: ad set and ad names follow ODY format exactly
- [x] Status updates: draft → approved → published (with Meta ad IDs stored in offer_ads)
- [x] Error handling: if Meta API fails, show error, don't lose the creative (failed assets tracked separately)
- [x] Batch operations: approve all, reject all, launch batch from Cockpit
- [x] Offer detail page shows published ads with Meta links

### Priority 2: Watcher Agent (Kill/Scale/Monitor)
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

### Priority 3: Background Autonomy
Agent creates its own work without being asked.

- [ ] **Daily health check**: auto-created task every morning
  - Scan all active ads, flag issues, summarize performance
  - Create approval items for kill/scale recommendations
  - Update learnings.md with new performance data
- [ ] **Weekly batch planning**: auto-created task every Monday
  - Analyze which angles/formats/personas are winning
  - Draft next batch strategy (angles, formats, personas, hooks)
  - Create "Ready to Generate" task for user to approve
- [ ] **Weekly competitor scan**: auto-created task
  - Scan competitor ad libraries via Meta Ad Library API
  - Flag new creative trends, angles, formats
  - Update creative-dna.md with findings
- [ ] Background tasks run via Railway worker service
- [ ] pg_cron or ARQ schedules trigger tasks
- [ ] All background work shows in Planner as task cards

### Priority 4: Agent Intelligence Improvements
Make the agent's creative decisions better over time.

- [ ] Agent reads Meta performance data BEFORE creating ads (already partially done)
- [ ] Agent parses ad names using naming convention to correlate angle/format/hook with CPA
- [ ] Agent asks user to verify decisions at key points (product selection, angle choice)
- [ ] User feedback on generated ads (thumbs up/down) → stored → informs future batches
- [ ] Competitor ad analysis feeds into creative strategy
- [ ] Performance-based prompt: "Last batch's Problem Callout got 1.2x ROAS, try Ingredient Breakdown this time"

### Priority 5: Planner & Approval UX
Make the task management and approval flow smooth.

- [ ] Wire approve/reject buttons to backend endpoints
- [ ] Approved ads → auto-launch to Meta (Priority 1 dependency)
- [ ] Task cards show inline previews (ad thumbnails, report summaries)
- [ ] "While you were gone" banner with pending approvals count
- [ ] Filter tasks by agent, type, date, offer
- [ ] Real-time task movement via Supabase Realtime
- [ ] Proactive "Ideas" section from agent suggestions

### Priority 6: Onboarding Flow
Build AFTER all features work, BEFORE opening signups.

- [ ] Step 1: Sign up (email + password + brand name + URL)
- [ ] Step 2: The Atomic Unit — explain offers concept
- [ ] Step 3: Connect Shopify (OAuth)
- [ ] Step 4: Connect Meta Ads (OAuth)
- [ ] Step 5: Connect Loop (API key — optional)
- [ ] Step 6: Auto-discover offers from Meta + Shopify
- [ ] Step 7: Set autonomy level
- [ ] Step 8: Brand brain generated, first recommendations shown
- [ ] Step 9: "Create your first ad batch?"

### Priority 7: Pre-Launch Polish
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
- [ ] Worker service deployed but not doing scheduled work yet
- [ ] Some offer endpoints still use global `db` client (potential RLS issues)
- [ ] Ad blocker blocks any URL with `/products` in path — renamed to `/items` but check for other instances
- [ ] Loop sync rate limited at 10 req/sec — need exponential backoff
- [ ] offer_metrics_daily only populated on import, not updated daily from Meta

---

## Integration Status

| Integration | Auth | Data Pull | Data Push | Status |
|-------------|------|-----------|-----------|--------|
| Shopify | ✅ OAuth | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ OAuth | ✅ Campaigns, ads, metrics, content_views | 🟡 Launch batch endpoint built | 🟡 70% |
| Loop Subs | ✅ API Key | ✅ Subscriptions, LTV, churn, MRR | N/A | ✅ 95% |
| Klaviyo | ⬜ Not started | ⬜ | ⬜ | ⬜ 0% (post-MVP) |

---

## Deployment Status

| Service | Platform | Status | Auto-deploy? |
|---------|----------|--------|-------------|
| Frontend | Cloudflare Pages | ✅ Live (demo.runodyssey.io) | Yes (git push) |
| Backend API | Railway (odyssey-x) | ✅ Live (api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | ✅ Deployed | Yes (git push) |
| Database | Supabase | ✅ Live | Manual migrations |

---

## Session Log

### 2026-03-20 (Sessions 1-3)
- Built 3-page layout, Team page, Planner page, Offers page
- Fixed Railway deployment (path resolution), Cloudflare builds
- Created ROADMAP.md, HANDOFF.md, dev dashboard

### 2026-03-22 (Session 4)
- Fixed duplicate terminal entries, sandbox hanging, font installation
- Started Pillow renderer improvements (7/10 formats updated)

### 2026-03-23 (Session 7)
- **Ad Launch Pipeline**: Complete approve → push to Meta flow
  - Fixed launch-batch bug (undefined `campaign_name`)
  - ODY naming convention enforced for ad sets and ads
  - Meta IDs stored back in offer_ad_sets / offer_ads tables
  - Batch approve/reject/launch endpoints added
  - Planner approval auto-triggers Meta launch for ad_batch_launch type
  - Cockpit: batch grouping, approve all, launch modal with ad set search
  - Asset card: wired "Push to Meta" button, format/awareness badges
  - Offer detail: approve all, launch button, published ads with Meta links

### 2026-03-23 (Sessions 5-6) — MASSIVE session
- **Ad System**: Replaced Pillow with Gemini AI (full composition, product images, 10 formats)
- **Security**: Debug endpoints removed, .single() fixes, Meta token headers, encryption required, RLS fixes
- **Team Page**: Three Pillars wired to live data (CPA/AOV/LTV)
- **Offers System**: Complete rebuild — auto-discover from Meta, funnel crawl, entry point grouping, og:image thumbnails, active/sort/delete, detail page with funnel flow visualization
- **Loop Integration**: Full build — API key auth, subscription sync, selling plan metrics (LTV/churn/MRR), manual linking to offers, Three Pillars LTV
- **Backend**: ~15 commits of fixes (RLS, route ordering, ad blocker workaround, rate limiting)
- **Naming Conventions**: Updated ad naming to include angle + persona at ad level

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

### Three Pillars
| Pillar | Source | Current Value | What Improves It |
|--------|--------|---------------|-----------------|
| CPA | Meta Ads | $51 | Better ads, angles, targeting |
| AOV | Shopify | $45 | Better offers, bundles, upsells |
| LTV | Loop | $326 | Better retention, email flows, product quality |
