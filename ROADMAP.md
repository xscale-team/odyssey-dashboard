# Odyssey X — Living Roadmap

> **Last updated: 2026-03-25 (Session 14)**
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
9. ⬜ Agent monitors performance daily, kills losers, scales winners (Watcher agent)
10. ⬜ Agent proactively plans next batch based on performance + competitor data
11. ⬜ Guided onboarding flow

---

## Quick Status

| Area | Status | Notes |
|------|--------|-------|
| Integrations (Shopify/Meta/Loop) | ✅ Done | All 3 connected, syncing data |
| Ad Generation (Gemini AI) | ✅ Working | 10 formats, product composition |
| Ad Copywriter (Sonnet 4.6) | ✅ Working | 5 primary texts + 5 headlines per ad |
| Ad Launch Pipeline | ✅ Done | Approve, push to Meta, New Batch / Add to Batch, CBO detection |
| Offer Discovery + Funnel | ✅ Done | Entry points, funnel flow, thumbnails |
| Entry Point Auto-Detection | ✅ Done | Sync from Meta, auto-detect active ad destinations |
| Subscription Metrics (Loop) | ✅ Done | LTV, churn, MRR per selling plan |
| Three Pillars (CPA/AOV/LTV) | ✅ Live | All 3 showing real data |
| Competitor Intelligence | ✅ Done | 185 ads scraped, classified, permanent storage, weekly auto-refresh |
| Intel Dashboard | ✅ Done | Standalone page with angles, formats, hooks, brand cards |
| Billing (Stripe) | ✅ Done | PAYG tokens, 5x markup, real cost tracking, Stripe Checkout |
| Security Audit | ✅ Done | 20 issues fixed (sandbox injection, auth leakage, CORS, etc.) |
| Navigation Redesign | ✅ Done | 2 main tabs (Chat/Planner) + sidebar sub-sections |
| Settings Redesign | ✅ Done | Sidebar nav (Billing/Integrations/Competitors/Account) |
| Watcher (Kill/Scale) | ✅ Done | Kill/scale/fatigue rules, 4 autonomy levels, cron |
| Background Autonomy | 🟡 Partial | Weekly competitor scrape + classify auto-scheduled |
| Onboarding Flow | ✅ Done | 6-step wizard, persisted progress, OAuth detection |

---

## What's Done (Sessions 3-9)

### Session 9 Highlights (2026-03-25)

#### Billing System (Stripe Integration) ✅
- [x] Real USD cost tracking with 5x markup (actual Claude/Gemini/E2B costs)
- [x] Pay-as-you-go tokens (1 token = 1 cent)
- [x] Stripe Checkout for one-time top-ups ($10/$25/$100/$200/$500)
- [x] Volume bonus tokens: +20% at $100, +40% at $200, +50% at $500
- [x] Decoy pricing psychology ($200 "BEST VALUE" hero card)
- [x] Stripe webhook for auto-crediting balance after payment
- [x] Real-time balance pill in header (green/yellow/red)
- [x] Low balance warning in Chat (< 100 tokens)
- [x] Balance deducted after chat, ad batch, and campaign launch
- [x] Usage dashboard: 30-day breakdown by action type + recent transactions

#### Navigation Redesign ✅
- [x] Consolidated to 2 main tabs: Chat + Planner
- [x] Planner sidebar: Tasks, Offers, Team, Intel, Performance
- [x] Sidebar labels with icons
- [x] Removed cluttered top-level tabs

#### Intel Dashboard (Standalone Page) ✅
- [x] Niche overview: total ads, active, turned off, likely winners
- [x] Top angles bar chart with winner rates
- [x] Format distribution grid (full names, not codes)
- [x] Awareness mix (TOF/MOF/BOF cards)
- [x] Top hooks list with copy-to-clipboard
- [x] Winning combos section
- [x] Expandable brand cards with per-brand stats
- [x] "What's Working" top ads with thumbnails
- [x] "Use This" button navigates to Chat with recreation prompt

#### Settings Redesign ✅
- [x] Sidebar navigation: Billing, Integrations, Competitors, Account
- [x] Compact balance card (text-4xl, not text-6xl)
- [x] Beautiful token pricing grid with gradient borders
- [x] Account section with email + logout

#### Planner Redesign ✅
- [x] 3-column Kanban: Needs Attention / Active / Done
- [x] Batch cards with number, angle, persona, awareness badges
- [x] Approve / Decline / Launch buttons per batch
- [x] Done column: compact cards, green border, completion time
- [x] Active column: inline progress bars
- [x] Expired image placeholders (gradient + icon)

