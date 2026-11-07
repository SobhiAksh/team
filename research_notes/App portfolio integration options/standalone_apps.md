# Standalone Lovable projects and GitHub repos: migrate, integrate or archive

Method: read-only inspection on 2026-11-07. Lovable projects were read with get_project / list_files / read_file (no messages, deploys or remixes). GitHub repos were cloned shallow, read-only, to /home/user/vintage and /home/user/vintagecloset. Already-cloned repos at /home/user/vibfinder, /home/user/vienna-pulse and /home/user/vib-finder were compared on disk. No .env values were opened or printed. Sources are Lovable editor URLs (lovable.dev/projects/<id>), GitHub URLs, or local file paths.

## Per-app verdicts (summary table)

| App | What it really is | Backend | Last edit | Verdict |
|---|---|---|---|---|
| Gentle Day Tracker ("Sensia") | Allergy/pollen/symptom diary, browser-only | None (localStorage); Open-Meteo, no key | 2026-10-05 | Archive on GitHub (repo already in sync); optional later standalone. No integration |
| Rising Path ("RisingSun") | German-language programming learning path with XP and stickers | None (localStorage) | 2026-07-28 | Archive (back up to GitHub first) |
| Sahha Big Bites ("sahha.") | Weekly meal membership + party packs (4–25+ people) | Supabase: profiles, meals, packs, chefs, orders, subscriptions (+ seed data). No Stripe code | 2026-08-03 | Back up, then park as standalone concept; possible future VibeCoin "party pack" partner, not a merge |
| Sentinel Watch ("OSINT Security Radar") | Personal SOC: devices, events, incidents, investigations, watchlists, action queue | Supabase: 16 tables, roles, realtime | 2026-08-06 | Back up, then archive; do not merge into Rennstern/VibeCoin admin |
| Private Preview Launchpad ("Sovereign Compass") | Bilingual (Arabic/English) relationship-strategy + oracle-card + astrology/Human Design app — schema and domain libs only, UI is the blank placeholder | Drizzle/Supabase schema, 13 tables; AI audit table names Perplexity | 2026-10-18 | Back up (contains personal data in a trigger), then archive or keep as private personal project |
| AI App Studio | Empty Lovable TanStack template | None | 2026-10-19 | Archive / delete; nothing to back up |
| Fresh Sync Push (actually "Rennstern Notify") | Rennstern client enquiry portal: enquiries, status, inbox notifications + emails | Supabase: profiles, user_roles, enquiries, notifications; Lovable email templates | 2026-10-05 | Back up, then archive; Rennstern already has booking_requests / booking_status_events / notifications |
| SobhiAksh/Vintage | Empty repo (README "online shop") | None | 2026-10-19 | Archive/delete on GitHub |
| vintageclosetvienna/vintagecloset | Live, active Vintage Closet Vienna shop + staff app (Next.js, Vercel) | Supabase, Stripe, OpenAI | 2026-11-07 | Keep standalone (already own repo); optional later Vercel→Cloudflare move |
| SobhiAksh/vibfinder (VibFinder) | Older GitHub copy of Replit Vibe Coin prototype, with Admin Smart Panel | Express + Drizzle | 2026-10-07 | Archive after porting/noting the Admin Smart Panel (it is NOT in vib-finder) |
| SobhiAksh/vienna-pulse | Older VibeFinder snapshot (3 migrations) | Supabase | 2026-10-07 | Archive; fully superseded by vib-finder |

## What does each app really implement (backend, Supabase tables, Stripe, external APIs)?

### Takeaway
Only three Lovable projects have a real database (Sahha, Sentinel Watch, Fresh Sync Push/Rennstern Notify), plus one with a schema but no UI (Private Preview Launchpad). Gentle Day Tracker and Rising Path are browser-only (localStorage). AI App Studio is an empty template. "Fresh Sync Push" is mis-named: it is a Rennstern enquiry/notification portal, not a GitHub sync tool. None of the Lovable projects contains Stripe code. The only repo with real Stripe integration is vintagecloset.

