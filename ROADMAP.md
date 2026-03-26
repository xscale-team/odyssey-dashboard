# Odyssey X — Living Roadmap

> **Last updated: 2026-03-26 (Session 15)**
> Goal-driven, not timeline-driven. Ship MVP when ad generation, launch, monitoring pipeline is bulletproof.

---

## MVP Definition

The MVP is ready when a user can:
1. ✅ Connect Shopify + Meta Ads + Loop
2. ✅ Auto-discover existing offers from Meta ad data
3. ✅ See full funnel flow (impressions, clicks, content views, checkout, purchase)
4. ✅ See subscription LTV/churn per product from Loop
5. ✅ Generate production-quality ads with Gemini AI
6. ✅ Approve ads, auto-push to Meta Ads Manager (with Sonnet 4.6 copy)
7. ✅ Competitor intelligence: auto-scrape Ad Library, classify ads, niche analytics
8. ✅ Billing: pay-as-you-go tokens, Stripe checkout, real cost tracking with 5x markup
9. ✅ Agent monitors performance daily, kills losers, scales winners (Watcher agent)
10. ✅ Agent proactively plans next batch based on performance + competitor data (self-improving prompts)
11. ✅ Guided onboarding flow

---

## Quick Status

| Area | Status | Notes |
|------|--------|-------|
| Integrations (Shopify/Meta/Loop) | ✅ Done | All 3 connected, syncing data |
| Ad Generation (Gemini AI) | ✅ Working | 10 formats, product composition |
| Ad Quality Check (Gemini Vision) | ✅ Done | Scores 0-100, auto-regen < 50 |
| Ad Copywriter (Sonnet 4.6) | ✅ Working | 5 primary texts + 5 headlines per ad |
| Ad Launch Pipeline | ✅ Done | Approve, push to Meta, New Batch / Add to Batch, CBO detection |
| Offer Discovery + Funnel | ✅ Done | Entry points, funnel flow, thumbnails |
| Entry Point Auto-Detection | ✅ Done | Sync from Meta, auto-detect active ad destinations |
| Subscription Metrics (Loop) | ✅ Done | LTV, churn, MRR per selling plan |
| Three Pillars (CPA/AOV/LTV) | ✅ Live | All 3 showing real data |
| Competitor Intelligence | ✅ Done | 185 ads scraped, classified, permanent storage, weekly auto-refresh |
| Intel Dashboard | ✅ Done | Standalone page with angles, formats, hooks, brand cards |
| Competitive Response Alerts | ✅ Done | Weekly diff reports, Intel page banner |
| Billing (Stripe) | ✅ Done | PAYG tokens, 5x markup, real cost tracking, Stripe Checkout |
| Security Audit | ✅ Done | 20 issues fixed + CRON_KEY + webhook signature + RLS bypass |
| Navigation Redesign | ✅ Done | 2 main tabs (Chat/Planner) + sidebar sub-sections |
| Settings Redesign | ✅ Done | Sidebar nav (Billing/Integrations/Competitors/Account) |
| Watcher (Kill/Scale) | ✅ Done | Kill/scale/fatigue rules, 4 autonomy levels, cron |
| Self-Improving Prompts | ✅ Done | Meta performance → angle/format winners in context |
| Landing Page Scanner | ✅ Done | 24h cache, headlines/CTAs injected into context |
| Ad Visual Diversity Engine | ✅ Done | Format/color overuse detection, diversity directive |
| Onboarding Flow | ✅ Done | 6-step wizard, persisted progress, OAuth detection |
| Model Toggle (Normal/Pro) | ✅ Done | Sonnet/Opus user-selectable, stored in profiles |
| Performance Dashboard | ✅ Done | Live Meta metrics page, per-offer breakdown |
| Account Settings | ✅ Done | Profile editing, password change, notification prefs |
| Background Autonomy | 🟡 Partial | Weekly competitor scrape + watcher daily — planner task cards not auto-creating |
| Domain Migration | 🟡 Pending | Code supports tryodyssey.ai, Cloudflare custom domain setup needed |