#### Chat Improvements ✅
- [x] Sidebar: chat titles auto-named, timestamps, time grouping (Today/Yesterday/Older)
- [x] Delete conversations with trash icon
- [x] Max 20 visible, "Show N more" toggle
- [x] Claude 529 Overloaded error: auto-retry with 5s delay
- [x] Real-time token balance update after actions

#### Entry Point System ✅
- [x] Auto-detect entry points from ACTIVE Meta ad sets
- [x] Quiz entry point detection (quiz.ibdassist.com)
- [x] "Sync from Meta" button on offer detail page
- [x] "Add Entry Point" button with ad set picker
- [x] Active-only filtering (effective_status, not just status)
- [x] Agent reads entry point data before generating ads

#### Security Audit + Fixes (20 issues) ✅
- [x] CRITICAL: Sandbox code injection fix (json.dumps escaping)
- [x] CRITICAL: Auth info leakage (generic error messages)
- [x] HIGH: Error sanitization across 6 router files (17 instances)
- [x] HIGH: CORS headers explicit allowlist (no wildcard)
- [x] HIGH: FORBIDDEN rules in agent orchestrator prompt
- [x] CRITICAL: Hardcoded Supabase project ID in frontend
- [x] HIGH: window.location.reload replaced with state refreshes
- [x] MEDIUM: Lazy loading on images, alt text, memoization
- [x] MEDIUM: Console.error removed from production stores
- [x] MEDIUM: Rate limit cooldown (60s) on sync button
- [x] CRITICAL: Missing Shopify tables migration
- [x] CRITICAL: Column mismatch in migration 009
- [x] MEDIUM: Performance indexes on user_id+status composites

#### Ad Generation Improvements
- [x] Entry point awareness (agent checks which EPs are active)
- [x] Auto-match to ACTIVE ad sets (not just any ad set)
- [x] ACTIVE ad sets sort to top in manual picker
- [x] Batch history tracking (avoid repeating same angles)
- [x] Competitor context injected into generation prompt

### Previous Sessions (3-8)

#### Ad System
- [x] Replaced Pillow renderer with Gemini AI full composition
- [x] Product images sent to Gemini for natural composition
- [x] 10 ad format prompts (problem_callout, testimonial, offer, hero_product, us_vs_them, ingredient_breakdown, advertorial, before_after, native_meme, lifestyle)
- [x] Angle selection guardrails
- [x] verify_with_user tool for agent decision points
- [x] Ad naming convention: `ODY-{batch}.{num} | {Angle} | {Persona} | {Awareness} | {Format} | {Hook}`

#### Ad Copywriter
- [x] Sonnet 4.6 generates 5 primary text + 5 headline variations per ad
- [x] Tailored to angle, persona, awareness level, and format
- [x] No em dashes (global rule)
- [x] Fallback copy if AI generation fails

#### Ad Launch Pipeline
- [x] Full approve, push to Meta flow
- [x] New Batch or Add to Batch modes
- [x] CBO detection (auto-detects Campaign Budget Optimization)
- [x] Destination URL copied from reference ad set
- [x] ODY naming convention enforced

#### Competitor Intelligence System ✅
- [x] Full scraping pipeline: E2B + Playwright, Ad Library, Supabase Storage
- [x] 185 ads scraped across 10 supplement brands
- [x] Sonnet classifier: format, awareness, angle, hook
- [x] Delta detection: turned off ads tracked with timestamps
- [x] Winner scoring (0-100 scale)
- [x] Auto-schedule: pg_cron weekly scrape + classify
- [x] Permanent image storage in Supabase Storage

#### Offers System
- [x] Auto-discover from Meta Ads by destination URL
- [x] Funnel flow visualization
- [x] Entry points with per-EP spend/ROAS/CPA/ad count
- [x] Loop subscription metrics per offer

#### Security & Stability
- [x] All tokens encrypted at rest
- [x] RLS on all tables
- [x] SECURITY DEFINER functions for competitor writes
- [x] Meta tokens in Authorization headers

---

## What's Next -- Priority Order

### Priority 0: Post-Watcher Deploy Steps (Immediate)
- [ ] Run `011_watcher_agent.sql` migration in Supabase dashboard
- [ ] Set `CRON_KEY` env var in Railway (for `/api/watcher/cron` security)
- [ ] Set up pg_cron or Railway cron: `POST api.runodyssey.io/api/watcher/cron` at 8 AM UTC daily

