# Odyssey X — Living Roadmap

> **Last updated: 2026-04-13 (Session 25)**
> Goal-driven, not timeline-driven. Ship MVP when pipeline is bulletproof.

---

## MVP Definition — ALL COMPLETE

1. ✅ Connect Shopify + Meta Ads + Loop
2. ✅ Auto-discover offers from Meta ad data
3. ✅ Full funnel flow (impressions → clicks → content views → checkout → purchase)
4. ✅ Subscription LTV/churn per product from Loop
5. ✅ Generate production-quality ads with Gemini AI (10 formats, 8-10/10 quality scores)
6. ✅ Approve ads, push to Meta Ads Manager (Sonnet 4.6 copy, CBO detection)
7. ✅ Competitor intelligence: scrape Ad Library, vision-classify, niche analytics
8. ✅ Billing: PAYG tokens, Stripe checkout, real cost tracking, 5x markup
9. ✅ Watcher: daily kill/scale, 4 autonomy levels, fatigue detection
10. ✅ Self-improving prompts: Meta performance → angle/format winners in context
11. ✅ Guided onboarding flow (6-step wizard, OAuth detection)

---

## What's Next — Priority Order

### Priority 0: Founding Member Quest (Session 25) — ✅ SHIPPED
- [x] 5-step activation quest: Connect Shopify → Welcome Audit → First Ad → Rate Ad → Invite Friend
- [x] 50 tokens per step drip, 500 bonus on completion, 20-slot Founding Build unlock
- [x] Migrations 020-023 applied to live DB
- [x] Backend quest module + hooks in Shopify OAuth, chat SSE (image_gen, feedback, welcome_audit), referral reward
- [x] Frontend: /quest page, chat banner, completion modal with confetti, sidebar progress pill, admin Founding Builds tab
- [x] 12 pytest tests + 2 Playwright E2E tests all passing
- [x] Removed NOW Foods from competitor list (pet supplement contamination)
- [x] Supabase + Cloudflare MCPs connected for automated workflow
- [ ] **MONITOR:** Watch activation funnel post-deploy. If conversion doesn't improve, audit whether users are seeing/engaging with the quest
- [ ] **PHASE 2 (deferred):** Login/usage streaks + milestone badges once Phase 1 proves activation lift
- [ ] **PHASE 3 (deferred):** Weekly leaderboard with cash/token prizes once activity density supports it

### Priority 1: Fix 30-Day Shopify Order Count Gap
- [x] Added debug diagnostics to _get_shopify_orders (Session 21)
- [x] Removed `pending` filter from order count (Session 21) — did NOT resolve the gap
- [x] Added subscription source_name mapping (Loop → Subscription)
- [x] Tried date-only format + 1-day buffer for timezone fix — only added 2 orders (270 vs 268)
- [x] **ROOT CAUSE CONFIRMED:** REST API returns only 270 orders total. Shopify Analytics counts 424. The REST API simply does not include all order types.
- [x] **FIX DEPLOYED (Session 22):** Replaced REST `orders.json` with GraphQL Admin API (`orders` query). GraphQL returns ALL order types (subscriptions, POS, draft orders, etc.). Cursor-based pagination, rate limiting, same return structure. Needs verification against Shopify Analytics.
- [ ] **VERIFY:** Run Welcome Audit and compare order count with Shopify Analytics (target: ~424)

### Priority 2: Fix Orphaned Conversation Bug
- [x] Added error message + retry guidance to chat-store.ts (Session 21)
- [x] Added friendly sandbox crash message to router.py (Session 21)
- [ ] **Still occurring:** 1 orphaned conversation during Session 21 testing. The SSE connection never opens at all — this is before the onError handler fires. Root cause is likely a failed POST request (network/deploy timing), not a sandbox crash.
- [ ] Add SSE connection failure detection + auto-retry or error UI

### Priority 3: Complete Meta App Review (BLOCKING for non-tester users)
- [ ] Upload App Icon, complete Data Handling + Reviewer Instructions
- [ ] Remove oEmbed permissions, submit for Advanced Access
- [ ] Workaround: Add teammates as Testers in App Roles

### Priority 4: Fix Sandbox Token Refresh on Integration Connect
- [ ] New integrations not picked up until "New Chat"
- [ ] Need sandbox invalidation or dynamic token injection

### Priority 5: Performance Dashboard Completion
- [ ] Time-range selector (7d / 30d / 90d)
- [ ] Per-offer ROAS/CPA/spend charts
- [ ] "vs previous period" comparison