---

## What's Done (Sessions 3-15)

### Session 15 (2026-03-26) — QA Hardening + New Features

#### Gemini Vision Quality Check ✅ (Priority 2 complete)
- [x] After ads generated, Gemini Vision scores each ad 0-100
- [x] Weak ads (score < 50) auto-regenerated (up to 2 retries per ad)
- [x] Quality score shown on ad cards in planner and cockpit view
- [x] Score factors: visual hierarchy, text legibility, brand fit, CTA prominence

#### Model Toggle (Normal / Pro) ✅
- [x] Normal mode: Claude Sonnet 4.6 in E2B sandbox
- [x] Pro mode: Claude Opus 4.6 in E2B sandbox (deeper reasoning, ~5x cost)
- [x] User selects in Settings > Account
- [x] Stored in `profiles.model_preference` (`sonnet` | `opus`)
- [x] `/me` endpoint returns model_preference, passed to sandbox script
- [x] `017_model_preference.sql` migration (run in Supabase dashboard)

#### Performance Dashboard ✅
- [x] Live Meta metrics for all active campaigns
- [x] Per-offer breakdown: spend, impressions, CTR, CPA, ROAS
- [x] Replaces "Coming Soon" placeholder page

#### Account Settings ✅
- [x] Profile editing (name, email)
- [x] Password change flow
- [x] Notification preferences

#### Critical Bug Fixes ✅
- [x] RLS bypass: service-role Supabase client for backend reads (was blocking data queries)
- [x] Entry point edit/delete + URL normalization (no duplicate entries)
- [x] Email signup validation + password reset flow
- [x] Onboarding RPC grants (`016_onboarding_rpc_grants.sql` — anon/authenticated can call onboarding RPCs)
- [x] Stripe webhook signature verification
- [x] CRON_KEY security on all cron endpoints
- [x] API overload retry (Claude 529 + Gemini quota: exponential backoff)
- [x] Normal/Pro mode parity (both use sandbox)
- [x] Per-brand competitor data in business context
- [x] UTF-8 surrogate character sanitization
- [x] Full business context enrichment (orders, subs, credits, assets, EPs, rules, competitors)

#### 13 QA Bug Fixes ✅
- [x] Empty response after long thinking (heartbeat keepalive)
- [x] Conversation corruption with multiple tabs (per-tab lock)
- [x] Token depletion display wrong (balance update after action)
- [x] Unnecessary "shall I proceed?" confirmations removed
- [x] Em dashes in generated copy (scrubbed from prompts and outputs)
- [x] Quality score not displaying on ad cards
- [x] Token cost estimates miscalibrated
- [x] Stale chat cleanup race condition (debounce)
- [x] Copy button missing feedback state
- [x] Bloom competitor returning 0 ads (search term fix)
- [x] Shopify date filter (was 7 days, now 30)
- [x] Ad set dates not propagating to Meta campaign
- [x] Watcher banner not updating after manual run (polling fix)

### Session 14 (2026-03-25)

#### Landing Page Scanner ✅ (Priority 5)
- [x] `landing_page_scanner.py` — stdlib html.parser, zero new deps
- [x] Extracts: h1 headline, h2 subheadlines, meta description, CTA texts, key phrases, price signals
- [x] `landing_page_cache` table (URL-unique, 7-day TTL, service-role upsert)
- [x] `_build_business_context()` injects "LANDING PAGE INTELLIGENCE" per active offer
- [x] Agent sees exact page copy before generating ads

#### Ad Visual Diversity Engine ✅ (Priority 6)
- [x] Analyses last 25 ads (all statuses: published + draft/approved)
- [x] Counts format/color_scheme/funnel/awareness with Counter
- [x] Labels OVERUSED (>45%) and UNTESTED attributes
- [x] Checks funnel distribution imbalance (heavy TOF or BOF)
- [x] Blocks last 6 headlines from verbatim reuse
- [x] Generates per-batch DIVERSITY DIRECTIVE

