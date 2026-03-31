# Odyssey X — Living Roadmap

> **Last updated: 2026-03-31 (Session 18 Part 3 — Shopify Dev Dashboard, UX Fixes, QA)**
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
| Security Audit | ✅ Done | 20 issues fixed + rate limiting + CRON_KEY + webhook signature + RLS bypass |
| Navigation Redesign | ✅ Done | 2 main tabs (Chat/Planner) + sidebar sub-sections |
| Settings Redesign | ✅ Done | Sidebar nav (Billing/Integrations/Competitors/Account) |
| Watcher (Kill/Scale) | ✅ Done | Kill/scale/fatigue rules, 4 autonomy levels, cron |
| Self-Improving Prompts | ✅ Done | Meta performance → angle/format/persona winners injected into context |
| Landing Page Scanner | ✅ Done | 24h cache, headlines/CTAs injected into context |
| Ad Visual Diversity Engine | ✅ Done | Format/color overuse detection, diversity directive |
| Onboarding Flow | ✅ Done | 6-step wizard, persisted progress, OAuth detection |
| Model Toggle (Normal/Pro) | ✅ Done | Sonnet/Opus user-selectable, stored in profiles |
| Performance Dashboard | ✅ Done | Live Meta metrics page, per-offer breakdown |
| Account Settings | ✅ Done | Profile editing, password change, notification prefs |
| API Rate Limiting | ✅ Done | slowapi: 60/min default, 5/min signup, 10/min login, 3/min password reset |
| OAuth Onboarding Redirects | ✅ Done | OAuth callbacks redirect to /onboarding or /chat based on profile state |
| Google Signup | ✅ Done | Google Cloud OAuth + Supabase provider, zero code changes |
| Image Display in Chat | ✅ Done | Competitor ads + own brand ads render visually via markdown |
| Founder Adaptation | ✅ Done | Agent learns user preferences via preferences.md, adapts per founder |
| Live-First Analysis | ✅ Done | Agent prioritizes active ads, past winners as suggestions |
| Surrogate Encoding Fix | ✅ Done (v3) | v2 + 4 more sanitization points for DB-loaded conversation history |
| Em Dash Rule | ✅ Done | Added to orchestrator.md prompt + sandbox system prompt |
| Visual Ad Tools | ✅ Done | get_competitor_ads + image embedding in get_ad_level_performance |
| Planner Task Timeout | ✅ Done | Auto-fail after 30min, amber "Timed Out" badge + Retry button |
| Conversation History Loading | ✅ Done | isLoadingConversation state + race condition guard |
| Background Autonomy | 🟡 Partial | Weekly competitor scrape + watcher daily; planner task cards not auto-creating |
| Domain Migration | 🟡 Pending | Code supports tryodyssey.ai, nameserver swap pending |
| Visual Ads in Chat | 🟡 Needs Verify | Images may not render inline in chat markdown |

---

## What's Done (Sessions 3-18)

### Session 18 (2026-03-27) — Surrogates v3 + Live QA

#### P0 — Surrogate Encoding v3 (Root Cause Fixed)
- [x] **Root cause identified**: Assistant responses loaded from DB (containing raw Meta/Shopify tool result data) were never sanitized before being passed back to Anthropic API on subsequent turns. Turn 3 crash reproduced live at position 872-873 in `chat/router.py`.
- [x] **4 additional sanitization points** added in `chat/router.py` at lines ~408, ~704, ~721, ~904 — sanitize ALL message content pulled from DB before re-injecting into the Anthropic messages array.
- [x] **v2 fix confirmed working** for turn 1 and turn 2 via live browser testing on demo.runodyssey.io.

#### Live QA Results (Chrome Extension Method)
- [x] **Turn 1** (Shopify + Meta + competitors complex query): 7 agents, 42 steps, 164s. Stream completed. **BUG FOUND**: Response showed "Analysis saved" but no actual data displayed to user. Agent saves to brand brain but doesn't show the analysis.
- [x] **Turn 2** (follow-up for actual numbers): Worked perfectly. Beautiful tables with real data (products by revenue, ads by CPA, competitor intelligence with 183 classified ads). Surrogates v2 confirmed working.
- [x] **Turn 3** (ad generation): Crashed at position 872-873 with surrogates error. Triggered v3 fix.
- [x] **Em dash compliance**: No em dashes spotted in visible responses. Fix confirmed working.
- [x] **Browser testing method confirmed**: Chrome extension (Claude in Chrome MCP) creates own tab group, operates via DOM/extension API. Does NOT take over user's mouse/keyboard. This is the method for all future live QA.