### Priority 6: Background Autonomy Completion
- [ ] Daily health check → auto-create task card in Planner
- [ ] Weekly batch planning → agent drafts strategy for approval
- [ ] "While You Were Gone" morning briefing
- [ ] Supabase Realtime (replace planner polling)

### Priority 7: Pre-Launch Polish
- [ ] Mobile responsive pass
- [ ] Loading skeletons for async operations
- [ ] Per-user token spending cap per action

### Priority 8: Klaviyo Integration (Post-MVP)
- [ ] OAuth, pull flows/sequences, track email engagement

### Priority 9: Meta Ads Integration (Post Meta App Review)
Deferred from MVP. Re-add once Meta App Review is approved for Advanced Access.
- [ ] Re-add `connect_meta` onboarding step
- [ ] Meta Ads launch pipeline (create campaigns, ad sets, ads from generated creatives)
- [ ] Offer system re-enable (offer = product + pricing + landing page + entry points + ads)
- [ ] Onboarding steps: Create Offer, Set Competitors, Pick Ad Styles, Set Autonomy
- [ ] Performance dashboard with Meta spend/ROAS/CPA charts
- [ ] Watcher integration with Meta (auto kill/scale based on live performance)
- [ ] Ad Library live scan (currently falls back to pre-scraped DB)
- **Code preserved in git history** — removed in commit for Session 24 onboarding simplification

---

## Known Bugs & Technical Debt

### P1 — Active
- [x] **CRITICAL: Brand data leaking across accounts** — Fixed: removed all hardcoded brand names, scoped competitor ads per user, auto-assign on signup (Session 23)
- [x] **CRITICAL: New user signup crash** — Fixed: `assign_default_competitors` had no search_path, crashing entire signup (Session 24)
- [x] **Billing only charged on batch completion** — Fixed: per-ad charging, prevents free-riding on errors (Session 23)
- [x] **Gemini ignoring competitor style references** — Fixed: contradictory prompt was telling it to copy style AND ignore everything (Session 23)
- [x] **30-day Shopify order count ~37% low** — Fixed: GraphQL API (Session 22)
- [x] **Gemini brand name hallucination** — Fixed: brand name validation in text + vision QC (Session 21)
- [x] **Generated ads vanish on SSE crash** — Fixed: partial content preserved as message on error (Session 24)
- [x] **Product image download fails (URL truncation)** — Fixed: download_product_image now uses Shopify product_id for fresh API fetch (Session 24)
- [x] **Sandbox doesn't pick up new integrations** mid-session — Fixed: `get_or_create()` now re-injects env vars on every cached-sandbox hit, so new Shopify/Meta tokens land before the next `run_code` (Session 26)
- [x] **Agent saw OLDEST 20 messages, not newest** — Fixed: `chat/router.py` conversation history query was `order(desc=False).limit(20)` which returned the first 20 messages. Now `desc=True, limit(50)` + reverse in Python. Long conversations were leaving the agent blind to recent turns (Session 26)
- [x] **`_save_brand_brain` returned `persisted: True` on cloud failure** — Fixed: only reports `persisted: True` when Supabase Storage accepted the upload. Returns `success: False` with error detail otherwise so the agent knows the save didn't stick (Session 26)
- [x] **Brain files silently truncated at 4000 chars on read** — Fixed: removed `_MAX_BRAIN_CHARS` cap. Agent now reads the full accumulated learnings.md / generated_ads.md. Half the brain was being hidden after ~50 ads (Session 26)
- [x] **Per-ad billing errors silently swallowed** — Fixed: exceptions in `chat/router.py` charge path now log loudly and insert a `billing_failures` row for reconciliation. Added migration 027 for the table (Session 26)
- [x] **`_CONTEXT_CACHE` grew unbounded** — Fixed: added `_context_cache_evict_expired()` called on every cache write. O(n) sweep is cheap at our scale (Session 26)
- [x] **`_active_sandboxes` never reaped idle users** — Fixed: `SandboxManager` now tracks `_last_used` and drops references inactive > 30 min on every `get_or_create` (Session 26)