#### Onboarding Flow ✅ (Priority 7)
- [x] `015_onboarding.sql`: onboarding columns on profiles, `complete_onboarding_step()` RPC state machine
- [x] 6-step wizard: Welcome → Shopify → Meta → Create Offer → Competitors → Autonomy Level
- [x] Progress persisted to backend after each step (resumes on reload)
- [x] All steps skippable
- [x] `auth-guard.tsx` redirects unonboarded users to /onboarding

### Session 13 (2026-03-25)

#### Competitive Response Alerts ✅ (Priority 4)
- [x] `diff_engine.py`: compares current vs previous weekly competitor snapshot
- [x] Detects: new ads launched, ads killed, format shifts (rising/falling), angle changes
- [x] Auto-generates markdown narrative + suggested responses
- [x] Intel page: collapsible "Intel Update" banner with brand activity, format shift cards, action items
- [x] Weekly cron: Monday 10 AM UTC

### Session 12 (2026-03-25)

#### Self-Improving Prompts ✅ (Priority 3)
- [x] `performance_learner.py`: pulls 30-day Meta metrics for all ODY-named ads
- [x] Parses naming convention to extract angle/persona/format/awareness dimensions
- [x] Classifies tiers: winner/average/loser/insufficient_data
- [x] `performance_insights` table with SECURITY DEFINER RPCs
- [x] `_build_business_context()` injects PERFORMANCE INSIGHTS before every generation
- [x] Weekly cron: Monday 10 AM UTC

### Session 11 (2026-03-25)

#### Watcher Agent ✅ (Priority 1)
- [x] 5 kill rules: emergency kill, CPA death spiral, zero conversions, CTR floor, frequency/fatigue
- [x] 2 scale rules: steady winner (5+ days), micro-scale (3+ days)
- [x] 4 autonomy levels: supervised / guided / autonomous / full_auto
- [x] Undo action endpoint (re-activate paused ad sets)
- [x] WatcherBanner in Planner: pill summary, expandable report, Run Check button
- [x] Daily cron: 8 AM UTC

### Sessions 9-10 (2026-03-25)

- [x] **Billing**: Stripe PAYG, 5x markup, volume bonuses, Stripe Checkout
- [x] **Security Audit**: 20 issues fixed across codebase
- [x] **Smart Routing**: `needs_sandbox()` for fast path on simple questions
- [x] **Navigation Redesign**: Chat + Planner + sidebar sub-sections
- [x] **Intel Page**: Competitor analytics dashboard
- [x] **Settings**: Premium billing UI, decoy pricing psychology
- [x] **Agent Freedom**: Removed forced constraints, keeps winning angles

### Sessions 3-8 (2026-03-20 to 2026-03-24)

- [x] Full auth flow, Shopify/Meta/Loop OAuth
- [x] Gemini AI ad generation (replaced Pillow)
- [x] Ad copywriter (Sonnet 4.6, 5 primary + 5 headline variations)
- [x] Ad launch pipeline (full approve → Meta flow)
- [x] Competitor intelligence system (185 ads, 10 brands, weekly auto-refresh)
- [x] Offers system (auto-discover, funnel flow, entry points)
- [x] Team page (Three Pillars wired to live data)
- [x] Security: RLS, encrypted tokens, CORS, no debug endpoints

---

## What's Next — Priority Order

### Priority 1: Verify Deployed Fixes (Immediate)
- [ ] Test ad generation + launch on demo.runodyssey.io with real Meta account
- [ ] Confirm per-brand competitor data shows in agent context (not entire niche)
- [ ] Verify Meta routing fix (Normal mode sandbox routing)
- [ ] Confirm Stripe webhook still processing correctly

