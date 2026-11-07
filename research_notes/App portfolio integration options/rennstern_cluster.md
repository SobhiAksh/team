# Rennstern cluster: Rennstream, Film Hub, Skill Spark, Trend Weaver vs. Rennstern

Method: read-only code inspection. Rennstern = local repo `/home/user/rennstern` (main @ 726e72b, shallow history). The four Lovable projects were read with Lovable `get_project` / `list_files` / `read_file` (no messages sent, nothing changed, no credits spent). Source notation: `lovable:<project-id>/<path>` = file read via the Lovable MCP at HEAD; local paths = Rennstern repo. Secret files (`.env`) were not opened.

Project IDs: Rennstream `7995e594-9258-443e-9a5c-dbe51868cc85`, Film Hub `1a24f44a-338a-4084-9036-29da14f6f743`, Skill Spark `84f1dee6-db5f-45d2-9862-bb8d8336bc96`, Trend Weaver `47a02e4b-aba0-4232-bc59-beb459fe1ce7`.

## Q1: What does each project actually implement (real backend vs UI only, tables, APIs)?

### Takeaway
Only Rennstream is a real product with a backend: 8 tables, auth, admin, app API for the Mac app, R2 storage. Film Hub has a small real Supabase schema, but its "job bot" makes up fake leads with random numbers, and all its tables are wide open to the public. Trend Weaver is a working single-page tool with no database: AI release text, AI cover, still-image video made in the browser, and YouTube upload. Skill Spark is an empty placeholder app that holds only skill markdown files and a plan document.