### Priority 1: Watcher Agent (Kill/Scale/Monitor) ✅ DONE
Daily monitoring that takes action based on user's autonomy level.

- [x] **Daily health check job**: scan all active ad sets, pull 7-day metrics
- [x] **Kill rules** (5 rules from ad-monitor.md):
  - Emergency kill: CPA > 3x target in first 48h + $50 spend
  - CPA death spiral: CPA > 2x target + 1000 impressions + 3 days
  - Zero conversions: $0 revenue after 3x target CPA spend
  - CTR floor: CTR < 0.8% + 2000 impressions + 3 days
- [x] **Scale rules** (2 rules):
  - Steady winner: ROAS > target for 5+ days + 15 conversions + CPA 20%+ below target
  - Micro-scale: ROAS > target + 3+ days (10% budget increase)
- [x] **Fatigue detection**: frequency creep, CTR decline, CPA creep
- [x] **Autonomy levels**: supervised / guided / autonomous / full_auto
- [x] Autonomy level stored in `user_preferences` table
- [x] Statistical significance gates (min $50 spend, min impressions, min days)
- [x] "While you were gone" shown in Planner Watcher Banner
- [x] Undo action endpoint (re-activate paused ad sets)
- [x] Daily cron via `/api/watcher/cron` (pg_cron or Railway cron)
- [x] Background task processor handles `health_check` task type
- [x] DB migration: `user_preferences`, `watcher_reports`, `watcher_actions`

### Priority 2: Post-Generation Quality Check
Reviewer agent that checks ad quality before presenting to user.

- [ ] After ads generated, reviewer scores each for:
  - Angle/persona match to brief
  - Repetition against past batches
  - Visual diversity (different colors, layouts)
  - Copy quality (no em dashes, proper CTA, landing page match)
- [ ] Weak ads auto-regenerated before presenting
- [ ] Quality score shown on each ad card

### Priority 3: Self-Improving Prompts ✅ DONE
Agent learns from real performance data over time.

- [x] After campaign sync, extract insights from Meta performance
- [x] Correlate ad names (ODY naming convention) with CPA/ROAS/CTR
- [x] `performance_insights` table: stores angle/format/persona/awareness winners + losers
- [x] Performance-based prompt injection: agent reads insights before every generation
- [x] Track which angles/formats/personas produce best results per brand
- [x] `POST /api/integrations/meta/sync-insights` — on-demand refresh
- [x] `GET /api/integrations/meta/insights` — view stored insights
- [x] `POST /api/watcher/cron/sync-insights` — weekly cron for all users
- [x] `performance_sync` background task type in worker
- [x] DB migration: `012_performance_insights.sql` (run in Supabase dashboard)

### Priority 4: Competitive Response Alerts ✅ DONE
Automated insights from weekly competitor scrapes.

- [x] Compare each week's scrape against previous week (diff_engine.py)
- [x] Detect: new ads launched, ads killed, format shifts, angle changes
- [x] Auto-generate alerts: "AG1 launched 5 new Ingredient Breakdown ads"
- [x] Suggest responses: "Your competitors are shifting to IB format. Consider testing it."
- [x] Show in Intel tab as collapsible "Intel Update" banner
- [x] Weekly cron: Monday 10 AM UTC (after scrape at 6am, classify at 8am)
- [x] DB migration: 013_competitor_diff_reports.sql (run in Supabase dashboard)

### Priority 5: Landing Page Scan Before Ad Generation ✅ DONE

- [x] `landing_page_scanner.py`: fetches URL with httpx, parses HTML with stdlib html.parser
- [x] Extracts: headline (h1), subheadlines (h2), meta description, CTA texts, key phrases, price signals
- [x] Cache: `landing_page_cache` table (URL-unique, 7-day TTL, service-role upsert)
- [x] `014_landing_page_cache.sql`: table + RLS + cleanup function
- [x] `_build_business_context()` injects LANDING PAGE INTELLIGENCE section for each active offer
- [x] Agent sees exact page headlines, CTAs, and brand vocabulary before generating ads
- [x] Only cache successful scans; failed scans (timeout, 403) retry next time

### Priority 6: Ad Visual Diversity Improvement ✅ DONE