#### New Bugs Found in Session 18
- [ ] **"Analysis saved" UX bug**: Agent saves analysis to brand brain but only says "Analysis saved" — doesn't display the actual analysis data to the user. Needs prompt fix in orchestrator.md.
- [ ] **Blank visual ad images**: "3 CREATIVES GENERATED" section appears in UI but images are blank (not loaded). Image rendering issue.
- [ ] **Slow competitor scan**: Competitor intelligence scan takes ~60+ seconds, causes long UI wait with no intermediate feedback.

### Session 17 (2026-03-26) — QA Bug Fixes + Visual Ads

#### Merged Branches
- **claude/quirky-poincare**: OAuth callback redirects (onboarding-aware), frontend alert() → inline toasts, onboarding OAuth notices, API rate limiting (slowapi), domain support for app.tryodyssey.ai, model toggle + account settings (Session 15)
- **claude/brave-goldwasser**: Surrogate encoding fix v1 — sanitize in router.py + executor.py, frontend receivedDone flag for stream state recovery

#### P0 Fixes (Critical)
- [x] **Surrogate encoding v2**: Sanitize BEFORE truncation in `chat/router.py`. Sanitize the initial message payload in `sandbox/executor.py`. Two-pass sanitization prevents crash on any non-standard unicode.

#### P1 Fixes (High)
- [x] **Em dash rule**: Added "NEVER use em dashes" directive to `orchestrator.md` prompt AND the sandbox system prompt injected at runtime. Double-enforcement.
- [x] **Visual ad creatives**: Registered two new tools in sandbox executor:
  - `get_competitor_ads` — queries `competitor_ads` table, returns permanent Supabase Storage image URLs
  - Updated `get_ad_level_performance` — embeds image URLs in response so agent can reference them
  - Updated Image Creative director prompt to always call `get_competitor_ads` before generating
  - Frontend gallery label updated for clarity

#### P2 Fixes (Medium)
- [x] **Planner task timeout**: Tasks auto-fail after 30 minutes. Amber "Timed Out" badge shown with Retry button. Prevents stuck tasks from staying in "Running" state forever.
- [x] **Conversation history loading fix**: `isLoadingConversation` state added to chat store. Race condition guard prevents new messages being sent while history is loading. Message input disabled while loading.

#### P3 Fixes (Low)
- [x] **Credit balance refresh on error**: Credit balance now refreshes even after failed API calls (so user sees correct balance without needing to reload).

### Session 16 (2026-03-26)
- [x] **OAuth Callback Redirects**: `get_user_redirect_base()` checks onboarding status; Shopify and Meta OAuth callbacks redirect to /onboarding or /chat accordingly.
- [x] **Frontend Alert Removal**: Replaced all `alert()` calls in sidebar.tsx and settings.tsx with inline toast notifications (5s auto-dismiss).
- [x] **Onboarding OAuth Notices**: Onboarding page reads URL params (?shopify=connected, ?meta=error) and shows success/error banners. URL cleaned after read.
- [x] **API Rate Limiting**: `rate_limit.py` module with slowapi. 60/min default, 5/min signup, 10/min login, 3/min forgot-password. Wired into FastAPI app state.
- [x] **Dependencies**: `slowapi==0.1.9` added to requirements.txt.

### Session 15 (2026-03-26) — QA Hardening + New Features

#### Gemini Vision Quality Check ✅
- [x] After ads generated, Gemini Vision scores each ad 0-100
- [x] Weak ads (score < 50) auto-regenerated (up to 2 retries per ad)
- [x] Quality score shown on ad cards in planner and cockpit view

#### Model Toggle (Normal / Pro) ✅
- [x] Normal mode: Claude Sonnet 4.6 in E2B sandbox
- [x] Pro mode: Claude Opus 4.6 in E2B sandbox (~5x cost, deeper reasoning)
- [x] Stored in `profiles.model_preference` (`sonnet` | `opus`)

#### Performance Dashboard ✅
- [x] Live Meta metrics for all active campaigns
- [x] Per-offer breakdown: spend, impressions, CTR, CPA, ROAS