### P2 — Medium
- [x] **Gemini rate limits (first 2 ads in batch fail)** — Fixed: 3s delay between calls + retry with simplified prompt (Session 23)
- [x] **Same competitor ads used every session** — Fixed: time-seeded randomization (Session 23)
- [x] **Wrong product images persisting across sessions** — Fixed: removed Supabase cache, fresh download each time + user verification (Session 23)
- [x] **Competitor inspiration images fail to download in sandbox** — Fixed: images cached to sandbox filesystem in get_competitor_ads, generate_ad_batch checks cache first (Session 24)
- [x] **usage_events check constraint rejects ad_single** — Fixed: expanded constraint to include ad_single, ad_batch (Session 24)
- [ ] **Slow competitor scan** (~60s with no progress feedback). Needs progress streaming.
- [ ] **`days_active` = 0 for all competitor ads**. Scraper doesn't track re-sighted ads.
- [ ] **Ad Library live scan fails**. Falls back to pre-scraped DB. Needs Apify/SearchAPI.
- [ ] **Agent still not using cached base64 for inspiration** — pre-loading works but agent passes URL not base64. Cache fallback handles it but style-copying would be better with actual images.

### P3 — Low
- [ ] Team persona inconsistency (agent says "I" instead of team member names)
- [ ] Response length inconsistency across scenarios
- [ ] No per-user token spending cap per action
- [ ] `offer_metrics_daily` not auto-updating from Meta
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads
- [x] Product image shows AFTER buttons in verification step — Fixed: terminal panel moved above message, buttons at bottom (Session 24)

---

## Integration Status

| Integration | Auth | Pull | Push | Status |
|-------------|------|------|------|--------|
| Shopify | ✅ OAuth | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ OAuth | ✅ Campaigns, ads, metrics | ✅ Launch/create | ✅ 90% |
| Loop Subs | ✅ API Key | ✅ LTV, churn, MRR | N/A | ✅ 95% |
| Stripe | ✅ Live | ✅ Checkout, webhooks | ✅ Balance top-up | ✅ 95% |
| Klaviyo | ⬜ | ⬜ | ⬜ | ⬜ 0% |

---

## Deployment