### Cited Findings
**Rennstream (published, last edited 2026-11-02)**
- Published, publicly visible; description: file transfer with anonymous email-based delivery and "a server-to-server API that enables external desktop applications to initiate transfers" — [get_project](https://lovable.dev/projects/7995e594-9258-443e-9a5c-dbe51868cc85)
- Schema (migration 1): enums `app_role` (super_admin, operations, moderation, analytics, support) and `transfer_status`; tables `profiles`, `user_roles`, `transfers`, `transfer_files`, `transfer_events`, `payments` (default 99 cents EUR), `stickers` (signal cards with callsign/serial), `admin_logs`, `abuse_reports`; RLS policies; `handle_new_user` trigger on `auth.users`; `sticker_serial_seq` — `lovable:7995e594/supabase/migrations/20260915011157_e576e6a6-….sql`
- Storage bucket `transfers` with per-user folder policies — `lovable:7995e594/supabase/migrations/20260915011237_77a00e07-….sql`
- Server code: `transfers.functions.ts`, `transfers.server.ts`, `object-store.server.ts`, `app-api.server.ts`, `werbepass.server.ts`, `admin.server.ts`, `auth.server.ts`; API routes `api/app/transfers` (index, `$code.finalize`, `$code.events`), `api/preview/$code`, `api/public/cleanup`; admin pages for revenue, security, system, transfers, users; `t.$code` download page; `drizzle/` schema plus one migration — `lovable:7995e594` file listing
- Deps include `@lovable.dev/cloud-auth-js`, `aws4fetch` 1.0.20 (R2 signing), `qrcode`, `drizzle-orm`, `nitro` — `lovable:7995e594/package.json`
- CLAUDE.md: "TanStack Start on Cloudflare Workers"; uses R2 "when the `R2_*` secrets are set, Supabase Storage otherwise"; it already has its own `.github/workflows/deploy-cloudflare.yml` — `lovable:7995e594/CLAUDE.md`
- UI: "Up to 5.0 GB per transfer, 20 files", "Sign in for more space", account optional — [get_project screenshot](https://lovable.dev/projects/7995e594-9258-443e-9a5c-dbe51868cc85)

**Rennstern Film Hub (not published, last edited 2026-08-30)**
- Three tables: `companies` (name, branch, status, email, tags, next_action…), `profile` (singleton: name, email, cv_url, portfolio_url, roles, subject/body templates with [ROLE]/[NAME]/[COMPANY] placeholders), `job_leads` (title, company_id, location, email, source, applied). **Every table has the RLS policy "Public full access … TO anon, authenticated USING (true)"** — `lovable:1a24f44a/supabase/migrations/20260720041345_….sql`
- Public `assets` storage bucket (used for CV/portfolio uploads); anon users may insert, update and delete — `lovable:1a24f44a/supabase/migrations/20260720041410_….sql`
- `runJobBot` does not search anywhere. It builds 10 "leads" by mixing the profile roles with random existing companies or made-up names (`"Nord"/"Halo"/"Kino"… + "Studio"/"Works"…`) and random cities, with `source: "internal-bot"`. Its comment says it is a stub that "can later be swapped for a real scraping/AI call or an n8n webhook" — `lovable:1a24f44a/src/lib/jobbot.functions.ts`
- `sendEmail` sends plain text through Resend **via the Lovable connector gateway** (`connector-gateway.lovable.dev/resend/emails`, needs `LOVABLE_API_KEY` + `RESEND_API_KEY`); the default sender is `onboarding@resend.dev` — `lovable:1a24f44a/src/lib/email.functions.ts`
- Routes: `index` (companies), `jobs`, `profile`, `api/public/webhooks/records.$type.$id` — `lovable:1a24f44a` file listing
- The current preview shows "No companies yet." with the tabs Companies / Job bot / Profile — [get_project screenshot](https://lovable.dev/projects/1a24f44a-338a-4084-9036-29da14f6f743)

**Rennstern Skill Spark (not published, last edited 2026-09-05)**
- `src/routes/index.tsx` is still the Lovable blank-page placeholder ("IMPORTANT: Replace this placeholder") — `lovable:84f1dee6/src/routes/index.tsx`
- No `supabase/` folder and no lib/server functions. Its only content is 10 skill files in `.agents/skills/` (cinematic-design-language, copywriting-voice, rennstern-ai-interface, rennstern-ai-safety, rennstern-booking-flow, rennstern-creative-engine, rennstern-mcp-tools, rennstern-portfolio, rennstern-seo-metadata, rennstern-services) — `lovable:84f1dee6` file listing
- Its plan proposes 12 workspace skills and says "No site code changes in this step — skills only" — `lovable:84f1dee6/.lovable/plan/rennstern-skill-set-proposal-2026-09-05.md`

**Trend Weaver Studio (not published, last edited 2026-10-03)**
- The UI is Arabic and titled "Mahatta / STATION — daily channel studio" with tabs: today's release, niches, weekly plan, promotion — [get_project screenshot](https://lovable.dev/projects/47a02e4b-aba0-4232-bc59-beb459fe1ce7)
- `generateAiRelease`: through the **Lovable AI gateway** (`ai.gateway.lovable.dev/v1`, model `openai/gpt-6-astra`, Vercel AI SDK) it returns JSON with title, sunoPrompt, description, tags, coverPrompt, pinnedComment, shortHooks, promoPlan and note, for a "faceless AI music channel (Suno-generated instrumentals)" — `lovable:47a02e4b/src/lib/ai-studio.functions.ts`
- `generateCover`: Lovable AI gateway image endpoint (`openai/gpt-image-2.5-sunburst`, 1536x1024, no text) — `lovable:47a02e4b/src/lib/cover.functions.ts`
- `makeStillVideo`: runs only in the browser. It records a canvas with the cover plus the audio track using MediaRecorder into WebM in real time ("a 3-minute track takes about 3 minutes") — `lovable:47a02e4b/src/lib/make-video.ts`
- `publishToYoutube` / `uploadVideo` / `setThumbnail`: a real YouTube Data API v3 multipart upload with an OAuth refresh token (`YOUTUBE_CLIENT_ID/SECRET/REFRESH_TOKEN`), protected by `PUBLISH_PASSCODE`. It sets category 10 (Music) and `containsSyntheticMedia: true` — `lovable:47a02e4b/src/lib/youtube.server.ts`, `lovable:47a02e4b/src/lib/youtube.functions.ts`
- No `supabase/` folder or migrations; state lives in `studio-data.ts`/`generator.ts` on the client — `lovable:47a02e4b` file listing

### Inferences
- Film Hub's "job bot" adds no value; its leads are invented. The real assets are the 3-table schema idea and the CV/portfolio email template.
- Film Hub's open RLS means anyone with the publishable key can read and delete its rows and files. Do not copy that pattern.

### Gaps
- No `query_database` was run (not requested; it may need the backend awake), so row counts in Film Hub and Rennstream are unknown.
- I did not read Rennstream's `transfers.server.ts`, `app-api.server.ts` or the middle migration (`20260915011217_…`) in full.

## Q2: Which features does Rennstern already have (concrete comparison)?

### Takeaway
Rennstern already covers almost everything Film Hub does, and does it better (real Perplexity/AI lead research, outreach drafts, a companies/leads/templates pipeline, contacts and mail). It also already has its own native transfer, "Streamseeds", branded "Rennstream" on `/transfer`. Rennstern does **not** have Trend Weaver's YouTube publishing or AI cover/music-release flow: its `youtube.ts` only parses YouTube IDs and builds embeds. Rennstern also lacks Rennstream's R2 storage, user accounts/premium, and the Mac-app API.

### Cited Findings
**Transfer / Rennstream**
- Rennstern `/transfer` page is titled "Rennstream — große Dateien senden | Rennstern" ("send large files"). It promises "up to 12 GB … per transfer … Up to 5 GB per file" and renders `TransferConsole` + `RennstreamShip` — `/home/user/rennstern/src/routes/transfer.tsx`
- `TransferConsole` imports from `@/lib/streamseeds.functions`, so transfers on rennstern.com already run on Rennstern's own backend — `/home/user/rennstern/src/components/transfer/TransferConsole.tsx`
- Streamseeds is described as "Rennsterns eigener Datei-Transfer" (Rennstern's own file transfer). Defaults: maxFileBytes 5 GiB, maxTotalBytes 12 GiB, 40 files, 7-day expiry, ad slots — `/home/user/rennstern/src/lib/streamseeds.ts`
- Server functions: start/finish/read/downloads/preview/status/overview/settings/revoke — `/home/user/rennstern/src/lib/streamseeds.functions.ts`; tables `streamseeds`, `streamseed_files`, `streamseed_events`, `streamseeds_settings` — `/home/user/rennstern/supabase/migrations/*.sql`; download page `s.$token.tsx`, zip route `api/public/streamseeds.$token.zip.ts`, admin `admin.streamseeds.tsx`
- Streamseeds stores files in the **Supabase Storage** bucket `streamseeds` (`const BUCKET = "streamseeds"`). The Rennstern repo has **no** `aws4fetch`/`R2_ACCESS` references, so it has no R2 path — `/home/user/rennstern/src/lib/streamseeds.server.ts` line 13; grep of `src` + `package.json`
- Rennstern already holds UI ported from Rennstream: `MysteryCourier.tsx` (255 lines) and `mystery-card.ts` (141 lines), matching the Rennstream files `mystery-courier.tsx` / `mystery-card.ts` — `/home/user/rennstern/src/components/transfer/`, `lovable:7995e594` file listing
- Links to the separate Rennstream app today: `rennstream.$.tsx` 301-redirects `rennstern.com/rennstream/*` to `https://rennstream.rennstern.com` — `/home/user/rennstern/src/routes/rennstream.$.tsx`; `rennstreamTransfers` reads `rennstream.rennstern.com/api/app/transfers` with a bearer key stored as studio secret `rennstream_app_key` (admin `RennstreamLivePanel`) — `/home/user/rennstern/src/lib/rennstream-admin.functions.ts`, `/home/user/rennstern/src/routes/api/public/studio/rennstream-key.ts`, `/home/user/rennstern/src/lib/studio-secrets.server.ts`
- about-this-app.md still says "Rennstream (file transfer) is a separate Lovable project at `https://rennstream.rennstern.com`" — `/home/user/rennstern/about-this-app.md` line 21
- The migration plan's next step 1 is to merge Rennstream into this repo, and the Paris Supabase project `koqtwrxhcowoaxakyqli` already has the Rennstream schema but no data — `/home/user/rennstern/docs/MIGRATION.md` (STATUS/HANDOFF → Next)

**Outreach / jobs (vs Film Hub)**
- `pipeline_companies` has name, branch, status, location, email, website, tags[], last/next_action, next_action_date, notes, contact_id, request_id and archived_at. This is a superset of Film Hub's `companies` columns — `/home/user/rennstern/supabase/migrations/20260902131631_f038d2e3-….sql`
- Pipeline server functions: companies, leads (save/accept/status/provider/scope), `suggestLeads`, `generateOutreachDraft`, templates and `sendOutreach` — `/home/user/rennstern/src/lib/pipeline.functions.ts`
- Lead research providers: Perplexity (web research), OpenAI and Lovable — `/home/user/rennstern/src/lib/lead-research.ts`; the outreach AI writes drafts that are reviewed in admin, "versendet wird nie automatisch" (never sent automatically) — `/home/user/rennstern/src/lib/outreach-ai.server.ts`
- Contacts with labels, senders and `adminSendMail` — `/home/user/rennstern/src/lib/contacts.functions.ts`; the mail transport supports Resend directly (the plan names Resend as the target) — `/home/user/rennstern/docs/MIGRATION.md`

**Promo / YouTube (vs Trend Weaver)**
- `youtube.ts` only has `parseYoutubeId`, `youtubeThumb` and `youtubeEmbedUrl` (wall embeds). There is no upload or OAuth — `/home/user/rennstern/src/lib/youtube.ts`
- The Promo drop box uploads a finished video to the private `media` bucket, writes German title/teaser/description from stills with a vision model, and publishes to the site's video wall/portfolio (`background_videos`), **not** to YouTube — `/home/user/rennstern/src/lib/promo.functions.ts` (header comment, `adminPublishPromo`)
- `promo/plan.ts` handles music/beat-driven cut timing for promo edits (`PromoSound`, seconds per beat) but does not generate music — `/home/user/rennstern/src/lib/promo/plan.ts`
- AI gateway: providers lovable → gemini → claude → openai with fallback, plus `aiSpeechFetch` — `/home/user/rennstern/src/lib/ai-gateway.server.ts`

**Skills (vs Skill Spark)**
- The Skill Spark skill names (rennstern-services, rennstern-portfolio, rennstern-booking-flow, rennstern-mcp-tools…) refer to the Rennstern MCP connector tools. Rennstern serves that MCP at `src/routes/mcp.ts` / `src/lib/mcp` — `lovable:84f1dee6/.lovable/plan/…md`; `/home/user/rennstern/src/routes/` listing

### Inferences
- Film Hub is fully covered by Rennstern's pipeline/contacts/outreach. The only parts missing in Rennstern are the "job application" angle (Sobhi applying for jobs as Director/Editor with CV and portfolio links) and the Film Hub statuses. A "job/application" category or template in `pipeline_templates` would cover it.
- Rennstern's Streamseeds plus the copied MysteryCourier mean the transfer *feature* is already merged. What remains of Rennstream is (a) the R2 storage backend, (b) user accounts, premium tier and payments, (c) the Mac-app `api/app/transfers` API, which the admin panel currently calls remotely, (d) old `rennstream.rennstern.com/t/CODE` links, and (e) admin roles/abuse reports.
- **Important risk:** after the move to Supabase Free (50 MB per file, 1 GB total per MIGRATION.md), Streamseeds' advertised 5 GB per file / 12 GB per transfer will break, because Streamseeds uses Supabase Storage and has no R2 path. Porting Rennstream's `object-store.server.ts` (aws4fetch/R2) into Streamseeds is the most valuable piece of the merge.

### Gaps
- I did not test whether Streamseeds and Rennstream share any storage-key or token format, so old `/t/CODE` links may or may not map onto `s.$token`.

## Q3: Concrete merge path per project (files/tables, effort, risks)

### Takeaway
Rennstream: **merge** (M–L). Port the R2 object store and the app API into Rennstern's Streamseeds; keep the `rennstream.rennstern.com` host and `/t/CODE` links as routes in the Rennstern Worker. Do not carry over a second transfer engine. Film Hub: **archive**, after an optional export of its few rows (S). Skill Spark: **archive**, after copying `.agents/skills/*` into the Rennstern repo (S). Trend Weaver: **keep standalone**: own GitHub repo + Cloudflare Worker, swap the Lovable AI gateway for Gemini/OpenAI (S–M). Optionally lift its YouTube uploader into Rennstern's Promo later.

### Cited Findings
- Rennstream already has the Cloudflare deploy workflow, R2 CORS config and object-store switch: `.github/workflows/deploy-cloudflare.yml`, `.github/r2-cors.json`, `src/lib/object-store.server.ts` — `lovable:7995e594` file listing, `lovable:7995e594/CLAUDE.md`
- MIGRATION.md: Rennstream R2 code is "done, tested against an S3 stand-in, not pushed" (branch `r2-storage` in the rennstream repo); the deploy "creates the bucket `rennstream-files` and its CORS rule itself" — `/home/user/rennstern/docs/MIGRATION.md`
- Owner goal: "Rennstern + Rennstream = ONE project, ONE database … rennstream.rennstern.com keeps working for old links"; copy its tables into the SAME Supabase project "avoid name clashes; owner account only" — `/home/user/rennstern/docs/MIGRATION.md` (STATUS/HANDOFF)
- Name clash candidates: Rennstream defines `public.profiles`, `user_roles`, `has_role`, `is_staff`, `set_updated_at`, `handle_new_user` (trigger on `auth.users`) and `payments` — `lovable:7995e594/supabase/migrations/20260915011157_….sql`. Film Hub also defines `set_updated_at` — `lovable:1a24f44a/supabase/migrations/20260720041345_….sql`
- Lovable-only deps: Rennstream `@lovable.dev/cloud-auth-js` — `lovable:7995e594/package.json`; Film Hub mail via `connector-gateway.lovable.dev` — `lovable:1a24f44a/src/lib/email.functions.ts`; Trend Weaver AI text and images via `ai.gateway.lovable.dev` with Lovable-specific model ids (`openai/gpt-6-astra`, `openai/gpt-image-2.5-sunburst`) — `lovable:47a02e4b/src/lib/ai-studio.functions.ts`, `cover.functions.ts`
- Rennstern's own rules: `*.server.ts` only dynamic-imported, `process.env` only in handlers, never force-push (pushing main syncs to Lovable) — `/home/user/rennstern/docs/MIGRATION.md` (Owner's rules)

### Inferences (recommended paths)
**Rennstream → merge into Rennstern (effort M–L)**
1. Port `object-store.server.ts` (+ `aws4fetch` dep, R2 secrets, `r2-cors.json`) and point `streamseeds.server.ts` at it instead of `client.storage.from("streamseeds")`. This is a prerequisite for the Supabase-Free move, whatever happens to the Rennstream UI (M).
2. Re-implement `api/app/transfers` (+ finalize/events) on top of Streamseeds, so the Mac app and `RennstreamLivePanel` call Rennstern itself. Then drop the remote fetch and the `rennstream_app_key` studio secret (M).
3. Host routing: replace the 301 in `rennstream.$.tsx` with a host check in the Worker. `rennstream.rennstern.com/*` serves `/transfer`, and `/t/CODE` maps to the streamseed download page or to a legacy-lookup route (S).
4. Accounts/premium/roles/abuse/stickers: only port if Sobhi wants public sign-ups. Otherwise skip, because Rennstern already has admin-gate auth. If they are ported, prefix the tables (`rs_profiles`, `rs_user_roles`…) or fold them into existing tables to avoid the `profiles`/`payments`/`set_updated_at` clashes (L if ported).
5. Werbepass already exists on the Rennstern side (`api/public/werbepass-check.ts`), so Rennstream's `werbepass.server.ts` call becomes an in-process call.
6. Then the Paris Supabase project `koqtwrxhcowoaxakyqli` can be deleted, as MIGRATION.md itself notes.
- Risks: storage limits (see Q2), old links, the Lovable auth package, and the two-way Lovable sync on both repos while work is in progress.

**Film Hub → archive (effort S)**
- Nothing to port in code. The jobbot is fake, and companies/email are covered by `pipeline_*` and `contacts`/mail. Optional: add a "Job application" outreach template (subject `Application for [ROLE] – [NAME]` plus CV/portfolio links) to `pipeline_templates`, and store the CV in the private `media`/`app` bucket rather than a public bucket.
- Before archiving, back up the code to GitHub per MIGRATION.md step 5 and turn off its open-RLS backend (security).

**Skill Spark → archive (effort S)**
- Copy the 10 `.agents/skills/*/SKILL.md` files into the Rennstern repo (e.g. `.claude/skills/` or `.agents/skills/`) so local Claude/agents get the brand voice, services and booking rules. Then archive. No app, data or deploy needed.

**Trend Weaver → keep standalone, migrate like Rennstern (effort S–M)**
- It is a different business (Arabic faceless AI-music YouTube channel "Mahatta") with no overlap with Rennstern's film-agency site. Merging it would mix brands.
- Steps: own GitHub repo, Cloudflare Worker (TanStack Start, same template as Rennstern), no Supabase needed (no DB). Replace the Lovable gateway with Rennstern's `ai-gateway.server.ts` pattern (Gemini/Claude/OpenAI) for text, and a direct image API (e.g. OpenAI Images or Gemini image) for covers. Move the YouTube OAuth secrets plus `PUBLISH_PASSCODE` into GitHub/Cloudflare secrets.
- Optional cross-use: its `youtube.server.ts` uploader (about 70 lines, no deps) could later give Rennstern Promo a "also publish to YouTube" button (S). Copy the code; do not merge the apps.
- Risk: the browser MediaRecorder output is WebM in real time. Running the same job server-side is not possible on Workers (no ffmpeg), so keep it client-side.

### Gaps
- The effort sizes are my own estimates from file counts and coupling, not measured.
- Whether Sobhi still uses Trend Weaver/Mahatta is unknown (last edit 2026-10-03, never published).

## Q4: Is there data worth migrating?

### Takeaway
Rennstream: yes, its users. Its transfers expire within days, so the users are the only lasting data. MIGRATION.md already plans to "start the new Rennstream with only the users". Film Hub: probably nothing or very little; the last preview showed "No companies yet". Skill Spark and Trend Weaver have no database, so no data.

### Cited Findings
- Rennstream has 3 Supabase migrations plus a drizzle migration (`0000_user_id_for_email_lookup.sql`) and is published publicly — `lovable:7995e594` file listing, [get_project](https://lovable.dev/projects/7995e594-9258-443e-9a5c-dbe51868cc85)
- "The Rennstream backend on Lovable must be awake … to copy its users; its transfers expire within days, so starting the new Rennstream with only the users is fine" — `/home/user/rennstern/docs/MIGRATION.md` (Notes)
- Film Hub has 2 migrations (tables + public assets bucket), was never published, and was last edited 2026-08-30; its screenshot shows "No companies yet." — [get_project](https://lovable.dev/projects/1a24f44a-338a-4084-9036-29da14f6f743)
- Skill Spark and Trend Weaver have no `supabase/` directory — `lovable:84f1dee6` and `lovable:47a02e4b` file listings

### Inferences
- For Film Hub, a one-time `select` export of `companies`/`job_leads`/`profile` (and any `assets` files, e.g. a CV) is enough. If there are any real companies, insert them into `pipeline_companies` with `branch`/`status` mapped (Film Hub status "To contact" → Rennstern `to_contact`).
- For Rennstream, MIGRATION.md says to copy only the owner account (the owner's rule for Rennstern). Whether other Rennstream sign-ups should be kept is an open owner decision.

### Gaps
- Actual row counts (Film Hub tables, Rennstream users/transfers/payments) were not queried. `mcp__Lovable__query_database` (read-only SELECT) could confirm them if the owner allows it.
- The screenshot date for Film Hub is not stated, so "no companies" may be out of date.