#### Account Settings ✅
- [x] Profile editing (name, email), password change, notification prefs

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
- [x] **Landing Page Scanner**: `landing_page_scanner.py` — stdlib html.parser, zero new deps. 7-day cache, context injection. Migration 014.
- [x] **Ad Visual Diversity Engine**: Last 25 ads, overuse detection, DIVERSITY DIRECTIVE per batch.
- [x] **Onboarding Flow**: 6-step wizard, OAuth detection, progress persistence. Migrations 015+016.

### Session 13 (2026-03-25)
- [x] **Competitive Response Alerts**: diff_engine.py, Intel Update banner, weekly cron. Migration 013.

### Session 12 (2026-03-25)
- [x] **Self-Improving Prompts**: Performance learner, ODY naming parse, `performance_insights` table, context injection, weekly cron.

### Session 11 (2026-03-25)
- [x] **Watcher Agent**: 5 kill rules + 2 scale rules + fatigue. 4 autonomy levels. Daily cron. Undo support. WatcherBanner in Planner.

### Sessions 9-10 (2026-03-25)
- [x] **Billing**: Stripe PAYG, 5x markup, volume bonuses, Stripe Checkout
- [x] **Security Audit**: 20 issues fixed across codebase
- [x] **Smart Routing**: `needs_sandbox()` for fast path on simple questions
- [x] **Navigation Redesign**: Chat + Planner + sidebar sub-sections
- [x] **Intel Page**: Competitor analytics dashboard

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

### Priority 1: Fix Critical UX Bugs Found in Session 18 QA
- [ ] **Fix "Analysis saved" UX bug** — Agent saves analysis to brand brain but doesn't display it. Fix orchestrator.md prompt: after saving to brand brain, agent MUST also display the full analysis to user.
- [ ] **Verify surrogates v3 on turn 3+** — v3 fix committed and pushed. Run a full 3-turn conversation on demo.runodyssey.io to confirm no crash on turn 3.
- [ ] **Fix blank visual ad images** — "3 CREATIVES GENERATED" section appears but images are blank. Debug image URL rendering in the UI component.
- [ ] **Slow competitor scan** — ~60+ second wait with no feedback. Add intermediate progress events or stream the competitor scan results.

### Priority 2: Domain Migration (app.tryodyssey.ai)
- [ ] Nameserver swap (waiting on boss)
- [ ] Set up `app.tryodyssey.ai` as custom domain in Cloudflare Pages
- [ ] Add redirect URLs to Supabase auth config
- [ ] Update `FRONTEND_URL` in Railway when ready to make primary
- [ ] Post-migration verification (see DOMAIN_MIGRATION_CHECKLIST.md)

### Priority 3: Performance Dashboard Completion
- [ ] Wire per-offer ROAS/CPA/spend charts to live Meta data
- [ ] Add time-range selector (7d / 30d / 90d)
- [ ] Add "vs previous period" comparison
- [ ] Auto-update `offer_metrics_daily` from Meta (currently only on import)

### Priority 4: Background Autonomy Completion
- [ ] Daily health check: auto-create task card in Planner each morning
- [ ] Weekly batch planning: agent drafts strategy for user to approve
- [ ] All background work shows in Planner as task cards (not just Watcher banner)
- [ ] "While You Were Gone" morning briefing format in chat
- [ ] Supabase Realtime: replace planner polling with live subscriptions

### Priority 5: Real Competitor Ad Images
- [ ] Wire Apify or SearchAPI for US commercial Meta ad images
- [ ] Replace expired CDN URLs with permanent Supabase Storage images
- [ ] `get_competitor_ads` tool already built — just needs real image source

### Priority 6: Pre-Launch Polish
- [x] Rate limiting: 60/min default, 5/min signup, 10/min login, 3/min password reset
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

