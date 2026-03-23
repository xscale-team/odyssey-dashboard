# Odyssey X — Living Roadmap & Checklist

> **This is the MAIN planning document.** Claude references and updates this every session.
> Items are checked off as they're completed. New items get added as scope evolves.
> Last updated: 2026-03-20

---

## Quick Status

| Phase | Status | Progress |
|-------|--------|----------|
| Phase 1: Foundation | 🟡 In Progress | ~87% |
| Phase 2: Three Pages | 🟡 In Progress | ~72% |
| Phase 3: Continuous Monitoring & Autonomy | ⬜ Not Started | 0% |
| Phase 4: Onboarding & Brand Brain | ⬜ Not Started | 5% |
| Phase 5: Learning Loop & Vision AI | ⬜ Not Started | 0% |
| Phase 6: Monetization & Polish | ⬜ Not Started | 5% |

---

## Phase 1: Foundation

**Goal:** The agent system works reliably with skill files, brand brain, and persistent assets.

### Database & Schema
- [x] `profiles` table with auto-create trigger on signup
- [x] `conversations` + `messages` tables (001 migration)
- [x] `agent_executions` + `connected_services` + `usage_events` (001 migration)
- [x] `offers` table with full schema (product_name, pricing, targets, etc.)
- [x] `offer_products` junction table
- [x] `offer_metrics_daily` with computed fields (CPA, ROAS, CTR, AOV)
- [x] `offer_campaigns`, `offer_ad_sets`, `offer_ads` linking tables
- [x] `generated_assets` table (ad_image, ad_copy, email, landing_page)
- [x] `agent_tasks` with enhanced schema (checkpointing, progress, logs)
- [x] `activity_log` for audit trail
- [x] `approval_queue` for human-in-the-loop
- [x] `brand_profiles` table
- [x] `scheduled_jobs` table
- [x] `credit_balances` + `credit_transactions` tables
- [x] `offer_dashboard` view (7-day aggregated metrics)
- [x] RLS enabled on ALL tables with proper policies
- [ ] `offer_metrics_lifetime` table (not in current migration)
- [ ] `offer_landing_pages` table
- [ ] `offer_entry_points` table
- [ ] `offer_status_log` table
- [ ] `offer_tests` table (A/B testing)
- [ ] `competitor_profiles` table
- [ ] `creative_analysis` + `creative_insights` tables
- [ ] `brand_scrape_results` table
- [ ] `agent_memory_episodes` table (episodic memory)
- [ ] `pre_approval_rules` table (progressive autonomy)
- [ ] `usage_meters` table (billing)

### Backend: Core API
- [x] FastAPI app with proper CORS, rate limiting, error handling
- [x] Auth router (signup, login, refresh, me)
- [x] Chat router with SSE streaming
- [x] Sandbox router (E2B execution)
- [x] Assets router (CRUD + approve/reject)
- [x] Offers router (CRUD + dashboard + discovery + products)
- [x] Tasks router (CRUD + cancel + activity feed)
- [x] Scheduled jobs endpoints (CRUD + manual trigger)
- [x] Approval queue endpoints (list + respond)
- [x] Stats endpoint for dashboard
- [x] Shopify integration router (OAuth + sync)
- [x] Meta integration router (OAuth + data pull)
- [ ] Metrics sync endpoint (pull daily metrics from Meta/Shopify into offer_metrics_daily)
- [ ] Brand profile endpoints (CRUD for brand_profiles table)
- [ ] Analytics/reporting endpoint (aggregate stats, trend data)
- [ ] Klaviyo integration router
- [ ] Webhook endpoints for Shopify/Meta real-time updates