- [x] Replaced basic launched-ad summary with full Creative Diversity Report
- [x] Pulls last 25 ads (all statuses: published + draft/approved) for comprehensive picture
- [x] Counts format/color_scheme/funnel/awareness per attribute using Counter
- [x] Classifies as OVERUSED (>45% of batch) and surfaces UNTESTED attributes
- [x] Checks funnel distribution imbalance (heavy TOF or BOF)
- [x] Blocks last 6 headlines from being reused verbatim
- [x] Generates specific DIVERSITY DIRECTIVE per batch ("avoid dark; try clinical")
- [x] Agent explicitly told which formats/colors to avoid vs. try

### Priority 7: Onboarding Flow ✅ DONE

- [x] `015_onboarding.sql`: adds onboarding columns to profiles, complete_onboarding_step() RPC (state machine), get_onboarding_state() RPC
- [x] `GET /api/auth/onboarding` + `POST /api/auth/onboarding/complete-step` endpoints
- [x] `/me` endpoint derives onboarding_completed from legacy boolean OR new completed_at
- [x] `frontend/src/pages/onboarding.tsx`: 6-step wizard with progress bar
  - Step 1: Welcome (team grid + Three Pillars intro)
  - Step 2: Connect Shopify (OAuth or skip, auto-detects if already connected)
  - Step 3: Connect Meta Ads (OAuth or skip, auto-detects)
  - Step 4: Create first offer (name + landing page URL)
  - Step 5: Competitor intelligence (shows tracked brands, info screen)
  - Step 6: Set autonomy level (saves to watcher preferences)
- [x] Progress persisted to backend after each step (resumes on reload)
- [x] All steps skippable (user never gets stuck)
- [x] auth-guard.tsx: redirects unonboarded users to /onboarding
- [x] App.tsx: /onboarding route registered
- [x] Supabase migrations: `014_landing_page_cache.sql` + `015_onboarding.sql` (run manually)

### Priority 5 (original): Background Autonomy
Agent creates its own work without being asked.

- [ ] **Daily health check**: auto-created task every morning
- [ ] **Weekly batch planning**: draft strategy for user to approve
- [x] **Weekly competitor scan**: pg_cron auto-scrapes Ad Library
- [x] **Auto-classify**: new ads classified after each scrape
- [x] **Diff reports**: new ads, killed ads, format/angle shifts per week
- [ ] All background work shows in Planner as task cards

### Priority 6: Agent Intelligence Improvements
Make ad output truly diverse and data-driven.

- [ ] Agent reads Meta performance data BEFORE creating ads
- [ ] Agent parses ad names to correlate angle/format/hook with CPA
- [ ] User feedback on ads (thumbs up/down), stored, informs future batches
- [ ] Batch strategy: "2 competitor-inspired + 1 winner iteration + 2 new swings"
- [ ] Gemini receives angle/persona/awareness context for visual diversity
- [ ] No repeated angle/format combo within 7 days

### Priority 7: Onboarding Flow
Build AFTER all features work, BEFORE opening signups.

- [ ] Step 1: Sign up (email + password + brand name + URL)
- [ ] Step 2: The Atomic Unit -- explain offers concept
- [ ] Step 3: Connect Shopify (OAuth)
- [ ] Step 4: Connect Meta Ads (OAuth)
- [ ] Step 5: Connect Loop (API key, optional)
- [ ] Step 6: Auto-discover offers from Meta + Shopify
- [ ] Step 7: Set niche + auto-add competitors
- [ ] Step 8: Set autonomy level
- [ ] Step 9: Brand brain generated, first recommendations shown
- [ ] Step 10: "Create your first ad batch?" (with competitor context)

### Priority 8: Pre-Launch Polish
Build the week before opening signups.

- [ ] Rate limiting (5 req/min chat, 3 sandbox, 60 everything else)
- [ ] Mobile responsive pass
- [ ] Loading skeletons for all async operations
- [ ] Empty states for all lists/grids
- [ ] Glass card/button/input reusable components
- [ ] Performance page (placeholder currently)

---

## Known Bugs & Technical Debt

### Active Issues
- [ ] Agent ad diversity still needs work (visual variety, not just angle variety)
- [ ] Agent doesn't analyze competitor ad IMAGES visually (only text/metadata)
- [ ] Chat doesn't render ad images inline in messages (shows in terminal only)
- [ ] No Supabase Realtime subscriptions (polling only, 10-30s intervals)
- [ ] offer_metrics_daily only populated on import, not auto-updated from Meta
- [ ] Facebook CDN image URLs in old generated_assets have expired
- [ ] Competitor scraper: Garden of Life + Transparent Labs return 0 ads
- [ ] Sandbox max_steps=10 may be insufficient for complex workflows (should be 16-20)
- [ ] No per-user token spending cap per action (agent could burn all credits in one run)
- [ ] Agent sometimes picks paused EPs if brand brain has old data (cleanup on boot helps but not 100%)
- [ ] "make me AN ad" sometimes still generates 3 (prompt parsing not perfect)
- [x] Agent scans landing page content before generating ads (Priority 5 done)
- [ ] Performance page is placeholder (coming soon)

