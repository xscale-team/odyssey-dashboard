# Odyssey X — Living Roadmap

> **Last updated: 2026-04-26 (Session 36)**
> Goal-driven, not timeline-driven. Ship MVP when pipeline is bulletproof.
>
> **What To Do Next:** Push/deploy Session 36 fixes, then re-test production Batch A/B generation. Remaining gaps: unify bot/dashboard metrics, mark failed/regenerated DB assets as non-ready, harden long-batch SSE/reconnect behavior, add OCR-backed creative review, and make `create_review_batch`/Open Review CTA reliable for generated ads. Full notes: `docs/qa-runs/2026-04-26-brand-owner-ad-bot-qa.md` and `docs/qa-runs/2026-04-26-deep-brand-owner-production-qa.md`.

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

### Priority 4: Fix Sandbox Token Refresh on Integration Connect — ✅ SHIPPED
- [x] New integrations not picked up until "New Chat" — fixed in commit e912663
- [x] Sandbox invalidation / dynamic token injection — `get_or_create()` now re-injects env vars on every cached-sandbox hit

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

### Priority 8: Klaviyo Integration — 🚧 IN PROGRESS (PR 3 of 4 merged)
- [x] PR 1 — Connect + data sync (lists/segments/flows/campaigns/templates → 5 brain files) + daily cron refresh + Settings UI
- [x] PR 3 — Email draft → Klaviyo campaign-draft push: `create_template` / `update_template` / `create_campaign` client methods, `email-assets` Supabase bucket (migration 032), Gemini 3 Pro hero pipeline, HTML email renderer (600px / inline CSS / liquid tags / em-dash-free), top-5 campaigns' HTML backfill during sync, 4 sandbox tools (`get_top_performing_emails`, `save_klaviyo_template`, `create_klaviyo_campaign_draft`, `generate_email_batch`), sandbox→Railway HTTP tool-bridge + `agent_events` log, `klaviyo_draft_created` SSE event + frontend deep-link card, per-email billing, orchestrator Flow B prompt + Mailer SOP upgrade. **Manual QA gate:** human verifies draft renders in Klaviyo UI before merge.
- [ ] PR 4 — SMS / push draft tools, flow drafts, proactive audit, Three Pillars brain file, competitor email library (RGE scraper, `competitor_emails` table, weekly pg_cron refresh), pytest expansion, docs finalize
- Private API key only; public OAuth app deferred.

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

### Priority 10: Post-Launch Expansion (Directional)
Directional items captured from the old architecture doc future phases. Not committed to, just preserved so nothing is lost.
- [ ] Entry-point page types beyond landing pages (advertorials, listicles, quizzes) — CPA pillar
- [ ] Video ad / UGC content generation (Gemini stills only today)
- [ ] Multi-platform expansion: TikTok Ads, Google Ads, Pinterest
- [ ] Multi-store support (one user → many Shopify stores)
- [ ] White-label / agency-mode for Xscale clients

---

## Known Bugs & Technical Debt