| Service | Platform | Auto-deploy? |
|---------|----------|-------------|
| Frontend | Cloudflare Pages (runodyssey.io) | Yes (git push) |
| Backend API | Railway (odyssey-x → api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | Yes (git push) |
| Database | Supabase (migrations 000-023) | Supabase MCP (automated) |
| Payments | Stripe (live keys) | Webhook auto |

---

## Session Log

| Session | Date | Key Work |
|---------|------|----------|
| 1-3 | 2026-03-20 | Layout, Team/Planner/Offers pages, Railway/Cloudflare deploy |
| 4 | 2026-03-22 | Terminal fixes, sandbox hanging, font install |
| 5-6 | 2026-03-23 | Gemini AI ads (replaced Pillow), security audit, Three Pillars, Offers rebuild, Loop integration |
| 7 | 2026-03-23 | Ad Launch Pipeline (approve → Meta), Sonnet Copywriter, CBO detection |
| 8 | 2026-03-24 | Competitor Intelligence (185 ads, 10 brands, vision classify, pg_cron) |
| 9 | 2026-03-25 | Billing (Stripe PAYG), security audit (20 fixes), navigation redesign, Intel page |
| 10 | 2026-03-25 | Smart routing, agent freedom, settings sidebar, Stripe live test |
| 11 | 2026-03-25 | Watcher Agent (5 kill + 2 scale rules, 4 autonomy levels, daily cron) |
| 12 | 2026-03-25 | Self-Improving Prompts (performance learner, ODY naming parse, weekly cron) |
| 13 | 2026-03-25 | Competitive Response Alerts (diff engine, Intel banner, weekly cron) |
| 14 | 2026-03-25 | Landing Page Scanner, Visual Diversity Engine, Onboarding Flow |
| 15 | 2026-03-26 | Gemini Vision QC, Model Toggle, Performance Dashboard, Account Settings, 13 QA fixes |
| 16 | 2026-03-26 | API Rate Limiting, OAuth onboarding redirects, inline toasts |
| 17 | 2026-03-26 | Surrogate v2, em dash rule, visual ad tools, planner timeout, history loading fix |
| 18 | 2026-03-27 | Surrogate v3, blank response fix (chunked events), founder adaptation, Google signup, Shopify dev dashboard, live QA |
| 19 | 2026-03-31 | Shopify Client ID OAuth, competitor vision analysis, Gemini full-creative, Meta OAuth improvements |
| 20 | 2026-04-06 | **Full system test**: 9-ad full-funnel suite (all 8-10/10), data accuracy audit, 5,641 credits for full session. Grade: A |
| 21 | 2026-04-07 | **Full system test + 6 bug fixes + Welcome Audit**: 12 msgs tested (all A+), 6 ads QA'd, 6 bugs fixed. Built Welcome Audit flow (726 tokens/$7.26). Cost optimization (MAX_ITER 30→20, parallel calls). Shopify REST API confirmed missing 154/424 orders. |
| 22 | 2026-04-07 | **New User UX Audit + P0 Fixes**: Full UX audit of every screen. Fixed: welcome screen (heading, CTA hierarchy, button text), brand name hallucination (AXOS→IBD Assist), broken creative images, competitor images in audit, mobile sidebar collapse. Audit now 80s/802 tokens. Ship verdict: CONDITIONAL SHIP. |

| 23 | 2026-04-09 | **CRITICAL: Data isolation + billing + ad quality overhaul**. Fixed AXOS brand contamination across all accounts, per-user competitor scoping, auto-assign competitors on signup, per-ad billing (was only charging on batch completion), Gemini cost $0.02→$0.13, Opus-only mode, admin P&L dashboard, kanban tickets, Shopify onboarding rewrite, product image verification flow, creative preferences hydration, feedback weight system (likes 3, dislikes 5, admin 7/10, no-feedback 1), Gemini retry on failure, mobile text limits, fixed Gemini style-copying (contradictory prompt was making it ignore competitor references entirely). |
| 24 | 2026-04-13 | **PUBLIC LAUNCH + 20 fixes + referral program**. Fixed signup crash, removed ghost connect_meta step (was blocking 38% of signups), simplified onboarding to Welcome→Shopify→Chat (removed 4 unnecessary steps), video walkthrough for Shopify setup, product images by ID, ads preserved on SSE crash, competitor image caching, sandbox timeout 14→20min, Meta Pixel dual tracking, Stripe Purchase tracking, sortable admin table, 2500 free tokens, auto-create credit_balances, referral program (both users get 500 tokens), Files gallery button. Tested 10-ad generation: 8.8min, $6.81, no timeout. 45 signups on launch day. |
| 25 | 2026-04-13 | **Founding Member Quest (gamification Phase 1)**. Full superpowers flow (brainstorm → spec → plan → subagent-driven implementation). 20-task plan executed across 4 migrations (020-023), backend quest module + RPCs, 5 SSE/OAuth hooks, full frontend (/quest page, banner, completion modal + confetti, sidebar pill, admin tab), 12 pytest tests, 2 Playwright E2E tests in real headless Chrome. Caught and fixed an orphaned pages/chat.tsx that prevented banner rendering. Also: removed NOW Foods from competitor list (pet contamination), connected Supabase + Cloudflare MCPs, saved multiple project-memory notes. 25 commits on feat/founding-member-quest, merged to main. |

### Session 23 Detail

**Data Isolation (CRITICAL):**
- Removed ALL hardcoded brand names (HashiAid/AXOS/IBDAssist) from 8 files
- Competitor ads query scoped to user's tracked brands (was global dump of all 500)
- Auto-assign all 12 competitor brands to new users via DB trigger + backfill
- Brand QA check validates against actual user brand name, not hardcoded whitelist
- Deleted contaminated brand brain files and cached product images for affected accounts

**Billing Overhaul:**
- Per-ad charging (was only at batch completion, so errors = we eat the cost)
- Gemini cost corrected: $0.02 → $0.13 per image
- All users on Opus 4.6 (removed Normal/Pro toggle)
- Admin P&L dashboard: Stripe Revenue, Our API Costs, Net Profit/Loss, Free Credit Subsidy
- Feedback weights: user like=3, dislike=5, admin like=7, dislike=10, no feedback=1

**Ad Quality:**
- Gemini style-copying FIXED: old prompt said "copy style" + "ZERO elements from competitor" which contradicted. Gemini ignored references entirely. New prompt separates design-copy from brand-safety.
- Gemini retry on failure (5s delay, simplified prompt, no inspiration)
- 3s delay between Gemini calls (prevents rate limit 429s)
- Mobile text limits per format (meme 15 words, callout 25, IB 40)
- Brand/product name rules (product name on ad, not parent brand)
- Competitor ad randomization (time-seeded, 3 winners + 3 random)
- Gemini outcome logging to usage_events for pattern analysis

**UX:**
- Admin tickets as drag-and-drop kanban board with user emails
- Clickable buttons for emoji/bullet options (parser updated)
- Questions + options at END of message (above input box)
- Product image verification mandatory before generation
- Shopify onboarding: correct dev dashboard URL, App URL, redirect URL, store name auto-suffix

### Session 22 Detail

**Phase 1 — Full UX Audit (every screen screenshotted and graded)**
- Welcome Screen: B→A (after fixes), Loading: A-, Audit Response: A, Three Pillars: A+, #1 Opportunity: A+, Planner: B+, Settings: A, Mobile: D→B (after sidebar fix)

**Phase 2-3 — P0 Fixes (commit 1fee488)**
1. Welcome heading "Meet ODY-1" → "Your AI Marketing Team"
2. Welcome CTA: teal accent primary button with subtitle, shorter text (no truncation)
3. System prompt: use Shopify shop name for audit header, no competitor image embeds
4. Image gallery: contextual labels ("References" vs "Creatives Generated"), broken image fallback
5. Mobile: sidebar hidden on < md breakpoint
6. Chat store: added subscription metrics tool label

**Phase 4 — Final Validation**
- Brand name: "IBD Assist (Crohniva)" (correct, no AXOS hallucination)
- Audit time: 80.8s (down from 103.9s, 22% faster)
- Token cost: 802 tokens ($8.02), leaves $11.98 for follow-ups
- No crashes, no orphaned conversations, no blank responses
- No competitor images embedded, no broken images

**Ship Verdict: CONDITIONAL SHIP**
- Ready for limited invite (5-10 users) with monitoring
- Blockers remaining: Meta App Review (non-tester users), brand brain reset for new users (first-time users won't have AXOS issue)
- Risks: orphaned conversation (3 occurrences total, intermittent), Shopify 36% order undercount

### Session 21 Detail

**Phase 1 — Testing (12 messages, ~9,000 credits)**
- Chat 1 (5/5 A+): data accuracy, trends, subscriptions, Meta deep dive, strategy synthesis
- Chat 2 (7/8 A+): top 5 ads, competitor landscape (183 ads, 12 brands), strategic brief, 6 ads generated (5 ship-ready, 1 brand name hallucination caught by new QC)
- Visual QA on all 6 generated ads: TOF (9/10, 8/10), MOF (9/10, 9/10), BOF (9/10, 8/10)

**Phase 2 — 6 Bug Fixes (commit 913c05b)**
- P1: Brand name validation in Gemini QC (text + vision check)
- P1: Shopify date-only format + 1-day buffer (only added 2 orders; REST API confirmed to miss 154/424 orders)
- P2: Orphaned conversation retry UI (lastFailedContent + error in chat)
- P2: Sandbox crash friendly message ("peer closed" → retry guidance)
- P3: Meta ad image persistence to Supabase Storage
- P3: Ad count "AN ad = 1" prompt enforcement

**Phase 3 — Welcome Audit + Cost Optimization**
- Built "Welcome Audit" one-click first-time user flow
- Cost optimization: MAX_ITERATIONS 30→20, parallel tool calls, combined phases
- Tested: 726 tokens ($7.26) for full audit. Within $20 budget with room for ad gen.
- New welcome screen with "Run my Welcome Audit" as #1 button

### Session 20 Detail

**Phase 1 — Data Accuracy (3 messages)**
- 7-day: Revenue ~7% low vs Shopify (B+). 30-day: Revenue ~37% low, 268 vs 424 orders (B). Meta ROAS: A+ (3.71x blended, 0.78x new customer).

**Phase 2 — Competitor Intel + 9-Ad Suite (5 messages)**
- 11 competitor images inline (A+). 9 ads across TOF (ODY-43), MOF (ODY-44), BOF (ODY-45), all 8-10/10 quality. Full-funnel launch plan with 50/20/30 budget split proactively included.

**Bugs found:** 30-day order gap (P1), orphaned conversation (P2, 2nd occurrence), Ad Library scan fails (P3).

---

## Architecture Reference

### The Offer as Atomic Unit
```
OFFER = Product(s) + Pricing + Incentive + Landing Page + Entry Points + Ads + Emails
Every campaign, ad set, ad, landing page, email, and metric traces back to an offer_id.
```

### Three Pillars
| Pillar | Source | What Improves It |
|--------|--------|-----------------|
| CPA | Meta Ads | Better ads, angles, targeting |
| AOV | Shopify | Better offers, bundles, upsells |
| LTV | Loop | Better retention, email flows |

### Cron Jobs
| Job | Schedule | What It Does |
|-----|----------|--------------|
| competitor-scrape | Mon 6 AM UTC | Scrapes Meta Ad Library |
| classify-ads | Mon 8 AM UTC | Vision-classifies new ads |
| watcher-health | Daily 8 AM UTC | Kill/scale check for all users |
| performance-sync | Mon 10 AM UTC | Updates performance insights |
| competitor-diff | Mon 10 AM UTC | Generates diff report |