### Fixed This Session
- [x] Chat titles not auto-naming (fixed: now names all messages)
- [x] Simple questions spinning up E2B sandbox (fixed: smart routing)
- [x] No thinking indicator in chat (fixed: pulsing dots)
- [x] Meta API rate limits on EP sync (fixed: 5-min cache)
- [x] Entry point sync unreliable (fixed: effective_status + auto-sync before ad gen)
- [x] Agent forced to rotate angles (fixed: keeps winners, varies creatives)
- [x] Empty conversations cluttering sidebar (fixed: auto-cleanup after 5 min)

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
| Backend API | Railway (odyssey-x) | ✅ Live (api.runodyssey.io) | Yes (git push) |
| Worker | Railway (clever-gentleness) | ✅ Deployed | Yes (git push) |
| Database | Supabase | ✅ Live | Manual migrations |
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

### 2026-03-25 (Session 14)
- **Priority 5 (Landing Page Scan)**: `landing_page_scanner.py` — stdlib html.parser, no new deps. Extracts headline/subheadlines/CTAs/key-phrases/prices. `landing_page_cache` table with 7-day TTL. `_build_business_context()` injects "LANDING PAGE INTELLIGENCE" section for each active offer. Agent now sees exact page copy before generating ads. Migration `014_landing_page_cache.sql`.
- **Priority 6 (Visual Diversity)**: Full Creative Diversity Report replaces basic launched-ad summary. Analyses last 25 ads, counts format/color_scheme/funnel/awareness. Labels OVERUSED (>45%) and UNTESTED. Checks TOF/BOF imbalance. Blocks last 6 headlines. Generates specific per-batch DIVERSITY DIRECTIVE.
- **Priority 7 (Onboarding)**: 6-step guided wizard. Backend: `complete_onboarding_step()` RPC state machine, onboarding columns on profiles, two API endpoints. Frontend: beautiful wizard with progress bar, OAuth detection (Shopify/Meta), offer creation, competitor info, autonomy level picker. Progress persisted; all steps skippable. `auth-guard` redirects new users to onboarding. Migrations `014` + `015` ready to run in Supabase dashboard.
- TypeScript: tsc -b passes clean on all three.

### 2026-03-25 (Session 13)
- **Competitive Response Alerts** (Priority 4 complete): Weekly diff engine compares current competitor ad state vs previous weekly snapshot.
- **diff_engine.py**: Detects new ads launched, ads killed, format shifts (rising/falling), angle changes per brand. Generates markdown narrative and actionable suggested responses.
- **Migration 013**: `competitor_diff_reports` table + `upsert_competitor_diff_report` + `upsert_competitor_snapshot` SECURITY DEFINER RPCs. pg_cron job Monday 10 AM UTC.
- **API**: `GET /api/competitors/diffs`, `GET /api/competitors/diffs/latest`, `POST /api/competitors/diffs/generate` (on-demand), `POST /api/competitors/cron/generate-diffs` (cron-key).
- **Intel Page**: Collapsible "Intel Update" banner at top of page. Shows brand activity (new/killed per brand), format shift cards (green up / red down), rising angle pills, high-priority action items, and suggested ad responses.
- TypeScript: tsc -b passes clean.
- DB migration: `013_competitor_diff_reports.sql` (run manually in Supabase dashboard).

### 2026-03-25 (Session 12)
- **Self-Improving Prompts** (Priority 3 complete): Performance learner pulls Meta Ads data, parses ODY naming conventions, and stores learned patterns in `performance_insights` table.
- **Performance Insights DB**: `012_performance_insights.sql` migration with `upsert_performance_insight` and `clear_performance_insights` SECURITY DEFINER RPCs.
- **Performance Learner**: `backend/app/integrations/meta/performance_learner.py` — fetches 30-day metrics for all ODY-named ad sets/ads, groups by angle/persona/format/awareness, classifies tiers (winner/average/loser), stores in DB.
- **Agent Context Injection**: `_build_business_context()` in executor.py now calls `get_performance_context()` and injects PERFORMANCE INSIGHTS section into every agent system prompt. Agent sees which angles are winning, which formats drive CTR, which personas convert best.
- **Endpoints**: `POST /api/integrations/meta/sync-insights` (on-demand) + `GET /api/integrations/meta/insights` (view stored).
- **Weekly Cron**: `POST /api/watcher/cron/sync-insights` runs for all Meta-connected users.
- **Background Worker**: `performance_sync` task type added to task_processor.py.
- TypeScript: tsc -b passes clean.
- DB migration: `012_performance_insights.sql` (run manually in Supabase dashboard).

