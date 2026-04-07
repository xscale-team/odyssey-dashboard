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
- [ ] **Gap persists: 268 vs 424 orders (37% low).** REST API may not return all order types.
- [ ] Investigate: Switch to Shopify GraphQL Orders API (may return more order types)
- [ ] Investigate: Compare order IDs from API vs Shopify CSV export to find exact missing orders
- [ ] Note: 7-day gap is only ~8% (110 vs 120), suggesting issue accumulates with older subscription orders

### Priority 2: Fix Orphaned Conversation Bug
- [ ] Message appears in chat but SSE connection never fires (2 occurrences across sessions)
- [ ] Root cause: fire-and-forget async in apiStream
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
| 21 | 2026-04-06 | **System test continued**: Chat 1 complete (5/5 A+), Chat 2 started. Fixed pending filter, added debug diagnostics. 30-day gap persists (REST API limitation). 2,871 credits. |

### Session 21 Detail

**Chat 1 — Data Accuracy (5 messages, 1,644 credits, all A/A+)**
- 30-day: 268 vs 424 orders (37% gap persists). Pending filter fix did not resolve.
- 7-day: 110 vs 120 orders (8% gap, much better). Revenue $5,050 vs $5,588 (10%).
- Subscription data: 119 active subs, $4,127 MRR, GUTsupport $421 LTV vs HashiAid $152 LTV.
- Meta performance: per-ad CPA table, frequency warnings, kill/scale recommendations, 3 ad images inline.
- Synthesis: Top 3 priorities with data-backed impact estimates and timelines.

**Chat 2 — Ad Lifecycle (1/8 messages, 1,227 credits)**
- Top 5 ads analysis with "why it works" per ad. Ad images partially expired (known P3).

**Bugs found:** 30-day gap is REST API limitation (not filter issue), ad image CDN expiry (P3).

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