### P1 — Active
- [ ] **Bot metrics disagree with dashboard metrics** - Session 31 production QA: bot reported 30d revenue `$16,387`, orders `392`, CPA `$65`; dashboard showed Net Sales `$13,327`, orders `346`, CPA `$33.22`. Need one shared metrics contract and labels for gross/net sales, first-order CPA, blended CPA, attribution window, and source filters.
- [x] **Wrong entry point / offer can leak into product-specific strategy** - Session 33 full-batch QA for HashiAid selected `https://quiz.ibdassist.com/` as the "only active EP" and launch destination for a HashiAid 90-Day Reset batch. Fixed in Session 34: `get_offer_entry_points` now accepts `product_name`, filters offers by product/offer name, keys its cache by filters, and returns a warning instead of silently reusing another product's EP. Business context now also tells the agent to ask for landing-page confirmation when the EP belongs to a different product/brand.
- [x] **Native ad generation skipped mandatory product image verification** - Fixed after Session 31 deep QA: `generate_ad_batch` now refuses to run unless `product_image_confirmed_by_user=true`, forcing the agent to show the selected product image and wait for confirmation before generating.
- [x] **Agent still guesses Shopify CDN URLs during ad generation** - Fixed after Session 31 deep QA: `get_shopify_products` now returns an explicit verified `image_url`; `generate_ad_batch` validates product image URLs against the user's synced Shopify product cache before download, unless the image is explicitly user-provided.
- [ ] **Regenerated or duplicated ads remain approve-ready / visible as deliverables** - Session 31: requested 3 ads, generated BOF replacement after QC, but UI rendered `AD BATCH · 4 ready` including the rejected BOF original. Session 33 reproduced harder: requested 5 ads, reviewer flagged 4 for word count, internal regen pushed the live strip to `AD BATCH · 8 ready` while the bot claimed only 5 final ads. Session 34 fixed live chat strip inflation for same-size regenerations. Session 36 found a one-slot regen edge case where the stream-local `ad_index` reset to 1 and the persisted gallery kept the rejected slot; patched final artifact persistence to prefer URLs in the agent's final response and hide duplicate markdown images when a structured ad payload exists. Rejected `generated_assets` DB rows still need a non-ready state before we expose a gallery approval flow.
- [x] **Final ad URLs disappear from chat after full-batch completion** - Session 33 full-batch QA showed `AD BATCH · 8 ready` while generation streamed, but after completion the DOM had zero `ad-creatives` links. Fixed in Session 34: `image_generated` events now carry batch metadata, native router persists final generated images as hidden `batch-json`, loaded assistant messages parse that payload, and frontend final messages fall back to the live generated-ad buffer.
- [x] **Prior ad batch leaks into fresh chats** - Fixed in Session 31 and verified in production post-deploy: a new production chat opened with no stale `AD BATCH` strip before any message.
- [x] **TOF ads can repeat the same visual template across sessions** - Session 31 follow-up: production could produce varied TOF concepts only when explicitly prompted, but the generator was not saving `gemini_prompt` or visual archetype memory. Fixed by persisting prompt/archetype metadata, hydrating archetype counts into business context, auto-assigning distinct TOF visual archetypes when omitted, and keeping product photos out of TOF image-provider inputs.
- [x] **TOF ad generation returned 0 ads after product confirmation** - Session 32 production smoke found the new TOF no-product-input path clashed with Kie.ai provider validation. Fixed by routing source-less Kie calls to `gpt-image-2-text-to-image` and returning `IMAGE_GENERATION_FAILED` with per-ad provider errors when every ad in a batch fails.
- [ ] **Creative self-critique hallucinates asset details** - Session 31: critique flagged nonexistent `lost 18 lbs` and `Always exhausted? Hashimoto's might be why` copy. Deep follow-up: the bot quoted planned/drafted text, not visible image text, for all four generated assets. Session 33 reviewer flagged 4 ads for word count without surfacing image-grounded evidence. Review needs OCR/image-grounded inputs or must disclose it cannot inspect final pixels.
- [ ] **Supplement/Meta compliance checker is too weak** - Session 31 generated Hashimoto's/fatigue claims, likely fabricated testimonial copy, and time-bound outcome claims without blocking or substantiation. Deep follow-up generated "Thyroid healing takes 90 days," "Still exhausted on thyroid meds?", "Hashimoto's is treated," and "disease itself" language, then recommended some planned copy as launchable. Session 33 generated/approved more risky disease and testimonial copy: "autoimmune thyroid," "Hashimoto's women," "Synthroid," "Day 47, my hairdresser noticed first," and "thyroid antibody and energy markers to shift." Session 36 added text-level supplement/Meta compliance guards to `generate_ad_batch` instructions and `review_ad_batch`, but OCR/pixel-grounded compliance is still outstanding.
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
- [x] **Generated ad images are 1254x1254, not 1080x1080** - Fixed after Session 31 deep QA: native ad generation normalizes provider output to 1080x1080 PNG before upload.
- [ ] **Em dash rule still leaks into customer-facing chat/ad labels** - Session 31 bot response used multiple em dashes despite the public-facing no-em-dash rule. Frontend v2 chat now sanitizes en/em dashes in rendered text, but Session 33 still showed em dashes in assistant paragraphs and option button labels (`Approved as-is — ...`, `Batch plan — 5 ads`). Backend/prompt/button rendering enforcement still needed.
- [ ] **No obvious review-gallery CTA after ad generation** - Bot says "Open the gallery to approve and ship", but Session 33 follow-up found no visible Review Gallery link and zero ad URL links in the completed chat DOM after a full batch. The gallery CTA needs to be a persistent, clickable artifact with final-only assets.
- [ ] **Long full-batch SSE streams enter confusing reconnect / stuck states** - Session 33 5-ad batch hit `Reconnecting...` after ad 1, recovered to ad 3, then kept `Stop` visible for several minutes during regeneration. The final message eventually completed, but progress signals were noisy (`:`, `→`) and polling saw no stable final asset strip. Need cleaner heartbeat/reconnect state and terminal batch status.
- [x] **Stale failure memories appear in fresh chats after a hotfix** - Session 33 fresh chat showed a saved "Ad batch generator failing on HashiAid" diagnostic from before the Kie text-to-image hotfix. Fixed in Session 35: old `saved_from_chat` rows now expire from the WYWG opener after 24h, while real notices/actions remain visible.
- [x] **Streaming chunk stitching drops spaces** - Observed `recommendation.I have` and `batch.Got` in streamed chat text. Fixed v2 chat rendering with light punctuation spacing normalization.
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
- [ ] `connected_services.shop_domain` overloaded for 3 platforms (Shopify domain / Meta ad account ID / Klaviyo account public ID). Rename to `platform_account_id` in a future cleanup migration.
- [ ] Klaviyo router endpoints have no `@limiter.limit` decorators due to a project-wide FastAPI + slowapi bug with `@limiter.limit` + Pydantic body + `Depends(get_current_user)` (same issue affects Loop/Shopify endpoints). Fix requires upgrading slowapi or restructuring the decorator stack.