### Backend: Agent System
- [x] Agent executor with agentic loop + event queue
- [x] Agent registry with role definitions
- [x] Agent tools (Shopify, Meta, image generation, etc.)
- [x] 6 system prompts in `/backend/prompts/` (orchestrator, creative, image, media, performance, product)
- [x] Sandbox executor saves generated assets to DB with batch_id
- [x] Sandbox executor uploads images to Supabase Storage
- [x] ODY-### naming convention implemented in batch generation
- [x] Terminal/activity event streaming during execution
- [ ] Remaining 5 system prompts (landing-page-builder, email-strategist, ad-monitor, brand-intelligence, offer-architect)
- [ ] Brand brain auto-generation pipeline (from Shopify + website data)
- [ ] Brand brain loading into sandbox at task start
- [ ] Skill file loading per role in sandbox (currently referenced but not verified end-to-end)
- [ ] Agent cost tracking (tokens used, cost per execution → usage_events)
- [ ] Agent error recovery / retry logic
- [ ] Checkpoint saving for long-running tasks

### Backend: Worker Service
- [x] Worker directory with task_processor.py (507 lines)
- [x] Polls Supabase for pending tasks with advisory locks
- [ ] Deploy worker as separate Railway service
- [ ] ARQ + Upstash Redis queue (currently polling Supabase directly)
- [ ] pg_cron integration for scheduled job triggers
- [ ] Health monitoring for worker process

### Frontend: Core Infrastructure
- [x] React 19 + Vite + TypeScript + Tailwind
- [x] Zustand stores: auth, chat, cockpit, dashboard
- [x] API fetch utility with auth headers
- [x] Supabase client for real-time subscriptions
- [x] Router with all pages wired
- [x] Deploys on Cloudflare Pages (tsc -b passes clean)
- [ ] Supabase Realtime subscriptions for live updates (tasks, activity)
- [ ] Error boundaries on all page components
- [ ] Loading skeletons for all async data
- [ ] Offline/reconnection handling

### Skill Files (Agent Training SOPs)
- [x] `/backend/prompts/orchestrator.md`
- [x] `/backend/prompts/creative_strategist.md`
- [x] `/backend/prompts/image_creative.md`
- [x] `/backend/prompts/media_buyer.md`
- [x] `/backend/prompts/performance_strategy.md`
- [x] `/backend/prompts/product_intelligence.md`
- [ ] `/backend/prompts/landing_page_builder.md`
- [ ] `/backend/prompts/email_strategist.md`
- [ ] `/backend/prompts/ad_monitor.md`
- [ ] `/backend/prompts/brand_intelligence.md`
- [ ] `/backend/prompts/offer_architect.md`

### Liquid Glass CSS
- [x] Design tokens in index.css (--bg-base, --accent, --glass-*, etc.)
- [x] Glass tabs component
- [x] Glass badge component
- [ ] Glass card component (reusable)
- [ ] Glass button component (reusable)
- [ ] Glass input component (reusable)
- [ ] Glass modal/dialog component
- [ ] Custom scrollbar styling
- [ ] Shimmer/skeleton loading animations
- [ ] Consistent border-radius system (8/12/16px)

---

## Phase 2: Three Pages

**Goal:** Chat, Planner, and Team pages all fully functional and polished.

### Layout & Navigation
- [x] Top nav bar with Chat / Planner / Team / Offers tabs
- [x] Active tab indicator with accent color
- [x] User menu dropdown (settings, sign out)
- [x] Sidebar shows on Chat page only
- [x] Routes: /chat (default), /planner, /team, /offers, /settings
- [ ] Sidebar: conversation list with search/filter
- [ ] Sidebar: active conversation highlight with accent border
- [ ] Mobile responsive navigation (hamburger or bottom tabs)

### Chat Page
- [x] MessageList with streaming content
- [x] ChatInput with send button
- [x] Terminal panel (live agent activity during streaming)
- [x] Terminal auto-collapse after streaming ends
- [x] Chat/Cockpit sub-tabs (see assets without leaving chat)
- [x] Suggestion chips on empty state
- [x] Conversation persistence (loadConversations on mount)
- [ ] Image previews inline for generated ads (rendered in message)
- [ ] User messages: gold-tinted glass bubble (right-aligned)
- [ ] Assistant messages: glass card with "O" avatar
- [ ] Agent activity cards with colored borders per agent
- [ ] Streaming cursor: gold pulsing dot
- [ ] "While you were gone" banner (pending approvals, completed tasks)
- [ ] Glass input bar with border glow on focus
- [ ] Gold send button with hover animation
- [ ] Copy message button
- [ ] Regenerate response button