### Priority 2: Outstanding QA Tests
- [ ] **Test 01 — New User Onboarding**: Sign up fresh account, go through 6-step wizard, generate first ad
- [ ] **Test 10 — Watcher Agent**: Let watcher run with real Meta ad data, verify kill/scale decisions
- [ ] **Test 12 — Speed**: Measure end-to-end latency for chat, ad generation, launch

### Priority 3: Domain Migration
- [ ] Set up `app.tryodyssey.ai` as custom domain in Cloudflare Pages
- [ ] Verify both `demo.runodyssey.io` and `app.tryodyssey.ai` work after setup
- [ ] Update CORS allowlist in backend config to include both domains
- [ ] Test auth flow on new domain

### Priority 4: Performance Dashboard Completion
- [ ] Wire per-offer ROAS/CPA/spend charts to live Meta data (currently static)
- [ ] Add time-range selector (7d / 30d / 90d)
- [ ] Add "vs previous period" comparison
- [ ] Auto-update `offer_metrics_daily` from Meta (currently only populated on import)

### Priority 5: Background Autonomy Completion
- [ ] Daily health check: auto-create task card in Planner each morning
- [ ] Weekly batch planning: agent drafts strategy for user to approve
- [ ] All background work shows in Planner as task cards (not just Watcher banner)
- [ ] Supabase Realtime: replace planner polling with live subscriptions

### Priority 6: Pre-Launch Polish
- [ ] Rate limiting: 5 req/min chat, 3 sandbox, 60 everything else
- [ ] Mobile responsive pass (currently desktop-only)
- [ ] Loading skeletons for all async operations
- [ ] Empty states for all lists/grids
- [ ] Sandbox max_steps: increase to 16-20 for complex workflows
- [ ] Per-user token spending cap per action (prevent runaway burns)

### Priority 7: Klaviyo Integration
- [ ] OAuth connection
- [ ] Pull flows, sequences, open/click rates
- [ ] LTV improvements tracked via email engagement
- [ ] Agent creates Klaviyo flows via Mailer team member

---

## Known Bugs & Technical Debt

### Active Issues
- [ ] Chat doesn't render ad images inline in messages (shows in terminal only)
- [ ] No Supabase Realtime subscriptions (polling only, 10-30s intervals)
- [ ] `offer_metrics_daily` only populated on import, not auto-updated from Meta
- [ ] Facebook CDN image URLs in old `generated_assets` have expired
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads
- [ ] Sandbox max_steps=10 may be insufficient for complex workflows (should be 16-20)
- [ ] No per-user token spending cap per action (agent could burn all credits in one run)
- [ ] Agent sometimes picks paused EPs if brand brain has old data
- [ ] Performance page charts not fully wired to live data (page exists, API calls needed)
- [ ] Planner approval buttons not fully connected to backend launch endpoints
- [ ] Domain migration to app.tryodyssey.ai pending (Cloudflare setup)
- [ ] Tests 01, 10, 12 from QA plan never run

### Recently Fixed
- [x] RLS blocking backend reads (service-role client)
- [x] Per-brand competitor data in business context
- [x] UTF-8 surrogate crash
- [x] Stripe webhook signature not verified
- [x] CRON_KEY not enforced on cron endpoints
- [x] API overload no retry logic
- [x] Normal mode incorrectly bypassing sandbox
- [x] 13 QA bugs (see Session 15 above)
- [x] Chat titles not auto-naming
- [x] Simple questions spinning up E2B sandbox
- [x] Meta API rate limits on EP sync (5-min cache)
- [x] Agent forced to rotate angles

---

## Integration Status

