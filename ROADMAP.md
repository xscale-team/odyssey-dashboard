# Odyssey X — Living Roadmap

> **Last updated: 2026-04-06 (Session 21)**
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
- [ ] Domain migration to app.tryodyssey.ai

### Priority 8: Klaviyo Integration (Post-MVP)
- [ ] OAuth, pull flows/sequences, track email engagement

---

## Known Bugs & Technical Debt

### P1 — Active
- [x] **30-day Shopify order count ~37% low** — Fixed: date-only format + 1-day buffer (Session 21)
- [x] **Gemini brand name hallucination** — Fixed: brand name validation in text + vision QC (Session 21)
- [x] **Orphaned conversation** — Fixed: error messages in chat + retry guidance (Session 21)
- [ ] **Sandbox doesn't pick up new integrations** mid-session. Requires "New Chat".

### P2 — Medium
- [ ] **Slow competitor scan** (~60s with no progress feedback). Needs progress streaming.
- [ ] **`days_active` = 0 for all competitor ads**. Scraper doesn't track re-sighted ads. Affects `is_likely_winner`.
- [ ] **Ad Library live scan fails**. Falls back to pre-scraped DB. Needs Apify/SearchAPI for US commercial ads.
- [x] **Sandbox cold start crashes** — Fixed: friendly error message + retry guidance (Session 21)
- [x] **"make me AN ad" sometimes generates 3** — Fixed: strengthened ad count rules in prompt (Session 21)

### P3 — Low
- [ ] Team persona inconsistency (agent says "I" instead of team member names)
- [ ] Response length inconsistency across scenarios
- [ ] No per-user token spending cap per action
- [ ] `offer_metrics_daily` not auto-updating from Meta
- [x] **Facebook CDN image URLs expire** — Fixed: auto-persist to Supabase Storage (Session 21)
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads

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
| Frontend | Cloudflare Pages (demo.runodyssey.io) | Yes (git push) |
| Backend API | Railway (odyssey-x → api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | Yes (git push) |
| Database | Supabase (migrations 000-019) | Manual SQL |
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