### Planner Page
- [x] Kanban board with task cards (Queued → Running → Completed)
- [x] Task creation modal (type, title, description)
- [x] Approval queue tab with "Needs Review" cards
- [x] Assets tab (reuses CockpitView)
- [x] Right panel: Live Activity feed with green pulse
- [x] SandboxExecutionPanel overlay for running tasks
- [x] TaskDetailPanel for viewing completed tasks
- [x] Status bar with stats
- [ ] Click active task card → see live terminal progress inline
- [ ] Completed cards show output inline (ad thumbnails, report summaries)
- [ ] Approve / Reject actions on approval cards (wired to backend)
- [ ] Filter tasks by: agent, task type, date, offer
- [ ] "While you were gone" banner with pending approvals count
- [ ] "Ideas" section — agent's proactive suggestions
- [ ] Real-time task movement (Supabase Realtime subscriptions)
- [ ] Task priority / reordering

### Team Page
- [x] 9 team member cards with name, role, emoji, color, description
- [x] Summary stats: Active Now, Total Tasks, Ads Created, AI Cost
- [x] Three Pillars section (CPA, AOV, LTV) — placeholder values
- [x] Team member grid (3 columns)
- [x] "Your Team's Brain" collapsible section (brand brain files, skill files, workspace)
- [ ] "Hire agents" concept — users see hired vs available agents
- [ ] Click agent to hire/unhire (cosmetic + priority, all work behind the scenes)
- [ ] Click team member → detailed stat page (tasks completed, avg duration, tools used, recent output)
- [ ] Live status per agent (idle / working on X / completed Y today) — from real data
- [ ] Performance impact attribution (CPA/AOV/LTV changes per agent)
- [ ] Three Pillars with REAL data from offer_metrics_daily
- [ ] Stats cards with REAL data from agent_tasks + generated_assets
- [ ] Agent cost tracking display (daily/weekly AI spend per agent)
- [ ] System health indicators (queue depth, error rate, uptime)

### Cockpit (Asset Management)
- [x] CockpitView with sub-tabs (All, Ads, Copy, Email, Landing Pages)
- [x] Asset cards with image preview, title, status badge
- [x] Approve/Reject/Delete actions on cards
- [x] Cockpit store with loadAssets, approveAsset, rejectAsset, deleteAsset
- [ ] Batch grouping (collapsible groups by batch_id)
- [ ] Asset detail modal (full ad preview with all metadata)
- [ ] Glass card hover: lift + glow effect
- [ ] Download asset button
- [ ] Bulk approve/reject
- [ ] Filter by date range
- [ ] Search assets by title/content

### Offers Page
- [x] List offers with status badges
- [x] Create new offer (modal form with correct fields)
- [x] Delete offers
- [x] Dashboard metrics view (spend, ROAS, CPA from offer_dashboard)
- [x] Meta ad discovery + import
- [ ] Edit existing offers (inline or detail view)
- [ ] Offer detail page (all linked campaigns, ads, metrics over time)
- [ ] Offer metrics chart/graph (7-day trend)
- [ ] Link offers to generated assets
- [ ] "Create ads for this offer" quick action

### Landing Page Generation
- [ ] Landing page builder agent (HTML generation in sandbox)
- [ ] Push to Shopify via Pages API (primary method)
- [ ] Fallback: deliver HTML file for manual upload into page builder
- [ ] Link landing page to offer_id
- [ ] Landing page preview in Cockpit
- [ ] Landing page performance tracking (conversion rate)