### 2026-03-25 (Session 11)
- **Watcher Agent**: Full kill/scale/monitor engine. 5 kill rules + 2 scale rules + fatigue detection. 4 autonomy levels (supervised/guided/autonomous/full_auto). User preferences stored in new `user_preferences` table. Daily health checks via `/api/watcher/cron`. Background worker handles `health_check` task type. DB: `watcher_reports` + `watcher_actions` tables with SECURITY DEFINER RPCs.
- **Watcher Frontend**: Compact WatcherBanner in Planner (always visible). Shows last run time, kills/scales/warnings pill summary. "Run Check" button. Expandable to show full report with rationale per action. Reports auto-load on planner open.
- **Undo Support**: `/api/watcher/actions/{id}/undo` reverses any kill or scale.
- TypeScript: tsc -b passes clean.
- Supabase migration: `011_watcher_agent.sql` (run manually in Supabase dashboard).

### 2026-03-25 (Session 10) -- PREVIOUS SESSION
- **Smart Chat Routing**: Simple questions (ROAS, strategy) use fast Sonnet 4.6 path (~3-5s) instead of full E2B sandbox (~60s). needs_sandbox() classifier routes appropriately.
- **Thinking Indicator**: Pulsing "ODY-1 is thinking..." with animated dots shown between send and first response.
- **Agent Freedom**: Removed forced constraints -- agent now keeps winning angles, allows 1-5 ads per batch, confirms plan before generating, can do multiple enhancement rounds.
- **Entry Point Intelligence**: Agent auto-syncs from Meta before generating, only uses ACTIVE EPs, confirms which EP to target in chat.
- **EP Auto-Sync**: Caches Meta API results for 5 min to prevent rate limits. Auto-marks non-active EPs as paused.
- **Brand Brain Cleanup**: Sandbox boot removes outdated rotation instructions from learnings.md.
- **Comprehensive UX Testing**: Tested 15+ real ecom owner prompts, identified 32 issues.
- **6 High-Impact UX Fixes**: Typing indicator, smart routing, auto-naming, empty cleanup, launch buttons, EP caching.
- **Design Research**: Deep research into Linear, Vercel, Cursor, Superhuman, Raycast, Notion, Figma, Framer -- produced design system brief.
- **Settings Sidebar Nav**: Billing/Integrations/Competitors/Account sections.
- **Token Pricing Redesign**: Decoy psychology with gradient borders, BEST VALUE hero card.
- **Stripe Payment Tested**: Live $1 test payment confirmed working end-to-end.
- Key learning: Agent needs freedom, not rigid rules. Guidelines > hard constraints.
- Key learning: Simple questions should NEVER spin up E2B sandbox.
- Key learning: Brand brain files can override prompt instructions -- need cleanup on boot.
- Key learning: EP status must come from effective_status (accounts for campaign being off).

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

### Competitor Intelligence Architecture
```
NICHE (e.g., "Supplements")
 |- 12 Competitor Brands (shared across all users in niche)
 |- 185 Total Ads (scraped from Meta Ad Library)
 |    |- Permanent images in Supabase Storage
 |    |- Each classified: format + awareness + angle + hook
 |    |- Winner score: 0-100
 |    |- Status: active / turned_off (with deactivation date)
 |- Niche Analytics (aggregated)
 |    |- Top angles: Gut Health (42), Nutrient Gaps (10), Sleep (7)
 |    |- Top formats: OFFER, HP, IB, TEST, LIFE
 |    |- Awareness mix: TOF 45%, MOF 30%, BOF 25%
 |- Auto-refresh: Weekly scrape + classify via pg_cron
```

### Three Pillars
| Pillar | Source | Current Value | What Improves It |
|--------|--------|---------------|-----------------|
| CPA | Meta Ads | $51 | Better ads, angles, targeting |
| AOV | Shopify | $45 | Better offers, bundles, upsells |
| LTV | Loop | $326 | Better retention, email flows |