### Cited Findings
**Gentle Day Tracker** (f20ae311…)
- Lovable description: youth-friendly app tracking food, environment and micro-allergy signals to estimate a daily "allergy load" score; created 2026-07-17, last edited 2026-10-05T02:56, not published. The app is branded "Sensia" in its onboarding screen ("Read the weather of your own body… never a diagnosis") — [Lovable project](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae)
- Routes: index, log, insights, settings; libs: allergens.ts, score.ts, suggestions.ts, env.ts, store.ts — [Lovable project](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae)
- No backend: state is a zustand store persisted to localStorage under key "microallergy.v1" (meals, symptoms, allergen sensitivities, city/lat/lon) — [src/lib/store.ts](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae)
- External APIs: Open-Meteo geocoding, Open-Meteo forecast (temperature, humidity, wind) and Open-Meteo air-quality (European AQI, PM2.5, PM10, ozone, NO2, and six pollen types: alder, birch, grass, mugwort, olive, ragweed). No API key — [src/lib/env.ts](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae)

**Rising Path** (44c9033c…)
- Lovable description talks about AI copilot, jobs/scholarships, Knowledge/Application/Money XP; the actual app is "RisingSun — Dein Programmer-Pfad, AT-fokussiert": a Duolingo-style path of stations (e.g. "Wie ein Computer denkt", "Variablen & Datentypen") with XP, streaks, "Anunaki" sticker companions and a daily reminder — [Lovable project](https://lovable.dev/projects/44c9033c-61e8-4b99-a8a0-b0deed0e12fe)
- No backend: profile (streak, xp {knowledge, application, money}, completed stations, stickers, notification hour) lives in localStorage key "risingsun.profile.v1" — [src/lib/rising-store.ts](https://lovable.dev/projects/44c9033c-61e8-4b99-a8a0-b0deed0e12fe)
- Only 2 routes (index, station.$stationId); created and last edited the same day (2026-07-28) — [Lovable project](https://lovable.dev/projects/44c9033c-61e8-4b99-a8a0-b0deed0e12fe)

**Sahha Big Bites** (e52aad27…)
- The Lovable description mentions oversized fusion meal kits, party packs for 4–25+ people, Supabase and Stripe. The current UI is "sahha. — A weekly meal membership: Your week, already fed", from €11.90 per meal, with Home / My week / Journey / Standard / You tabs — [Lovable project](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)
- Supabase tables: profiles (with loyalty_points, referral_code), meals, packs (group_size 4/8/12/25_plus; occasion netflix/birthday/office/rave), chefs, orders (payment_status unpaid/paid/failed), subscriptions. Seeds 12 meals, 8 packs (e.g. "Rave Recovery 4/12") and 6 chefs in Berlin, Vienna, Amsterdam, Milan, Rotterdam, Sofia — [migration 20260803093901](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)
- Membership model in code: "subscription-first. No marketplace, no à la carte ordering"; plans Sahha 4 / Sahha 5; portion prices €11.90 / €13.40 / €14.90 — [src/lib/membership.ts](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)
- No Stripe: package.json has no stripe or @stripe dependency; payments exist only as a payment_status column — [package.json](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)
- Created 2026-08-03 09:37, last edit 2026-08-03 10:13 (one session) — [Lovable project](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)

**Sentinel Watch** (4b46d1b2…)
- Branded "OSINT Security Radar — Personal SOC": event triage, investigations, "honest actions" (system actions are queued, never faked) — [Lovable project](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)
- Supabase tables: profiles, user_roles (app_role), devices, investigations, incidents, events, entities, watchlists, action_queue, sensor_logs (magnetometer readings), evidence_files, notification_rules, event_entities, investigation_entities, incident_events; owner RLS + "admin read all"; realtime on events and incidents. Five migrations, all 2026-08-06 — [migration 20260806060610](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)
- Routes: overview, events, incidents, investigations, devices (list + map), sensors, watchlists, settings; components include proximity-finder and protect-me-now — [Lovable project](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)
- Despite the "OSINT enrichment" description, insights are computed only from DB data: "no external calls, no live scanning" — [src/lib/insights.ts](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)
- Last edit 2026-08-06 — [Lovable project](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)

**Private Preview Launchpad** (9fe2210b…)
- The home route is still Lovable's blank placeholder ("IMPORTANT: Replace this placeholder"), and the screenshot is the blank Lovable logo — [src/routes/index.tsx](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c)
- Confirmed content: a Drizzle migration "0000_sovereign_compass_schema" with 13 tables: profiles (birth date/time/place/tz, default locale 'ar'), contacts, relationships, interaction_events, shadow_observations, goals, goal_contacts, action_steps, protection_plans, oracle_sessions, journal_entries, shadow_settings, ai_audit (provider default 'perplexity') — [drizzle/migrations/0000_sovereign_compass_schema.sql](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c)
- Domain libs: astro/ephemeris.ts, hd/bodygraph.ts (Human Design), domain/oracle.ts (30-card bilingual Arabic/English "surreal oracle deck", spreads like Negotiation, Conflict, Protection, each card text says it "cannot prove anyone's intent, predict the future"), conflict.ts, safety.ts, timing.ts, contact-profile.ts — [src/lib/domain/oracle.ts](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c)
- The signup trigger hard-codes the owner's own birth date, time window and birthplace as the default profile seed (values intentionally not repeated here) — [drizzle/migrations/0000_sovereign_compass_schema.sql](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c)
- Created 2026-10-18 11:39, last edit 11:45 (six minutes) — [Lovable project](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c)

**AI App Studio** (1189905b…)
- Only template files (ui/*, router, one test); index.tsx is the blank placeholder; no Supabase or domain code. Confirmed empty template — [src/routes/index.tsx](https://lovable.dev/projects/1189905b-2f30-4d2d-8ccb-fd67e7df7b58)

**Fresh Sync Push** (472ecdef…)
- Lovable's AI description ("utility to automate synchronization to a GitHub repository") does not match the code. The page title is "Rennstern Notify — enquiries and project updates": clients send an enquiry, follow its status, and get emails "from notify.rennstern.com" — [src/routes/index.tsx](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)
- Supabase tables: profiles (email_notifications), user_roles, enquiries (status received/in_review/confirmed/declined/completed), notifications (realtime) — [migration 20261005032336](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)
- Server notifier inserts inbox notifications and sends "notification-update" template emails; it falls back to origin https://rennstern.com — [src/services/notify.server.ts](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)
- Routes: admin, enquiries, inbox, settings, auth, Lovable email webhook/preview routes — [Lovable project](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)
- Last edit 2026-10-05 03:27 (about 15 minutes after creation) — [Lovable project](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)

**SobhiAksh/Vintage**
- One commit "Initial commit" (2026-10-19), a single file README.md with the text "# Vintage / online shop". Empty — [GitHub](https://github.com/SobhiAksh/Vintage) (local /home/user/vintage)

**vintageclosetvienna/vintagecloset**
- Monorepo with VintageCloset/ (Next.js 16 app "vintage-closet-app") and Zeitaufteilung/time-tracker (a stock create-next-app README) — [GitHub](https://github.com/vintageclosetvienna/vintagecloset) (local /home/user/vintagecloset)
- Dependencies: @supabase/supabase-js, stripe + @stripe/stripe-js, openai, onnxruntime-web, sharp, @vercel/analytics — [VintageCloset/package.json](https://github.com/vintageclosetvienna/vintagecloset)
- Real Stripe: api/checkout, api/checkout/session, api/webhooks/stripe, lib/stripe.ts — [VintageCloset/src](https://github.com/vintageclosetvienna/vintagecloset)
- Hosted on Vercel (vercel.json, region dub1, daily cron /api/staff/storage-cleanup); about 30 hand-written Supabase SQL files (products, orders, discount codes, events, journal, team chat, creator profiles, 2FA) — [VintageCloset/vercel.json](https://github.com/vintageclosetvienna/vintagecloset)
- Features: shop, cart, checkout, admin (orders, journal, team, media), staff app (guided camera, photo arranger, drafts, shop sale, team chat), creators, events — [VintageCloset/src](https://github.com/vintageclosetvienna/vintagecloset)
- Very active: latest commit 2026-11-07 00:06 ("Photo arranger: merge duplicate drafts…"), 200+ commits, history back to 2025-04-17 — [GitHub](https://github.com/vintageclosetvienna/vintagecloset)

**SobhiAksh/vibfinder** (GitHub name "VibFinder")
- README "Vibe Coin — nightlife social discovery app"; Express server + Vite client; single commit 2026-10-07 "Fix local dev server binding" — /home/user/vibfinder
- Compared with vib-finder's frozen backup legacy/replit-vibe-coin, vibfinder has three files the backup lacks: client/src/components/AdminSmartPanel.tsx (159 lines), client/src/pages/Admin.tsx (87), client/src/lib/adminSettings.ts (51), plus edits to App, Radar, Home, Wallet, Community, Matcher, Profile, layout files and server/index.ts, routes.ts, storage.ts — `diff -rq /home/user/vibfinder/client /home/user/vib-finder/legacy/replit-vibe-coin/client`
- The Smart Panel is a set of radar filter presets ("Night Pulse", "Low-Key", "High Energy": underground, liveMusic, erotic, autoSync, tripMode, radiusKm) stored only in localStorage — /home/user/vibfinder/client/src/components/AdminSmartPanel.tsx, /home/user/vibfinder/client/src/lib/adminSettings.ts
- No AdminSmartPanel exists anywhere in /home/user/vib-finder — `find`/`grep` on /home/user/vib-finder

**SobhiAksh/vienna-pulse**
- README is the original Lovable prompt "Vienna Pulse — VibeCoin ecosystem MVP, Events first"; single commit 2026-10-07 — /home/user/vienna-pulse/README.md
- Every route file in vienna-pulse/src/routes also exists in vib-finder/src/routes, and all 3 vienna-pulse migrations exist in vib-finder/supabase/migrations (vib-finder has 21) — comparison on /home/user/vienna-pulse vs /home/user/vib-finder

### Inferences
- Lovable's AI-generated descriptions are unreliable (Fresh Sync Push, Rising Path, Sahha's Stripe claim), so verdicts should rest on the code, not the project name.
- Sentinel Watch and Sahha are the most complete Lovable-only projects (real schemas, many routes); both were built in one day and not touched since August.

### Gaps
- Did not read every route/component, so UI completeness (e.g. Sahha checkout flow, Sentinel proximity finder) is estimated from file lists.
- Could not check whether any Lovable project's Lovable Cloud database holds real user data (query_database was not used, to stay strictly read-only on code). The user should check this before deleting Sahha, Sentinel or Fresh Sync Push.

## Does it fit inside Rennstern or VibeCoin, or should it stay separate?

### Takeaway
Nothing here should be merged wholesale. Fresh Sync Push duplicates features Rennstern already has. Rising Path, Sentinel Watch and Gentle Day Tracker serve different audiences from VibeCoin and Rennstern. Sahha's party packs are the only real link to VibeCoin, and only as a possible later partner offer. Gentle Day Tracker and VibeCoin both have weather code, but the overlap is small: they use different providers and different data.

### Cited Findings
- Rennstern already has booking_requests, booking_messages, booking_status_events and notifications tables, an MCP get-booking-status tool, and outbound mail via notify.rennstern.com — /home/user/rennstern/supabase/migrations (20260830023514, 20260830155250, 20260831125429), /home/user/rennstern/src/lib/mcp/tools/get-booking-status.ts, /home/user/rennstern/src/lib/outbound-mail.server.ts
- Fresh Sync Push's enquiries → status → inbox/email flow is the same idea ("Received, in review, confirmed. Every change lands in your inbox") — [Fresh Sync Push index.tsx](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0)
- VibeCoin's weather module uses MET Norway Locationforecast 2.0 (api.met.no), giving temperature, wind, rain probability/amount and a symbol code, with caching and rate-limiting, for event "rain incoming / heat caution" flags — /home/user/vib-finder/src/lib/weather/provider.server.ts, /home/user/vib-finder/src/lib/weather/model.ts
- Gentle Day Tracker uses Open-Meteo (forecast + air-quality + pollen), not MET Norway; VibeCoin has no pollen or air-quality code (grep for open-meteo/pollen in vib-finder/src returned nothing) — [Gentle Day Tracker env.ts](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae); /home/user/vib-finder/src
- VibeCoin already has its own rewards economy: vibe_profiles, coin_ledger, store_items, store_redemptions, community posts, match_swipes — /home/user/vib-finder/supabase/migrations/20261107040000_vibecoin_social_rewards.sql
- VibeCoin's Vibe Passport promises "Fixed first-exploration rewards; no blockchain, wallet, exchange or financial promise"; host studio exists at /dashboard; "payments… are not enabled" — /home/user/vib-finder/README.md
- Rising Path XP has three axes (knowledge/application/money) earned by finishing coding-lesson stations, stored only in the browser — [rising-store.ts](https://lovable.dev/projects/44c9033c-61e8-4b99-a8a0-b0deed0e12fe)
- Sahha packs include occasion "rave" ("Rave Recovery 4": "Post-club carbs, delivered late") and group sizes up to 25+ — [Sahha migration](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d)
- Sentinel Watch is built around personal devices, IPs, Bluetooth trackers and magnetometer sensor logs, owned by one user — [Sentinel migration](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1)

### Inferences
- **Fresh Sync Push → do not migrate.** Rennstern covers it with booking requests, status events and notifications. At most, look at its notify.server.ts and the user-facing "inbox" idea once, in case Rennstern lacks a client-side inbox page, then archive.
- **Sahha → keep separate (or park).** It is a food-subscription business with chefs in six cities, a different product from nightlife discovery. A VibeCoin tie-in would be a partner listing or a "party pack" perk in store_items once VibeCoin has payments. That does not need Sahha's code: copying the 8 seeded pack rows would be enough. Its schema has no Stripe, so even standalone it needs a payments build.
- **Rising Path → do not merge into VibeCoin.** It teaches programming; VibeCoin rewards nightlife exploration. Its XP is localStorage-only, so there is nothing to migrate, and the coin_ledger design already covers VibeCoin's needs.
- **Sentinel Watch → not an admin view.** Its data model is personal device and threat telemetry, not app-security monitoring for Rennstern/VibeCoin users. Admin security for those apps is better served by Supabase advisors/logs and Cloudflare dashboards. It is a personal side project.
- **Gentle Day Tracker → stays separate if kept.** Pollen/AQI could become a small "air & pollen" add-on to VibeCoin's open-air event cautions via Open-Meteo, but that is a ~50-line feature you could write fresh; the tracker itself (food and symptom diary) is a health app with different audience and privacy needs.
- **Private Preview Launchpad → separate personal project.** It holds sensitive personal and relationship data and is unrelated to the three products.

### Gaps
- Did not check whether Rennstern already has a client-facing "inbox" page equivalent to Fresh Sync Push's /inbox (only tables and mail code were checked).

## Is it worth keeping at all (maturity, last edit date)?

### Takeaway
Only vintagecloset is clearly alive (commit today). All Lovable side projects were built in one short session and abandoned: 6 of 7 had no edits after the day they were created, and Gentle Day Tracker was last edited 2026-10-05. Recommendation: one cheap backup step (export to GitHub, archived) for each project with real code, then delete from Lovable. Do not do a full Cloudflare migration for any of them now.

### Cited Findings
- Dates (created → last edited): Gentle Day Tracker 2026-07-17 → 2026-10-05; Rising Path 2026-07-28 06:41 → 15:40; Sahha 2026-08-03 09:37 → 10:13; Sentinel Watch 2026-08-06 06:03 → 09:23; Fresh Sync Push 2026-10-05 03:12 → 03:27; Private Preview Launchpad 2026-10-18 11:39 → 11:45; AI App Studio 2026-10-19 20:02 → 21:25. None is published — Lovable get_project for each ID ([GDT](https://lovable.dev/projects/f20ae311-a8b6-43d3-8dfc-067cff7c01ae), [Rising Path](https://lovable.dev/projects/44c9033c-61e8-4b99-a8a0-b0deed0e12fe), [Sahha](https://lovable.dev/projects/e52aad27-e460-4f65-9c22-a7f87500175d), [Sentinel](https://lovable.dev/projects/4b46d1b2-9b61-4e21-bc7c-b7897bca85e1), [PPL](https://lovable.dev/projects/9fe2210b-e902-405c-a708-49c9f681684c), [AI App Studio](https://lovable.dev/projects/1189905b-2f30-4d2d-8ccb-fd67e7df7b58), [Fresh Sync Push](https://lovable.dev/projects/472ecdef-4808-4dbc-99b0-b2251bb586a0))
- vintagecloset last push 2026-11-06T23:06Z; Vintage last push 2026-10-19; VibFinder 2026-10-07; vienna-pulse 2026-10-07; gentle-day-tracker 2026-10-05T02:56:21Z — GitHub list_repos for the user
- vib-finder is the live VibeCoin repo (last commit 2026-11-07 "Add frozen backup of the Replit Vibe Coin prototype source"; README describes the merged VibeCoin edition deploying to Cloudflare Workers) — /home/user/vib-finder

### Inferences — per-app verdict
1. **Gentle Day Tracker: archive (GitHub copy is current).** Optional later standalone migration is simple (no backend, static Worker), but only if the user wants a health app.
2. **Rising Path: back up to GitHub, then archive.** Small and client-only; no product fit.
3. **Sahha Big Bites: back up to GitHub (code + the two migrations with seed data), then park.** It is the most "business-like" concept, but has no payments and no edits for 3 months. Revisit only as a separate venture.
4. **Sentinel Watch: back up, then archive.** Big schema, personal use only; no integration.
5. **Private Preview Launchpad: back up privately, then archive or keep as a personal project.** The repo must stay private because the migration contains the owner's birth data. Its name on Lovable is misleading; rename the GitHub repo to e.g. "sovereign-compass".
6. **AI App Studio: delete.** Empty template; no backup needed.
7. **Fresh Sync Push (Rennstern Notify): back up, then archive.** Rennstern already has the same function.
8. **SobhiAksh/Vintage: archive or delete on GitHub.** Empty; the real shop is vintageclosetvienna/vintagecloset. The two are related only by name and intent. Vintage is an unused placeholder, not a duplicate of the code.
9. **vintageclosetvienna/vintagecloset: keep standalone.** Already on GitHub with Supabase + Stripe; it currently runs on Vercel, not Cloudflare. A Cloudflare move (Next.js on Workers via OpenNext) is optional and should be a separate decision. The Vercel cron and the sharp/onnxruntime image pipeline make that move non-trivial. The Zeitaufteilung/time-tracker folder looks like a leftover create-next-app scaffold.
10. **SobhiAksh/vienna-pulse: archive.** Fully superseded: all routes and migrations exist in vib-finder.
11. **SobhiAksh/vibfinder (VibFinder): archive, but note first that the Admin Smart Panel (AdminSmartPanel.tsx, Admin.tsx, adminSettings.ts) and its server edits are not in vib-finder's legacy backup.** Archiving on GitHub keeps them readable; if the user wants the Smart Panel presets in VibeCoin's /admin, port the preset idea (~300 lines, localStorage-only) before or after archiving.

### Gaps
- Could not confirm whether the gentle-day-tracker GitHub repo has every Lovable commit. The timestamps match to the second (Lovable last edit 2026-10-05T02:56:20Z vs GitHub push 02:56:21Z), which strongly suggests Lovable's GitHub sync is current, but the repo was not cloned.
- Lovable Cloud database contents (real rows) were not checked for Sahha, Sentinel, Fresh Sync Push or Private Preview Launchpad.

### Backup status table (where does the code live today?)

| Lovable project | GitHub repo? | Code only on Lovable? | Action before deleting from Lovable |
|---|---|---|---|
| Gentle Day Tracker | Yes: SobhiAksh/gentle-day-tracker (pushed 2026-10-05T02:56:21Z, matches last Lovable edit) | No | None needed; archive repo |
| Rising Path | No | **Yes** | Connect to GitHub / export, then archive |
| Sahha Big Bites | No | **Yes** (incl. 2 Supabase migrations with seed data; has a .env file, do not commit it) | Export to private repo; check Lovable Cloud DB for real rows |
| Sentinel Watch | No | **Yes** (5 migrations; has .env) | Export to private repo; check DB |
| Private Preview Launchpad | No | **Yes** (schema + domain libs; has .env; personal data in trigger) | Export to a private repo only |
| AI App Studio | No | Yes, but empty template | Nothing to keep; delete |
| Fresh Sync Push (Rennstern Notify) | No | **Yes** (2 migrations, email templates; has .env) | Export to private repo; check DB |

The full GitHub list for the user (9 repos) has no repo for any of these except gentle-day-tracker: vib-finder, team, vintagecloset, rennstern, rennstream, Vintage, VibFinder, vienna-pulse, gentle-day-tracker — GitHub list_repos (2026-11-07).