### Chat: Autonomy Controls
- [ ] Autonomy level indicator in chat header or settings
- [ ] Natural language autonomy changes ("give me full control", "approve everything for me")
- [ ] Confirmation when autonomy level changes

---

## Phase 3: Continuous Monitoring & Autonomy

**Goal:** The agent monitors continuously, acts based on data, and respects user-chosen autonomy levels.

### Worker & Infrastructure
- [ ] Deploy ARQ worker on Railway as separate service
- [ ] Set up Upstash Redis for task queue
- [ ] Implement pg_cron triggers for continuous monitoring tasks
- [ ] Job execution logging (start, end, cost, errors)
- [ ] Job failure alerting (notify user on repeated failures)

### User-Controlled Autonomy
- [ ] Autonomy level selector in onboarding flow
- [ ] Autonomy level changeable from chat window ("give me full control" / "let me approve everything")
- [ ] Autonomy level stored per user in profiles table
- [ ] All agent actions check user's autonomy level before acting
- [ ] Four levels: 🔒 Supervised → 🟡 Guided → 🟢 Autonomous → ⚡ Full Auto
- [ ] FORBIDDEN actions enforced at all levels (delete products, change pricing, send emails)

### Continuous Monitoring (Replaces Weekly Cadence)
- [ ] CPA monitoring per ad (every 6 hours) — pause bad ads
- [ ] CPA monitoring per offer (daily) — diagnose: ads, landing page, or targeting?
- [ ] Ad fatigue detection (daily) — flag for creative refresh
- [ ] ROAS winner detection (daily) — scale budget within guardrails
- [ ] AOV trend monitoring (weekly) — suggest offer restructure
- [ ] Landing page conversion tracking (weekly) — suggest new variant
- [ ] Subscription churn monitoring (weekly) — flag for email flow adjustment
- [ ] Repeat purchase rate tracking (weekly) — suggest retention strategies
- [ ] Competitor ad scanning (weekly) — analyze and suggest counter-strategy
- [ ] Revenue anomaly detection (daily) — alert on drops/spikes

### Campaign Launch Pipeline
- [ ] Generate new ad batch (images + copy) on demand or when monitoring suggests
- [ ] Structure campaign with proper naming convention
- [ ] Create Meta campaign via API (CBO structure)
- [ ] Create ad sets with proper targeting
- [ ] Upload creatives and create ads
- [ ] Submit for user approval (based on autonomy level)
- [ ] Launch on approval

### Kill/Scale Rules Engine
- [ ] Configurable thresholds per offer (target CPA, min ROAS)
- [ ] Kill rules (5 rules from MASTER-PLAN.md)
- [ ] Scale rules (budget increase limits, duplication rules)
- [ ] Audit trail for every automated action
- [ ] Override/undo capability for users

### Proactive Suggestions
- [ ] Agent identifies opportunities and surfaces them in Chat + Planner "Ideas"
- [ ] "Your best ad has been running 14 days, want me to create variations?"
- [ ] "Competitor X launched 5 new ads targeting your angle"
- [ ] "Landing page conversion dropped 15%, want me to build a variant?"

