# Vibe Coin (Replit) × VibeFinder (Lovable): comparison and merge plan

Sources inspected (November 2026):
- **Replit:** "Vibe Coin": https://replit.com/@sobhiaksh/Vibe-Coin
- **Lovable:** "VibeFinder" (shown in the UI as VibeCoin Events): https://lovable.dev/projects/e88cc228-3349-43a9-b0f9-eabfd9e8ffe1

## TL;DR

| | Replit: Vibe Coin | Lovable: VibeFinder |
|---|---|---|
| What it really is | A polished **front-end prototype**. Every screen uses hard-coded mock data. | A **working full-stack product** with a real database, auth, admin and jobs. |
| Stack | React + Vite + wouter, Express server, Drizzle/Postgres | TanStack Start (SSR), Supabase (Postgres + Auth + Realtime + Storage), Drizzle migrations |
| Backend | `server/routes.ts` is empty. The schema has only a `users` table. | 17+ server modules: events, hosts, submissions, live sync, weather, autopilot, ads, blog, admin RBAC |
| Look and feel | Pure black, neon pink `#ff0080` and electric blue. Orbitron headings, glassmorphism, glow, a social-app feel (bottom nav, left/right sidebars). | Same idea in a calmer, editorial style. Black with oklch neon pink/blue/green, IBM Plex Mono meta labels, neon cards. |
| City | Generic (Berlin/London placeholders) | Vienna-first, with districts, Vienna time and SEO landing pages |
| Tests | None | 26 unit and regression tests (Vitest) |

**Verdict:** Replit has the **vibe and the social features**. Lovable has the **engine**. The enhanced version should use Replit's UX and social layer as the skin and Lovable's data model and pipelines as the core.

## Feature-by-feature

| Feature | Replit | Lovable | Keep in merged app |
|---|---|---|---|
| Home feed | Party Radar widget (city, weather, heat level), steps and coins counters, genre chips, event cards | NOW / TONIGHT / THIS WEEK, search, category filter, hero, data-mode banner | **Replit layout** fed by Lovable queries and real weather |
| Map / radar | Fake map image, price-pin markers, Vibe Check %, attendees, heat bar, filter popover | Real Leaflet map, district heat, near-me radius, time/price/category/energy filters, list sync | **Lovable engine** with Replit's price pins, hover cards and heat widget |
| Event detail | none | Full page: facts, artists/venue/host, tickets, save, Join the Vibe, share, related, "why" panel | Lovable |
| Matcher (swipe to find rave buddies) | Tinder-style cards (mock) | none | **New:** match on shared saved/joined events |
| Community feed (X-style and "disco sphere" view) | Mock posts | none | **New:** real posts on Supabase |
| Profile | Stats, crew stories, tags, post grid | My Vibe Life: saves, interest, attendance, memories, follows | Replit layout with Lovable data |
| Wallet / coins | Balance, Buy Coins, **Staking 12% APY**, store, history | Vibe Passport: achievements, no money or crypto by design | See the open decision below |
| Bounce / steps | Daily bounce, mystery box, steps counter | `/bounce` device motion counter, device-local points | Lovable counter in the Replit wallet UI |
| Ride button (Uber) | Yes | No | Keep (deep link) |
| Host studio | none | `/dashboard`: submissions, moderation, analytics | Lovable |
| Artists / venues / hosts pages | none | Yes, plus SEO pages (techno, free events, tonight, and so on) | Lovable |
| Weather | Hard-coded "12°C" | MET Norway forecasts, reminders, quiet hours | Lovable |
| Admin console | none | RBAC, integration health, source registry, dead letters | Lovable |
| Social Autopilot (Instagram @vibfinder) | none | Daily carousel, approval flow, official Graph API | Lovable |
| Live event ingestion | none | Firecrawl, Perplexity research, AI categorization, evidence | Lovable |
| Ads / monetization | none | Ad slots, `ads.txt`, sponsored labels | Lovable |
| Erotic / sex-positive filter | Toggle on the radar | none | Optional, 18+ gated (see decisions) |

## Things to watch

1. **Financial claims.** Replit's "Vibe Staking 12% APY" and "Buy Coins" imply a financial product. That brings real legal exposure in the EU (MiCA, consumer protection). Lovable deliberately avoided it. Recommendation: keep coins as **non-transferable reward points** (earn by bouncing, exploring and attending; spend in the store on perks and merch) and drop APY/staking.
2. **Mock people and posts.** Replit's Matcher and Community use stock photos of real-looking people. They need real users, profiles, consent, blocking and reporting before launch.
3. **Demo data.** Lovable's database still holds demo events only. Real Vienna sources must be registered through Admin → Integration & Health.
4. **Unverified flows.** Lovable notes that the signed-in flows have never been tested end to end. Create an admin account first.
5. **Lovable-specific runtime.** VibeFinder uses `@lovable.dev/vite-tanstack-config`, Lovable AI gateway helpers and a `LOVABLE_CRON_SECRET`. These need replacements when it moves to Replit: a standard Vite config, a direct OpenAI/Anthropic key, and Replit Scheduled Deployments for the cron jobs.

## Recommended migration path

**Target:** one Replit app, "Vibe Coin", upgraded in place, using Lovable's Supabase project as the backend.

1. **Bring the code over.** Connect Lovable → GitHub (Lovable editor → GitHub → Connect). Then in Replit, import that repo as a new Repl, or let the Replit agent pull it into Vibe Coin.
2. **Backend.** Keep Supabase: point `SUPABASE_URL` and `SUPABASE_ANON_KEY` at the existing project, and put the service-role key in Replit Secrets. All tables, RLS, auth and migrations come along unchanged.
3. **Skin.** Apply Replit's design tokens (pure black, `#ff0080`, `#3b82f6`, Orbitron, glass panels, glow) and its navigation (bottom nav on mobile, left and right sidebars on desktop) across Lovable's routes.
4. **Wire Replit screens to real data:**
   - Home → `events.functions` / `discovery` queries and real weather
   - Radar → Lovable Leaflet `vienna-map` styled with price pins, Vibe Check % and the heat widget
   - Wallet → Passport achievements plus bounce points as **Vibe Coins** (non-financial), store and history
   - Profile → My Vibe Life data in the Replit layout
5. **New social layer** (Supabase tables with RLS):
   - `posts`, `post_likes`, `post_comments` for the Community feed and sphere view
   - `match_profiles`, `match_swipes`, `matches` for the Matcher, with candidates who saved or joined the same events
   - `crews` and `crew_members` for profile crews
   - `coin_ledger` as an append-only points ledger, and `store_items` / `redemptions`
6. **Replace Lovable-only pieces:**
   - The AI gateway becomes a direct provider key.
   - The cron endpoints (`/api/public/*-sync`, `social-autopilot`) are called by Replit Scheduled Deployments using a `CRON_SECRET`.
7. **Verify.** Run `vitest`, create an admin user, and check signed-in save, follow, host submission, moderation and the new social flows on mobile.
8. **Publish** from Replit. Optionally keep Lovable as a staging editor on the same GitHub repo.

## Open decisions (owner: Sobhi)

- [ ] Upgrade the existing Replit "Vibe Coin" in place, or create a new Repl, "Vibe Coin Pro"?
- [ ] Keep Supabase (recommended) or move to Replit Postgres?
- [ ] Coins: reward points only (recommended), or a real token or purchase flow?
- [ ] Include the 18+ erotic / sex-positive category (it needs age gating)?
- [ ] City scope: Vienna first (Lovable data) or multi-city from day one?