---

## Integration Status

| Integration | Auth | Pull | Push | Status |
|-------------|------|------|------|--------|
| Shopify | ✅ OAuth | ✅ Products, orders, revenue | ⬜ Landing pages | 🟡 80% |
| Meta Ads | ✅ OAuth | ✅ Campaigns, ads, metrics | ✅ Launch/create | ✅ 90% |
| Loop Subs | ✅ API Key | ✅ LTV, churn, MRR | N/A | ✅ 95% |
| Stripe | ✅ Live | ✅ Checkout, webhooks | ✅ Balance top-up | ✅ 95% |
| Klaviyo | ✅ API Key | ✅ Lists, Segments, Flows, Campaigns, Metrics | ✅ Email template + campaign drafts (PR 3); SMS/push/flows in PR 4 | 🟡 60% |

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
| 26 | 2026-04-17 | **Context leak hardening + credit balance on signup**. Closed brand brain cache, billing, conversation history leaks. Added `auto_create_credit_balance` trigger (migration 029) so new users see 2500 tokens immediately. Fixed sandbox to refresh env vars on cached hit. Loop onboarding step + Meta coming-soon banner. |
| 27 | 2026-04-20 | **Klaviyo PR 1 — integration + data sync**. Private API key auth, 5 new brand brain files (lists/segments/flows/campaigns/email-performance), daily pg_cron refresh (migration 030), Settings UI Klaviyo card, worker handlers for klaviyo_initial_sync + klaviyo_daily_sync tasks. Generation tools come in PR 3. |
| 28 | 2026-04-20 | **Klaviyo PR 3 — email draft push**. Client write methods (create/update_template, create_campaign), `email-assets` bucket (migration 032), Gemini 3 Pro hero pipeline, HTML email renderer (600px / inline CSS / liquid tags / no em-dashes), top-5 campaigns' HTML backfill during sync, 4 sandbox tools, sandbox→Railway HTTP tool-bridge + `agent_events` log, `klaviyo_draft_created` SSE + frontend deep-link card, per-email billing, orchestrator Flow B prompt + Mailer SOP. 136 tests passing, frontend builds clean. PR opened for human review + manual QA inside Klaviyo. |
| 29 | 2026-04-21 | **Full QA run + Loop RLS fix + cinematic redesign kickoff**. End-to-end Playwright QA as a fresh single-store merchant: signup → Shopify connect → Loop connect → `/v2/home` with real $14.9k / 384 orders / $42.61 AOV. Found + fixed Loop's `store_connection` anon-key RLS bug mid-run (commit 5f49fea, same pattern as Shopify PR 28). Added RLS lesson to CLAUDE.md. Shipped XS row: Loop onboarding copy fix ("API & Integrations" → "API tokens"), `/v2` default now routes to `/v2/chat`, login/signup/post-onboarding all land in v2 cinematic chat. Wrote three plan docs for the next arc: cinematic-chat UX, sandbox persistent-daemon, daily-plays generator. |
| 30 | 2026-04-24 | **E2B fully removed — native Anthropic tool_use is the only runtime**. Phases 8 + 11 shipped: `use_native_tool_use` + `native_mutations_allowed` flipped to True defaults then removed as fields; `backend/app/sandbox/` deleted (5,789-line executor.py gone); `_CONTEXT_CACHE` + `_build_business_context` + `invalidate_business_context` + `SandboxEvent` extracted to `app/chat/business_context.py` + `app/chat/events_schema.py`; 9 `sandbox_manager.destroy` call sites swapped to `invalidate_business_context`; `e2b` + `e2b-code-interpreter` dropped from requirements.txt; frontend Planner sandbox pipeline (dashboard-store/sandbox-execution-panel/pages/dashboard.tsx) deleted as dead code. Net: +1,809 / -9,145 lines. Also shipped: `cache_control: ephemeral` on the full `TOOL_SCHEMAS` prefix (prompt-cache win, ~1,648 tokens/turn avoided on hits) and migration 060 (`shopify_orders` composite index on `user_id, order_created_at DESC`, ~60% expected p95 reduction on dashboard `/stats`). Sandbox-persistent-daemon plan obsoleted — replaced by Operations Layer path. Backend: 889 tests pass; one pre-existing failure on main unchanged. Frontend: build green in 223ms. |
| 31 | 2026-04-26 | **Production brand-owner browser QA: data chat + ad generation.** Production bot answered real Shopify/Meta pulse check and generated HashiAid ads, but found P0/P1 issues: bot/dashboard metric mismatch, skipped product-image verification, 3-ad request rendered 4 ready assets after regen, old ad strip leaked into a fresh chat, fresh-chat 3-ad plan reused the same 2pm-fatigue/offer creative spine, self-critique hallucinated image details, supplement/Meta policy risks, 1254x1254 outputs, em-dash leakage, unclear review-gallery CTA, and streaming chunk spacing bugs. Patched chat-store reset, hardened diversity context, verified fresh production chats no longer show stale ad strips, and added hard TOF visual diversity memory/guardrails so repeat TOF ads rotate archetypes instead of defaulting to the same quote-card look. Deep follow-up tested a realistic owner flow with strategy analysis + two 2-ad batches + critique, finding repeated CDN URL guessing, duplicate image rendering, OCR-less launch critique, and severe supplement compliance gaps. Full reports: `docs/qa-runs/2026-04-26-brand-owner-ad-bot-qa.md`, `docs/qa-runs/2026-04-26-deep-brand-owner-production-qa.md`. |
| 32 | 2026-04-26 | **Production TOF generation hotfix.** After product image verification started working, live TOF ad generation failed with `ads_generated: 0` because the Kie provider rejected calls without source images. Routed source-less Kie calls to `gpt-image-2-text-to-image`, preserved image-to-image when sources exist, and made `generate_ad_batch` return `IMAGE_GENERATION_FAILED` with per-ad error details when all provider calls fail. |
| 33 | 2026-04-26 | **Deep production full-batch QA after Kie hotfix.** Fresh production chat planned a 5-ad HashiAid 90-Day Reset batch from live Shopify/Meta/Loop data and confirmed the product image gate. Kie GPT Image 2 generated the full batch and survived TOF text-to-image plus MOF/BOF image-to-image paths, but QA found serious product-state issues: wrong `quiz.ibdassist.com` entry point selected for HashiAid, long-batch `Reconnecting...` / noisy `:` and `→` stream artifacts, internal QC regenerated 4 rejected ads and inflated the visible strip to `AD BATCH · 8 ready`, final chat lost all generated ad URLs, follow-up bot could not surface actual URLs, stale failure memory appeared in a fresh chat, and compliance risks persisted around Hashimoto's/Synthroid/autoimmune/testimonial claims. |
| 34 | 2026-04-26 | **Full-batch ad artifact and entry-point hotfix.** Patched native `image_generated` events to include total/batch/format/funnel/headline metadata, made the native router persist final generated images as hidden ad `batch-json`, and taught the frontend to cap the live ad strip and replace internal QC regenerations by `ad_index` so a 5-ad request stays five visible final assets. Loaded assistant messages now parse persisted ad batch JSON, and stream completion falls back to live generated URLs even when the agent text omits the fence. Also made `get_offer_entry_points` product-filterable with filter-aware cache keys and an explicit no-cross-product warning. Verification: targeted backend chat tests, frontend chat-store tests, Python compile, `git diff --check`, and frontend production build all passed. |
| 35 | 2026-04-26 | **Post-hotfix production QA follow-up.** Fresh production chat verified the new HashiAid entry-point path: the bot excluded account-wide/Axos/Masterclass offers, surfaced the two HashiAid-specific offers, and flagged `quiz.ibdassist.com` as a potentially generic umbrella quiz before asking for confirmation. QA also found two smaller UX bugs: stale 3-day-old `saved_from_chat` failure diagnostics still appeared in new-chat WYWG, and streamed setup text could run into `◆ THE LEDE` without a paragraph break. Patched WYWG to expire old chat-saved rows after 24h and added chat text cleanup that puts glyph section headers on their own paragraph. Verification: events router tests, WYWG tests, clean chat message tests, Python compile, `git diff --check`, and frontend production build passed. |
| 36 | 2026-04-26 | **Production deep QA + single-slot regen artifact fix.** Added $25 QA balance (live account now had room for more generation) and ran a fresh HashiAid owner flow: live data audit, entry-point confirmation, product-image gate, two-batch approval, then Batch A recovery after the bot first said "generating" without calling the ad tool. Batch A produced 3 assets, but reload showed a persisted gallery with a rejected one-slot regen asset because the second `generate_ad_batch` call reset `ad_index` to 1. Patched native persistence and frontend message commit to prefer final response ad URLs over raw stream order, strip duplicate markdown ad images when structured ad payload exists, tightened "do not promise generation without tool call" prompt wording, and added supplement/Meta compliance guards plus fewer false brand-name flags. Verification: `test_native_router.py`, `test_native_tools_klaviyo.py`, ad artifact/frontend batch tests, Python compile, `git diff --check`, and frontend production build passed. |

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
- All users on Opus 4.7 (removed Normal/Pro toggle; model-preference feature deleted 2026-04-21)
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