### Approval Workflow
- [ ] Queue items with context (what action, why, expected impact)
- [ ] Timeout with safe defaults (don't launch without approval)
- [ ] Batch approvals (approve all from one batch)
- [ ] Rejection with feedback (agent learns from rejections)
- [ ] Push notifications (email or in-app) for pending approvals

### "While You Were Gone" System
- [ ] Track user's last active timestamp
- [ ] On return, aggregate: tasks completed, ads generated, ads paused, budget changes
- [ ] Banner component at top of Chat and Planner pages
- [ ] Clickable items that navigate to details

---

## Phase 4: Onboarding & Brand Brain

**Goal:** New users get a fully populated brand brain automatically.

### Website Scraping Pipeline
- [ ] E2B sandbox with Playwright for website scraping
- [ ] Extract: brand voice, visual identity, product positioning, trust elements
- [ ] Auto-detect competitors from website content
- [ ] Generate brand-profile.md from scraped data
- [ ] Store results in brand_scrape_results table

### Brand Brain Auto-Generation
- [ ] Trigger on first Shopify connection
- [ ] Pull all products, collections, orders, revenue data
- [ ] Generate `/brand/brain/` file structure:
  - [ ] `brand-profile.md` — from Shopify + website scrape
  - [ ] `products.md` — full catalog with prices, images, bestsellers
  - [ ] `offers.md` — active offers with performance data
  - [ ] `competitors.md` — 5 auto-detected competitors
  - [ ] `creative-dna.md` — visual styles that work for this brand
  - [ ] `performance.md` — Meta Ads performance data
  - [ ] `strategy.md` — current testing plan
  - [ ] `learnings.md` — accumulated insights
  - [ ] `preferences.md` — learned from approvals/rejections
- [ ] Store in Supabase Storage (per user)
- [ ] Load into sandbox at task start

### Competitor Detection
- [ ] Meta Ad Library API integration
- [ ] Auto-detect 5 competitors from niche + product category
- [ ] Scrape competitor ad creatives
- [ ] Generate competitor analysis report

### Onboarding Flow UI
- [ ] Step 1: Sign up (email + password + brand name + URL)
- [ ] Step 2: The Atomic Unit — explain offers concept with visual
- [ ] Step 3: Connect Shopify (OAuth — already built, needs polish)
- [ ] Step 4: Website scrape (background, show progress)
- [ ] Step 5: Connect Meta Ads (OAuth — already built, needs polish)
- [ ] Step 6: Build Your Team — "What do you need help with?" → auto-hire agents
- [ ] Step 7: Set Autonomy Level — choose 🔒/🟡/🟢/⚡ with examples
- [ ] Step 8: Brand brain generated, first recommendations shown
- [ ] Step 9: First task suggestion ("Create your first ad batch?")
- [ ] Progress indicator across steps
- [ ] Skip option for optional steps (Meta, Klaviyo)

---

## Phase 5: Learning Loop & Vision AI

**Goal:** Each ad batch is better than the last.

### Vision AI Classification
- [ ] Claude Haiku on generated ad images (classify format, quality score)
- [ ] Compare generated ads to winning competitor ads
- [ ] Auto-detect visual elements (text placement, color usage, face presence)
- [ ] Quality gate: reject low-scoring ads before showing to user

### Performance Correlation Engine
- [ ] Format × CPA correlation (which formats perform best?)
- [ ] Angle × ROAS correlation (which angles drive revenue?)
- [ ] Persona × CTR correlation (which audiences engage?)
- [ ] Awareness level × conversion rate
- [ ] Store findings in creative_insights table

### Creative DNA Profiles
- [ ] Per-brand visual profile (colors, styles, formats that work)
- [ ] Update after each batch performance review
- [ ] Inform next batch's creative direction
- [ ] Show in Team page under agent performance

### Episodic Memory
- [ ] Track user approvals/rejections with reasons
- [ ] Agent learns preferences over time
- [ ] Store in agent_memory_episodes table
- [ ] Inform future creative decisions

---

## Phase 6: Monetization & Polish

**Goal:** Billing works, app is production-ready.

### Credit System
- [ ] Stripe Billing integration
- [ ] Credit deduction per action (see MASTER-PLAN.md for rates)
- [ ] Balance display in header/settings
- [ ] Low balance warnings
- [ ] Auto-pause background tasks when credits run out

### Two-Tier Pricing (Simplified)
- [ ] Free tier: on-demand VM, limited free credits, 1 store
- [ ] Pro ($199/mo): always-on VM, unlimited stores, continuous monitoring, same usage rates
- [ ] Upgrade prompts when hitting limits or wanting continuous monitoring
- [ ] Usage dashboard in settings

### User-Controlled Autonomy (Settings)
- [ ] Autonomy level picker (4 levels) in onboarding + settings + chat
- [ ] Per-action override capability
- [ ] FORBIDDEN action list (hardcoded, not changeable)

### Production Polish
- [ ] Error boundaries on all page components
- [ ] Loading states for every async operation
- [ ] Empty states for all lists/grids
- [ ] 404 / error pages
- [ ] Mobile responsive (all 3 pages)
- [ ] Accessibility basics (keyboard nav, screen reader labels)
- [ ] Performance: lazy load routes, image optimization
- [ ] SEO: meta tags, og:image for marketing pages
- [ ] Custom domain setup
- [ ] Analytics (Plausible or PostHog)

---

## Known Bugs & Technical Debt

> Add bugs here as they're found. Check them off when fixed.

- [x] TS6133 build errors on Cloudflare (unused imports/variables) — fixed 2026-03-20
- [x] Offers router referenced non-existent DB columns — fixed 2026-03-20
- [x] Discovery module referenced non-existent columns — fixed 2026-03-20
- [x] CHECK constraint mismatch (old vs new offer_type values) — fixed 2026-03-20
- [x] Path resolution breaks on Railway (skills, prompts, supplement-ad-creator) — fixed 2026-03-20
- [x] **Chat freezes 2-3+ minutes during ad generation** — fixed: added error reporting, 240s timeout, progress events
- [x] Duplicate terminal entries (2-4x per action) — fixed: removed duplicate emits in executor.py + SSE mapper
- [x] Sandbox stuck with no error feedback — fixed: error + done events on crash, 240s timeout
- [x] Ads ugly/tiny text — fixed: replaced Pillow renderer with Gemini AI full composition
- [x] Images disappear after streaming — fixed with creatives_grid SSE event
- [x] Ads lack product photos — fixed: product image sent to Gemini for composition
- [x] Team page Three Pillars — wired to live Meta CPA + Shopify AOV
- [x] Team page stats cards — wired to per-agent task/asset counts
- [x] Debug endpoints exposed API keys — removed
- [x] .single() crashes on missing data — fixed all 10 callsites
- [x] Meta tokens in URL params — moved to Authorization header
- [x] Message delete missing ownership check — fixed
- [x] Token decryption crashes on bad data — graceful error handling
- [ ] Agent invents insane angles (fertility for thyroid supplement) — partially fixed with guardrails
- [ ] Shopify product fetch incomplete (pagination/status filter) — fixed but needs verification
- [ ] Chat page doesn't render generated ad images inline in messages
- [ ] Planner approval cards not wired to backend approve/reject endpoints
- [ ] No error handling in cockpit store API calls
- [ ] Worker service not deployed on Railway yet
- [ ] No Supabase Realtime subscriptions for live task updates
- [ ] Offer metrics view will show empty until metrics sync is built

---

## Integration Status

| Integration | OAuth | Data Pull | Data Push | Status |
|-------------|-------|-----------|-----------|--------|
| Shopify | ✅ Built | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ Built | ✅ Campaigns, ads, metrics | ⬜ Create campaigns | 🟡 60% |
| Klaviyo | ⬜ Not started | ⬜ | ⬜ | ⬜ 0% |
| Supabase Storage | ✅ | ✅ Upload images | ✅ | ✅ 100% |
| E2B Sandbox | ✅ | ✅ | ✅ | ✅ 95% |

---

## Deployment Status

| Service | Platform | Status | Auto-deploy? |
|---------|----------|--------|-------------|
| Frontend | Cloudflare Pages | ✅ Live | Yes (git push) |
| Backend API | Railway | ✅ Live | Yes (git push) |
| Worker | Railway | ⬜ Not deployed | — |
| Database | Supabase | ✅ Live | Manual migrations |
| Redis | Upstash | ⬜ Not set up | — |

---

## Session Log

> Brief notes on what was done each session, for continuity.

### 2026-03-20 (Session 1)
- Ran safe ALTER migrations on Supabase (added missing columns without dropping data)
- Fixed offers router + discovery module to match actual DB schema
- Added asset persistence in sandbox executor (saves to generated_assets on image upload)
- Built 3-page layout: Chat / Planner / Team with top nav
- Built Team page with 9 agents, stats, Three Pillars, Team Brain section
- Built Planner page with kanban, approvals, assets tabs, activity feed
- Fixed TypeScript build errors (unused vars/imports) for Cloudflare deploy
- Created this ROADMAP.md as living planning document

### 2026-03-20 (Session 2)
- Fixed path resolution for Railway deployment (skills, prompts, supplement-ad-creator)
- Copied supplement-ad-creator into backend/ for Docker build
- Fixed chat/router.py and agents/registry.py prompts path resolution
- Added ROADMAP.md rules to CLAUDE.md (must read/update every session)
- Major MASTER-PLAN.md revision (v2) based on founder Q&A:
  - Continuous monitoring replaces rigid weekly cadence
  - User-controlled autonomy (4 levels, changeable anytime in chat)
  - Simplified pricing: Free + Pro ($199/mo always-on VM), pure usage-based
  - "Hire agents" concept: cosmetic + priority, onboarding asks what you need
  - Landing pages via Shopify Pages API (HTML fallback)
  - Atomic Unit explanation added to onboarding flow
  - Three Pillars expanded: which metrics affect each, which agents responsible
  - Quality over speed — no timeline pressure, build the best possible product
- Fixed images disappearing after streaming (collect image_urls during stream, prepend to saved message)
- Fixed ads lacking product images (auto-download product_image_url in _generate_ad_batch, added to tool schema + system prompt)
- Created HANDOFF.md — complete session passoff document (~270 lines)
- Updated CLAUDE.md: HANDOFF.md only read on fresh chats, not mid-conversation

### 2026-03-20 (Session 3)
- Pushed CLAUDE.md update (fresh-chat-only HANDOFF.md rule) to git
- Built live dev dashboard (dev-dashboard/server.py + index.html)
  - Glass-themed dark kanban board: Up Next → Bugs → To Do → In Progress → Done
  - 4 tabs: Kanban Board, Phase Status, Bugs & Debt, Session Log
  - Stats bar: overall progress %, phases, open bugs, action items, sessions
  - Auto-refreshes every 3 seconds from local ROADMAP.md
- Created separate GitHub repo (xscale-team/odyssey-dashboard) for team visibility
  - GitHub Pages enabled: https://xscale-team.github.io/odyssey-dashboard/
  - Dual-mode: local server.py (3s) or GitHub Pages (30s, fetches raw ROADMAP.md from main repo)
  - Client-side ROADMAP.md parser mirrors server.py logic
- Identified #1 bug: chat freezes 2-3+ minutes during ad generation with no progress updates

---

## What To Do Next

> Updated each session. This is the "pick up here" section.

### Immediate Priority (Current Sprint)
1. ~~Wire Team page to real data~~ ✅ Done (session 6)
2. **Wire Planner approval actions** — approve/reject buttons call backend endpoints
3. **Ad click-to-expand + download button** on generated creatives
4. **Creatives grid during streaming** — show ads in grid as they generate, not full-width
5. **"While you were gone" system** — summarize what happened since last login

### Next Up
6. **Supabase Realtime** — live task updates on Planner (no polling)
7. **User feedback system** — rate ads, flag issues, continuous improvement loop
8. Deploy worker service on Railway
9. Metrics sync (daily pull from Meta → offer_metrics_daily)
10. Watcher kill/scale logic (NEVER auto-kill, always require approval)

### Pre-Launch (build the week before signups)
11. **Rate limiting** — 5 req/min on chat, 3 on sandbox, 60 on everything else. Exempt SSE streams.
12. **Credit enforcement** — atomic check + reserve before execution, refund overage, env toggle for rollback
13. Error handling in all stores
14. Mobile responsive pass

### Quality Focus
15. Glass card / glass button / glass input reusable components
16. Chat message styling (gold user bubbles, glass assistant cards)
17. Loading skeletons everywhere