| Integration | Auth | Data Pull | Data Push | Status |
|-------------|------|-----------|-----------|--------|
| Shopify | ✅ OAuth | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ OAuth | ✅ Campaigns, ads, metrics | ✅ Launch batch, create ads | ✅ 90% |
| Loop Subs | ✅ API Key | ✅ Subscriptions, LTV, churn, MRR | N/A | ✅ 95% |
| Meta Ad Library | ✅ Public (Playwright) | ✅ Competitor static ads, copy, images | N/A | ✅ 95% |
| Stripe | ✅ Live keys | ✅ Checkout, webhooks | ✅ Balance top-up | ✅ 95% |
| Klaviyo | ⬜ Not started | ⬜ | ⬜ | ⬜ 0% (post-MVP) |

---

## Deployment Status

| Service | Platform | Status | Auto-deploy? |
|---------|----------|--------|-------------|
| Frontend | Cloudflare Pages | ✅ Live (demo.runodyssey.io) | Yes (git push) |
| Frontend | Cloudflare Pages | 🟡 Pending (app.tryodyssey.ai) | After custom domain setup |
| Backend API | Railway (odyssey-x) | ✅ Live (api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | ✅ Deployed | Yes (git push) |
| Database | Supabase | ✅ Live (migrations 000-017 applied) | Manual migrations |
| Image Storage | Supabase Storage | ✅ Live (competitor-ads bucket) | Auto via scraper |
| Payments | Stripe (live keys) | ✅ Live | Webhook auto-processes |

---

## Cron Jobs

| Job | Schedule | What it does |
|-----|----------|--------------|
| weekly-competitor-ad-scrape | Monday 6 AM UTC | Scrapes Ad Library for all competitor brands |
| weekly-classify-competitor-ads | Monday 8 AM UTC | Classifies new unclassified ads with Sonnet |
| daily-watcher-health-check | Daily 8 AM UTC | Pulls metrics, kills losers, scales winners for all users |
| weekly-performance-sync | Monday 10 AM UTC | Syncs performance insights (angle/format/persona winners) for all users |
| weekly-competitor-diff-report | Monday 10 AM UTC | Generates diff report: new ads, killed ads, format/angle shifts per niche |

---

## Session Log

### 2026-03-20 (Sessions 1-3)
- Built 3-page layout, Team page, Planner page, Offers page
- Fixed Railway deployment (path resolution), Cloudflare builds

### 2026-03-22 (Session 4)
- Fixed duplicate terminal entries, sandbox hanging, font installation

### 2026-03-23 (Sessions 5-6)
- Ad System: Replaced Pillow with Gemini AI
- Security: Debug endpoints removed, RLS fixes, encryption required
- Team Page: Three Pillars wired to live data
- Offers System: Complete rebuild with auto-discover, funnel crawl
- Loop Integration: Full build with LTV/churn/MRR

### 2026-03-23 (Session 7)
- Ad Launch Pipeline: Full end-to-end approve, push to Meta
- Sonnet 4.6 Copywriter: 5 primary text + 5 headline variations
- CBO Detection, New Batch / Add to Batch

### 2026-03-24 (Session 8)
- Competitor Intelligence System: 185 ads, 10 brands, full pipeline
- Niche Analytics: angles, formats, hooks, winner scoring
- Auto-schedule: pg_cron weekly scrape + classify

### 2026-03-25 (Session 9)
- **Billing**: Full Stripe integration, PAYG tokens, 5x markup, volume bonuses
- **Security Audit**: 4 agents audited entire codebase, 20 issues found and fixed
- **Navigation**: Consolidated to Chat + Planner with sidebar sub-sections
- **Intel Page**: Standalone competitor dashboard with full analytics
- **Settings**: Sidebar nav, premium billing UI with decoy pricing
- **Planner**: 3-column Kanban, batch cards, compact Done column
- **Chat**: Titles, timestamps, grouping, delete, overloaded retry
- **Entry Points**: Auto-detect from Meta, sync button, active filtering

### 2026-03-25 (Session 10)
- **Smart Chat Routing**: `needs_sandbox()` classifier, fast path for simple questions
- **Agent Freedom**: Removed forced constraints, keeps winning angles
- **EP Intelligence**: Auto-sync before generation, ACTIVE-only filtering
- **Settings Sidebar**: Billing/Integrations/Competitors/Account
- **Token Pricing**: Decoy psychology, gradient borders, BEST VALUE hero card
- **Stripe**: Live $1 test payment confirmed end-to-end

### 2026-03-25 (Session 11)
- **Watcher Agent** (Priority 1): 5 kill rules + 2 scale rules + fatigue. 4 autonomy levels. Daily cron. Undo support. WatcherBanner in Planner.

### 2026-03-25 (Session 12)
- **Self-Improving Prompts** (Priority 3): Performance learner, ODY naming parse, `performance_insights` table, context injection, weekly cron.

### 2026-03-25 (Session 13)
- **Competitive Response Alerts** (Priority 4): diff_engine.py, Intel Update banner, weekly cron. Migration 013.

### 2026-03-25 (Session 14)
- **Landing Page Scanner** (Priority 5): landing_page_scanner.py, 7-day cache, context injection. Migration 014.
- **Visual Diversity Engine** (Priority 6): Last 25 ads, overuse detection, DIVERSITY DIRECTIVE.
- **Onboarding Flow** (Priority 7): 6-step wizard, OAuth detection, progress persistence. Migrations 014+015.

### 2026-03-26 (Session 15)
- **Gemini Vision Quality Check** (Priority 2): Scores every ad 0-100, auto-regen < 50. Quality score on ad cards.
- **Model Toggle**: Normal (Sonnet) / Pro (Opus) user-selectable. Migration 017.
- **Performance Dashboard**: Live Meta metrics, per-offer breakdown. Was placeholder.
- **Account Settings**: Profile editing, password change, notification prefs.
- **RLS bypass**: Service-role Supabase client for backend reads.
- **13 QA bug fixes**: See Session 15 section in HANDOFF.md for full list.
- **Security hardening**: Stripe webhook signature, CRON_KEY enforcement.
- **Context enrichment**: Full business context in every sandbox run (orders, subs, credits, EPs, competitors, insights, landing pages, diversity report).
- **Migrations**: 016 (onboarding RPC grants) + 017 (model_preference) — both run.

---

## Architecture Notes

### The Offer as Atomic Unit
```
OFFER (e.g., "Hashi Aid Special")
 |- Landing Page URL (www.ibdassist.com/pages/hashi-aid-special)
 |- Entry Points (auto-detected from Meta ACTIVE ads)
 |    |- Quiz | 50% Off + Free Gift (quiz.ibdassist.com) -- ACTIVE, $0 CPA
 |    |- Hashi Listicle V2 ($1787 spend, 0.3x ROAS, 40 ads)
 |    |- Hashi Listicle V1 ($219 spend, 0.5x ROAS, 20 ads)
 |- Selling Plans (from Loop)
 |    |- HashiAid 1 Bottle Monthly (381 active, $152 LTV, 22% churn)
 |- Ad Spend (from Meta) -> CPA
 |- Orders (from Shopify) -> AOV
 |- Subscriptions (from Loop) -> LTV
 |- Funnel: 47K impressions -> 861 clicks -> 664 views -> 194 checkouts -> 40 purchases
```

### Billing Architecture
```
USER ACTION (e.g., chat, ad batch, launch)
 |- Backend tracks actual AI costs (Claude tokens, Gemini calls, E2B time)
 |- Applies 5x markup
 |- Deducts from user balance (in cents/tokens)
 |- If balance < estimated cost -> 402 Payment Required
 |- User tops up via Stripe Checkout ($10-$500, bonus tokens at volume)
 |- Webhook auto-credits balance on successful payment
```

### Three Pillars
| Pillar | Source | Current Value | What Improves It |
|--------|--------|---------------|-----------------|
| CPA | Meta Ads | $51 | Better ads, angles, targeting |
| AOV | Shopify | $45 | Better offers, bundles, upsells |
| LTV | Loop | $326 | Better retention, email flows |