### Active Issues (New — Found in Session 18 Continued QA)
- [ ] **"Analysis Saved" evasion (persistent)**: Guard catches most variants but agent finds new phrasings. Current patterns cover "full report is above", "already included", "save_brand_brain", etc. May need a deeper prompt fix. Response DOES render now but is sometimes a summary-only instead of full data.
- [x] **Competitor intel `get_competitor_ads` column bugs**: Tool was selecting `days_running` (doesn't exist) and `ad_copy` (doesn't exist). Both fixed — now uses `days_active`, `primary_text`. Also added `ad_classification` JSONB fallback for angle/format/awareness. **FIXED** commit 0de0292.
- [x] **`get_competitor_ads` individual records not loading**: Root cause was column name mismatch (`days_running` doesn't exist in DB). Fixed to use correct columns. All 185 competitor ads now return with valid image URLs (Supabase Storage, permanent). **FIXED** commit 0de0292.
- [x] **Images not showing in chat**: Added IMAGE DISPLAY RULE to system prompt — agent must embed `![Brand — Angle](image_url)` markdown for competitor and own brand ads. Frontend renders natively. Tool result also returns `note` reminding agent to use image markdown. **FIXED** commit 0de0292.
- [ ] **`days_active` = 0 for all competitor ads**: Scraper sets `days_active=0` on all inserts. `first_seen_at` = `last_seen_at` = scrape date so duration is always 0. Need to update scraper to track re-sighted ads and increment `days_active`. Also affects `is_likely_winner` (always false).
- [ ] **Blank visual ad images**: "3 CREATIVES GENERATED" section shows but images don't load. UI rendering bug.
- [ ] **Slow competitor scan**: ~60+ seconds with no intermediate feedback. Needs progress streaming.

### Active Issues (New — Found in Session 18 QA)
- [x] **Blank response (rendering bug)**: Root cause found — E2B delivers stdout line-by-line via WebSocket. Long response JSON (5K+ chars) gets fragmented, json.loads fails, done event lost, blank chat. Fixed by chunking response into 400-char response_chunk events. **FIXED** in commit 37bc952.

### Active Issues (Confirmed via v3 Fix — Needs Verification)
- [ ] Surrogate encoding v3: fix committed and pushed. Needs live verification on turn 3+ conversation.
- [ ] Competitor ad images from Meta Ad Library not yet wired (Apify/SearchAPI needed)

### Active Issues (Known Open)
- [ ] Sandbox crashes intermittently on first message ("peer closed connection" — E2B cold start)
- [ ] Normal mode runs full E2B sandbox (may want lighter path for quick questions)
- [ ] Response length inconsistency across scenarios (some too short, some too long)
- [ ] Team persona inconsistency — agent sometimes says "I" instead of using team member names
- [ ] No "While You Were Gone" morning briefing format yet
- [ ] No Supabase Realtime subscriptions (polling only, 10-30s intervals)
- [ ] `offer_metrics_daily` only populated on import, not auto-updated from Meta
- [ ] Facebook CDN image URLs in old `generated_assets` have expired
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads
- [ ] Sandbox max_steps=10 may be insufficient for complex workflows (should be 16-20)
- [ ] No per-user token spending cap per action (agent could burn all credits in one run)
- [ ] Background autonomy: planner task cards not auto-creating from daily checks
- [ ] "make me AN ad" sometimes still generates 3 (prompt parsing not perfect)

### Fixed (Sessions 15-18 Continued)
- [x] **Blank response (E2B line limit)**: Response emission chunked to 400-char response_chunk events. Router accumulates chunks and forwards as text SSE. No more silent lost done events.
- [x] **Active campaign awareness**: Agent now checks effective_status, identifies offer swaps (PAUSED = old offer), focuses analysis on ACTIVE campaigns only.
- [x] **MAX_ITERATIONS safety net**: Loop exhausted without end_turn now emits done event instead of silent failure.
- [x] **manager.py silent exception**: Sandbox event parse failures now logged (were silently dropped).
- [x] **Analysis Saved guard (programmatic)**: Short "saved"-pattern responses trigger a one-shot rewrite. Guard has infinite-loop protection (_rewrite_attempted flag). Patterns expanded: full report is above, already included, save_brand_brain, response above, etc.
- [x] Surrogate encoding v1 + v2 + v3 (sanitize before truncate, sanitize initial payload, sanitize all DB-loaded history messages)
- [x] Em dash rule added to orchestrator.md + sandbox system prompt
- [x] Visual ad tools registered (get_competitor_ads + updated get_ad_level_performance)
- [x] Planner task timeout (auto-fail 30min, amber badge, Retry button)
- [x] Conversation history loading race condition
- [x] Credit balance refresh on error
- [x] Rate limiting added: 60/min default, 5/min signup, 10/min login, 3/min forgot-password
- [x] OAuth callbacks are onboarding-aware (redirect to /onboarding or /chat)
- [x] Frontend alert() calls replaced with inline toasts
- [x] Onboarding page shows OAuth connection notices from URL params
- [x] All 7 DB migrations (011-017) confirmed applied
- [x] Model toggle (Normal/Pro) working
- [x] Account settings (profile edit, notification prefs) working
- [x] RLS blocking backend reads (service-role client)
- [x] Per-brand competitor data in business context
- [x] UTF-8 surrogate crash
- [x] Stripe webhook signature not verified
- [x] CRON_KEY not enforced on cron endpoints
- [x] API overload no retry logic
- [x] Normal mode incorrectly bypassing sandbox
- [x] 13 QA bugs (see Session 15 above)

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
- **13 QA bug fixes**: Heartbeat keepalive, per-tab conversation lock, balance display, em dash scrub, Bloom fix, Shopify date filter, watcher banner polling, and more.
- **Security hardening**: Stripe webhook signature, CRON_KEY enforcement.
- **Context enrichment**: Full business context in every sandbox run.
- **Migrations**: 016 (onboarding RPC grants) + 017 (model_preference) — both applied.

### 2026-03-26 (Session 16)
- **API Rate Limiting**: `rate_limit.py` with slowapi. 5/min signup, 10/min login, 3/min forgot-password, 60/min default.
- **OAuth Onboarding Redirects**: `get_user_redirect_base()` in dependencies.py. Shopify + Meta OAuth callbacks redirect to /onboarding or /chat.
- **Inline Toasts**: All `alert()` calls in sidebar.tsx and settings.tsx replaced with glass-style inline notifications.
- **Onboarding OAuth Notices**: Onboarding page handles ?shopify= / ?meta= URL params with success/error banners.

### 2026-03-26 (Session 17)
- **Merged** claude/quirky-poincare (Session 16 work) + claude/brave-goldwasser (surrogate fix v1) to main
- **Surrogate encoding v2**: Two-pass sanitization — sanitize before truncate in router.py, sanitize initial payload in executor.py. Eliminates surrogate crash on non-standard unicode.
- **Em dash enforcement**: "NEVER use em dashes" rule added to orchestrator.md + sandbox system prompt. Double-enforcement.
- **Visual ad tools**: `get_competitor_ads` tool queries competitor_ads table with permanent image URLs. Image Creative director prompt updated. `get_ad_level_performance` updated with image embedding.
- **Planner task timeout**: Auto-fail after 30min. Amber "Timed Out" badge + Retry button. Prevents infinite stuck tasks.
- **Conversation history loading**: `isLoadingConversation` flag + race condition guard. Input disabled during history load.
- **Credit balance on error**: Balance refreshes even after failed API calls.

### 2026-03-31 (Session 18 Part 3) — Shopify Dev Dashboard, UX Fixes, QA

#### Shopify Dev Dashboard Connection Flow
- **Built POST /connect-manual endpoint**: Validates token with test API call to /shop.json, checks scopes, encrypts + stores, kicks off background sync. All existing OAuth endpoints untouched.
- **ShopifyConnectGuide component**: 5-step accordion guide with scope pills, token warning, paste fields. Used in both onboarding and settings.
- **Expanded Shopify scopes**: Added read_analytics, read_reports, read_content, read_themes, read_price_rules, read_discounts, read_marketing_events, read_inventory, read_checkouts (13 total).

#### Onboarding Redirect Loop Fix
- **Root cause**: After completing onboarding, AuthGuard checked `user.onboarding_completed` in Zustand store which was still `false` (never updated). Caused infinite loop: /chat → AuthGuard → /onboarding → API says completed → /chat → loop.
- **Fix**: Update `useAuthStore` with `onboarding_completed: true` BEFORE navigating to /chat.

#### UX Improvements (All Verified Live)
- **Inline thinking status**: Chat bubble shows "Checking your ad campaigns" → "Pulling ad performance data" → "Saving insights for next time" instead of static "ODY-1 is thinking..."
- **No more empty chats**: "+ New Chat" just shows welcome screen. Conversations created lazily on first message.
- **Prompt suggestions work**: Click sends message immediately (was already wired but + New Chat was interfering).
- **Human-readable tool names**: `get_meta_campaigns` → "Checking your ad campaigns", `save_brand_brain` → "Saving insights for next time"
- **Cleaner Team Activity**: "Loaded 7 brand brain files" → "Remembering your brand context (7 files)"

#### Live QA Tests (3 Scenarios, All Passing)
1. "What should I focus on to grow revenue?" — 6 agents, 29 steps, 96.8s. Business metrics table, priority-ranked action plan, Three Pillars scorecard. A+ quality.
2. "How much did I spend on ads this week?" — 5 agents, 19 steps, 32.5s. Campaign table, frequency warning, forward projection. Fast and direct.
3. "Write me 5 scroll-stopping ad headlines for HashiAid" — 3 agents, 22 steps, 39.4s. Table with Headline/Angle/Funnel/Format/Why It Works. Strategically avoided overused angles. A+ quality.
4. "Give me a full analysis of my store and ads" — Prompt suggestion click test. Live-first analysis ("SECTION 1: WHAT'S LIVE RIGHT NOW") with status indicators (✅⚠️🔴). All UX fixes confirmed.

### 2026-03-30 (Session 18 Continued Part 2) — Images, Founder Adaptation, Google Signup

#### Google Signup
- **Google OAuth configured**: Google Cloud Console OAuth app + Supabase provider enabled. Zero code changes — frontend buttons, callback handler, and profile trigger were already built. Tested end-to-end: Google login → onboarding flow → chat. Working.

#### Founder Adaptation System
- **preferences.md brain file**: Agent now learns each founder's communication style, decision patterns, risk tolerance, and preferred formats over time. Reads at session start (Phase 0), saves observations silently after meaningful interactions.
- **Live-first analysis rule**: Agent prioritizes active/spending ads (70% of response), past winners as suggestions (20%), key takeaways (10%). Paused ads are context, not priorities.
- **All system prompt rules framed as defaults**: Diagnostic format, image display, live-first analysis are defaults for new users that adapt as agent learns the founder.

#### Column Bug Fix + Image Display
- **Root cause of `get_competitor_ads` looping**: Tool queried `days_running` and `ad_copy` — neither column exists in DB. Fixed to `days_active` and `primary_text`. Also added `ad_classification` JSONB fallback for angle/format/awareness.
- **IMAGE DISPLAY RULE added**: System prompt now requires agent to embed `![Brand — Angle](image_url)` markdown for all ads. Tool result also returns reminder note.
- **Live QA verified**: Both tests passed end-to-end on demo.runodyssey.io:
  1. **Competitor ads**: "Show me what my competitors are doing" — 183 classified ads across 12 brands, brand-by-brand breakdown (Seed, AG1, Bloom, Momentous, Ritual, Thorne), competitive moat analysis, intercept strategy, **25 ad images rendered in 3-column grid**. 5 agents, 32 steps, 118.9s.
  2. **Own brand ads**: "Show me my best performing ads with their images" — 8 ads ranked by CPA, performance summary table, KEY TAKEAWAYS (50/50 bottle is winner at $30.97 CPA, kill "2 img box"), **own ad creative images rendered** (testimonials, product shots, review cards). 5 agents, 24 steps, 197.2s.
- **All 185 competitor ads have permanent Supabase Storage image URLs** — no expired CDN links.
- **Own brand ads use Meta thumbnail URLs** — valid at query time, rendered inline.

### 2026-03-30 (Session 18 Continued) — Blank Response Root Cause + Phase 4 QA

#### Root Cause Fix: Blank Responses
- **Root cause identified**: E2B delivers sandbox stdout line-by-line over WebSocket. A long competitor analysis response (~5K chars) produces one massive JSON line that gets fragmented. `json.loads` fails silently in `manager.py`, `done` event lost, `full_response = ""`, blank chat. This explained why morning briefings (short) worked but competitor intel (long) was blank.
- **Fix**: Sandbox now emits `response_start` + multiple `response_chunk` events (400 chars each) + bare `done` signal. Router handles `response_chunk` events, accumulates `full_response`, forwards as text SSE events. 5 commits: `ffc55b0`, `dd60437`, `3090a66`, `37bc952`, `ab12041`, `8005b04`.

#### Phase 4 QA Tests Run
- **Competitor intel** ("Show me what my competitors are doing..."): Response NOW RENDERS. Agent gave useful insight ("you have a massive competitive moat"). Still shows Analysis Saved pattern with "full report is above" — new guard patterns added to catch this.
- **CPA spike diagnostic** ("My CPA spiked this week..."): FULLY WORKING after multiple rounds of fixes. Final response: full table ($439.69 spend, 10 purchases, $43.97 CPA vs $51 target), ad-level breakdown naming "50/50 bottle" as winner ($30.97 CPA, 60% of purchases), 2 dead ads with exact wasted spend, frequency 2.81 approaching danger zone, priority action list with time estimates. Production-quality output.

#### UX Improvements Observed (To Implement)
1. **Agent loops on failed tool**: `get_competitor_ads` called 8-12x when individual records are empty. Tool should return explicit "no records, use aggregate" and agent should stop.
2. **"Full report is above" / "Already included above"**: Agent thinks earlier thinking blocks are visible. Guard now catches these but root fix is a stronger system prompt: "ONLY your final text message is visible. Nothing from before save_brand_brain."
3. **No "what happened" answer in CPA query**: Agent jumped straight to "what to do" without explaining root cause first. User asked "what happened" and got a to-do list.
4. **~90-110 second response time**: No chat-area progress indicator during the wait. Terminal panel is good but the message bubble itself just shows "thinking..." dot. Could show "Analyst is checking your campaigns..." in the bubble.
5. **Tool names leaked in chat**: Agent said "above the save_brand_brain call" — internal tool names should never appear in user-facing responses.
6. **Credit balance drop visible**: Charges showing correctly (~200 tokens per analysis), good.

### 2026-03-27 (Session 18)
- **Live QA via browser testing** (Chrome extension method): Full 3-turn conversation on demo.runodyssey.io with real Shopify + Meta account.
  - Turn 1: 7 agents, 42 steps, 164s — stream completed but "Analysis saved" UX bug found
  - Turn 2: Perfect — beautiful tables with real data, surrogates v2 confirmed working
  - Turn 3: Crashed with surrogates at position 872-873 — triggered v3 fix
- **Surrogate encoding v3**: Root cause found — DB-loaded conversation history (assistant messages containing raw Meta/Shopify tool results) never sanitized before re-injection into Anthropic API. Added 4 new sanitization points in chat/router.py (~lines 408, 704, 721, 904). Committed and pushed to main.
- **Em dash confirmed working**: No em dashes observed in live responses.
- **New bugs catalogued**: "Analysis saved" UX bug, blank visual ad images, slow competitor scan (~60s).

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

---

## What To Do Next (Session 19)

### P0 — Fix `days_active` = 0 for competitor ads
The scraper sets `days_active=0` on all inserts because `first_seen_at = last_seen_at = scrape timestamp`. Need to:
1. On re-scrape, update `last_seen_at` for existing ads (match by `meta_ad_id`)
2. Calculate `days_active = last_seen_at - first_seen_at` via pg trigger or scraper update
3. Set `is_likely_winner = true` when `days_active >= 30`
This affects the learning loop — can't distinguish winner ads without duration data.

### P1 — Test Shopify Dev Dashboard connection flow end-to-end
The manual connect flow is built (POST /connect-manual, ShopifyConnectGuide component) but untested with a real Shopify store. Need to:
1. Open onboarding on a fresh account → verify the 5-step guide renders
2. Create a custom app on a dev store following the guide
3. Paste credentials → verify token validation, encryption, sync
4. Also test in Settings page (expandable card)
5. Take Dev Dashboard screenshots for `/public/guides/shopify/step{1-5}.png`

### P2 — UX: Follow-up action buttons below responses
After the agent suggests something ("Want me to create ads for that?"), show 1-2 clickable action buttons below the response. This makes conversation flow natural instead of requiring the user to type.

### P3 — UX: Copy button on responses
Founders will want to copy ad headlines, tables, or specific text. Add a copy-to-clipboard button on hover over each response.

### P4 — Apple signup
Google is live. Add Apple sign-in for full OAuth coverage. Similar process: Apple Developer Console + Supabase provider config.

### P5 — Marketing site / landing page
Now that the product is working (ads, images, competitor intel, Google auth, onboarding), need a public-facing marketing site at runodyssey.io for signups.

### P4 — Landing page / marketing site
Now that the product is working (ads, images, competitor intel, Google auth, onboarding), need a public-facing marketing site at runodyssey.io for signups.
