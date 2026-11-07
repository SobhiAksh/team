# Platform costs and limits for consolidating ~10 apps (Supabase, Cloudflare Workers, Lovable, Replit) — state as of 7 Nov 2026

All pages below were fetched live on 2026-11-07 unless noted. Page "updated" dates are given where the page shows one. Prices are USD and exclude VAT (Supabase applies VAT based on billing address — [Supabase pricing FAQ](https://supabase.com/pricing)).

## Supabase: free-plan limits, Pro pricing, per-project cost, branching, and whether several apps can safely share one project

### Takeaway
The Free plan allows only **2 active projects** (both already used by Rennstern and Rennstream) and pauses after 1 week of inactivity, so 10 apps cannot each get their own free project. On Pro, **one project costs $25/month all-in; each additional project adds at least $10/month (Micro compute)**, so 10 separate projects ≈ $115/month versus $25/month for one shared project. Sharing one project via separate Postgres schemas works technically (documented, supported), but Auth, quotas, keys and outages are shared across every app, which is the main risk.

### Cited Findings
**Free plan (pricing page, live 2026-11-07)**
- Free: $0; unlimited API requests; 50,000 MAU; **500 MB database size per project**; shared CPU, 500 MB RAM; 5 GB egress; 5 GB cached egress; 1 GB file storage; community support. "Free projects are paused after 1 week of inactivity. **Limit of 2 active projects.**" — [Supabase pricing](https://supabase.com/pricing)
- Free: max file upload size **50 MB**; basic CDN; no image transformations; Realtime 200 concurrent peak connections, 2 million messages/month, 256 KB max message; Edge Functions 500,000 invocations; log retention 1 day; **no automatic backups**, no PITR, **no branching**, no custom domains. — [Supabase pricing](https://supabase.com/pricing)
- "Our Free Plan gives you 2 free projects, but you can have as many paused projects as you want. Just pause and unpause them as needed." — [Supabase pricing FAQ](https://supabase.com/pricing)
- "A Free plan project is considered inactive if it does not receive sufficient user database activity over the past week." After pausing, docs now state "there is a **1-year window** to restore the project on the platform from within Supabase Studio." — [Supabase docs: Project Pausing](https://supabase.com/docs/guides/platform/free-project-pausing). **Conflict/history:** a 2024 changelog said paused Free projects were restorable for **90 days** — [Supabase changelog 2024](https://supabase.com/changelog/27497-paused-free-plan-projects-are-restorable-for-90-days); the current docs and a KB article ("Projects paused for more than 1 year can no longer be restored through Supabase Studio") indicate the window is now 1 year — [Supabase KB](https://supabase.com/kb/troubleshooting/restore-project-after-90-days-pause). Treat 1 year as current.

**Pro plan and per-project cost**
- Pro "From $25/month. **First project included. Additional projects from $10/mo.**" Includes 100,000 MAU (then $0.00325/MAU), **8 GB disk per project** (then $0.125/GB), 250 GB egress (then $0.09/GB), 250 GB cached egress (then $0.03/GB), 100 GB file storage (then $0.0213/GB), email support, daily backups kept 7 days, 7-day log retention. — [Supabase pricing](https://supabase.com/pricing)
- "Paid plans include $10/mo in compute credits, enough to cover one Micro instance." "Every project on the Supabase Platform comes with its own dedicated Postgres instance." Compute is billed hourly. — [Supabase pricing](https://supabase.com/pricing)
- Compute sizes: Micro $10 (shared, 1 GB RAM, 60 direct / 200 pooler connections); Small $15 (2 GB); Medium $60 (4 GB); Large $110 (dedicated 2 vCPU, 8 GB); higher tiers up to $3,730. — [Supabase pricing](https://supabase.com/pricing)
- Pro: max file upload 500 GB; Smart CDN; image transformations 100 origin images then $5/1000; Realtime 500 concurrent connections, 5 M messages; Edge Functions 2 M invocations; **projects never paused**; spend cap on by default. — [Supabase pricing](https://supabase.com/pricing)
- **Branching:** not on Free; on Pro "$0.01344 per branch, per hour" (≈ $9.80/month if a branch runs all month, inferred: 0.01344 × 730 h). — [Supabase pricing](https://supabase.com/pricing)
- Custom domain add-on: $10 per domain per month per project (Pro). PITR from $100/month. Log drains $60/drain/month. — [Supabase pricing](https://supabase.com/pricing)
- Pricing FAQ suggests "You can create two projects, one for development and one for production — the Free Plan includes two free projects", or use Branching on Pro. — [Supabase pricing FAQ](https://supabase.com/pricing)

**Sharing one project across apps via schemas (Data API)**
- Custom schemas are supported: `CREATE SCHEMA myschema;`, then "Go to API settings and add your custom schema to 'Exposed schemas'", then grant `USAGE` and `ALL` on tables/routines/sequences to `anon, authenticated, service_role` plus `ALTER DEFAULT PRIVILEGES ... IN SCHEMA myschema`. Page updated 3 Sep 2026. — [Supabase docs: Using Custom Schemas](https://supabase.com/docs/guides/api/using-custom-schemas)
- Client usage: `createClient(URL, KEY, { db: { schema: 'myschema' } })` or per query `supabase.schema('myschema').from('todos')`. With generated types, `createClient<Database>` type-checks against `public` only unless you pass `createClient<Database, 'myschema'>`; the schema "has to be present in the generated Database type." — [Supabase docs: Using Custom Schemas](https://supabase.com/docs/guides/api/using-custom-schemas)
- Auth redirect config is per project: one **Site URL** ("defines the default redirect URL when no redirectTo is specified... critical for email confirmations and password resets") plus an allow-list of Redirect URLs that supports wildcards (`*` = non-separator chars, `**` = any chars; `.` and `/` are separators). Supabase recommends exact redirect paths in production. When using `redirectTo`, email templates may need `{{ .RedirectTo }}` instead of `{{ .SiteURL }}`. Page updated 30 Oct 2026. — [Supabase docs: Redirect URLs](https://supabase.com/docs/guides/auth/redirect-urls)
- Quotas such as 50,000 MAU, 500 MB DB, 1 GB storage, 5 GB egress, 200 Realtime connections are per project/organisation on Free — all apps sharing one project share them. — [Supabase pricing](https://supabase.com/pricing)

### Inferences
- **Cost comparison (Pro):** 1 shared project = $25/month (Micro covered by the $10 credit). 10 separate projects = $25 + 9 × $10 = **~$115/month** minimum, more if any project needs Small ($15) or larger. Hybrid (e.g. 2–3 projects grouping related apps) = $35–$45/month.
- **Free-plan option:** because Rennstern + Rennstream already fill both free slots, putting 8 more apps on Free requires either sharing one of those two projects or pausing projects; pausing after 7 days of inactivity makes Free unsuitable for low-traffic production apps.
- **Pitfalls of one shared project (mostly inferred from how Supabase Auth/Data API work; not stated as warnings in the fetched docs):**
  - One `auth.users` table: a user who signs up for app A is a user of every app. Each app needs its own membership/profile table and RLS policies that check app membership, not just `auth.uid()` — otherwise a JWT from app A can read app B's exposed tables.
  - One Site URL and one set of email templates/SMTP sender for all apps; per-app branding of confirmation/reset emails needs `{{ .RedirectTo }}` logic or Auth hooks.
  - One `service_role`/secret key with access to every app's data; a leak in one app exposes all.
  - Shared quotas, connection pool (Micro: 60 direct/200 pooler) and a single point of failure; a bad migration or a noisy app affects all apps.
  - Each schema must be added to "Exposed schemas" and granted explicitly; generated types must include each schema.
  - Splitting an app out later means dumping its schema plus its subset of `auth.users` and storage objects into a new project — doable but manual.
  - No per-app backups/PITR: restoring one app's data restores the whole database.
- A reasonable middle path: keep Rennstern on its own project (production, revenue), and group the ~10 smaller apps into one or two shared projects with strict per-app schemas and RLS.

### Gaps
- No official Supabase page found (in this pass) that explicitly endorses or warns against multi-app/multi-tenant use of one project's Auth; pitfalls above are inference.
- Whether Free-plan quotas (MAU, egress) are counted per project or per organisation was not confirmed from the fetched text for every line item.
- No EU-specific price difference found; pricing page shows a single USD price list. Frankfurt (eu-central-1) region availability for Free was not re-verified in this pass.

## Cloudflare Workers: free vs $5 paid limits, number of Workers, cron, custom domains/subdomains on one zone, R2

### Takeaway
Workers are billed **per account, not per Worker**: Free allows 100 Workers and 100,000 requests/day shared across all of them; Paid is $5/month minimum for the account with 500 Workers, 10 M requests and 30 M CPU-ms included. Running 10 separate Workers costs the same as one. The Free plan's **10 ms CPU limit** is the real constraint for SSR apps (Cloudflare itself says SSR typically uses 10–20 ms), so the $5 plan is the safe choice. One zone (rennstern.com) can host up to 100 Custom Domains (e.g. app1.rennstern.com …) and 1,000 routes.

### Cited Findings
- Account plan limits (page updated 29 Oct 2026): Requests Free **100,000/day**, Paid no limit; CPU time Free **10 ms**, Paid 5 min (default 30 s); memory 128 MB both; subrequests 50 vs 10,000 per request; env vars 64 vs 128 per Worker; **Worker size 64 MiB (uncompressed) on both plans**; startup time 1 s; **Number of Workers 100 (Free) vs 500 (Paid)**; **Cron Triggers per account 5 (Free) vs 250 (Paid)**; static asset files per Worker version 20,000 vs 100,000; asset file size 25 MiB. — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- "The average Worker uses approximately 2.2 ms per request. Heavier workloads that handle authentication, **server-side rendering**, or parse large payloads typically use **10-20 ms**." Exceeding returns Error 1102. Isolates have "some built-in flexibility" for occasional overruns. — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- Free daily limit resets at midnight UTC; over the limit returns Error 1027 (route can be set to fail open or fail closed). — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- Routes and domains: **1,000 routes per zone; 100 Custom Domains per zone**; 1,000 routed zones per Worker; for more than 100 custom domains per zone use a wildcard route. (Not listed as plan-dependent.) — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- Request body size depends on the zone's Cloudflare plan: Free 100 MB. — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)
- Pricing (page updated 2 Oct 2026): Paid plan "minimum charge of **$5 USD per month for an account**"; Standard model: 10 M requests/month included then $0.30/M; 30 M CPU-ms/month included then $0.02/M CPU-ms; no duration charge; "**Requests to static assets are free and unlimited**"; "no additional charges for data transfer (egress)". — [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- Service bindings: Worker-to-Worker calls via service binding "do not incur additional request fees" (billed as one request plus combined CPU). — [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- Workers Logs: Free 200,000 events/day, 3-day retention; Paid 20 M/month then $0.60/M, 7-day retention. — [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- **R2:** Free 10 GB-month storage, 1 M Class A ops, 10 M Class B ops per month; then $0.015/GB-month (Standard), $4.50/M Class A, $0.36/M Class B; **egress free**. — [Cloudflare Workers pricing (R2 section)](https://developers.cloudflare.com/workers/platform/pricing/)
- KV Free: 100,000 reads/day, 1,000 writes/day, 1 GB; Hyperdrive Free 100,000 queries/day, Paid unlimited. — [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- Workers for Platforms is for "building a platform where your end customers deploy their own sites or applications" — not needed for ~10 own apps. — [Cloudflare Workers limits](https://developers.cloudflare.com/workers/platform/limits/)

### Inferences
- **Many Workers vs one:** no cost difference (billing is per account on requests + CPU). Separate Workers per app give independent deploys, rollbacks, secrets (64/128 per Worker), and blast-radius isolation; one combined Worker only makes sense if apps are truly one product. Recommended: one Worker per app.
- **Subdomains:** app1.rennstern.com … app10.rennstern.com as Workers Custom Domains on the existing zone is within limits (100 per zone) and needs no extra Cloudflare zone plan. Separate apex domains are also fine (each is its own zone on Cloudflare Free).
- **Free vs $5:** on Free the 100k requests/day is shared by all 10 apps, and 10 ms CPU risks 1102 errors on SSR pages. Cron triggers (5 per account on Free) would also be tight across 10 apps. The $5 Paid plan covers all 10 apps at typical small-app traffic with no further charge (10 M requests, 30 M CPU-ms ≈ 1.5–3 M SSR requests at 10–20 ms each).
- **Worker size:** the docs now list 64 MiB for both plans; older docs (pre-2026) listed much lower free limits (3 MB compressed free / 10 MB paid). Bundle size is no longer a reason to upgrade, per the current page.

### Gaps
- Did not separately confirm whether Custom Domains on a Free zone have any extra restrictions (docs list no plan dependence).
- Exact historical date when the Worker size limit changed to 64 MiB was not verified.

## Lovable and Replit: ongoing subscription cost vs self-hosting, and what happens to code/repos when you stop paying, disconnect or delete

### Takeaway
Lovable Pro is $25/month (Business $50) and Replit Core $20/month ($18 billed annually), versus roughly $5 (Cloudflare) + $0–$25 (Supabase) for self-hosting all 10 apps. A Lovable-linked GitHub repo **stays in the user's GitHub account** when the project is disconnected or deleted, and published Lovable apps stay live after downgrading. Data in Lovable's built-in backend (Lovable Cloud) is not the user's Supabase project and must be exported and restored manually. Replit offers zip download and Git/GitHub sync; secrets, database and deployment config are not included.

### Cited Findings
**Lovable**
- Plans (live 2026-11-07): Free $0 (daily 5 build credits up to 30/month, 20 Cloud credits/month, 5 lovable.app domains, invite-only publishing); **Pro $25/month** (100 monthly credits + daily grants, custom domains); **Business $50/month**; Enterprise custom; yearly billing gives "2 months free". Hosting on Lovable is covered by included grants for small apps; heavy traffic draws on the credit balance. — [Lovable pricing](https://lovable.dev/pricing)
- Disconnecting GitHub: "Sync stops." "**Your repository stays on GitHub.** It remains intact with all history and files." "Your project and its code stay in Lovable. Future changes are stored only inside Lovable." Reconnecting later creates a **new** repository; "The original repository stays unchanged on GitHub and is no longer linked to Lovable." — [Lovable docs: GitHub](https://docs.lovable.dev/integrations/github)
- Lovable-created repos are "private by default, on every plan". Lovable syncs one branch at a time (default branch, usually `main`). Code can also be downloaded via "Download codebase" in Project settings → Git or the Code editor ("paid plans"). — [Lovable docs: GitHub](https://docs.lovable.dev/integrations/github)
- Deleting a project: "A connected GitHub repository stays in your GitHub account and **is never deleted with the project**." The published app goes offline within about a minute. "If the project uses the built-in backend, Lovable pauses it. The data in it stays." Project is restorable for 60 days, then permanently deleted. — [Lovable docs: Delete a project](https://docs.lovable.dev/features/projects/delete-project)
- Downgrading/cancelling: "Your published app stays live. Downgrading or canceling does not unpublish your project." — [Lovable docs: Publish](https://docs.lovable.dev/features/publish). Unexpired paid credits are frozen on downgrade to Free — [Lovable FAQ](https://docs.lovable.dev/introduction/faq)
- Built-in backend (Lovable Cloud) "utilizes Supabase's open-source foundation". "Lovable does not move a project from the built-in backend to your own Supabase project in one step. You export your database and restore it into the Supabase project, then move your storage files and your code, and set up authentication, secrets, and any external services again." "Remove database" permanently deletes the database, backups, app user accounts and storage files. — [Lovable docs: Built-in backend](https://docs.lovable.dev/integrations/cloud)

**Replit**
- Core: "$20 / $18 per month, billed annually", "For personal projects and simple apps", "$20 towards more powerful models". Core dropped from $25 to $20 per month (Replit blog, date not captured). Pro shown at $90/month billed annually with higher tiers ($215–$2,000/month) for more credits. — [Replit pricing](https://replit.com/pricing); [Replit blog: Pro plan](https://replit.com/blog/pro-plan)
- Published apps: Starter 1 (30-day), Core unlimited, Pro unlimited; collaboration seats 0 / 5 / 15. — [Replit docs: Core](https://docs.replit.com/billing/plans/replit-core)
- Publishing costs (page updated 19 Aug 2026): Autoscale $2/month base + $0.60/M compute units + $0.40/M requests; Reserved VM from $15/month (0.5 vCPU/2 GB); Static hosting free + $0.05/GB transfer; Scheduled $2/month base. "All publishing costs are deducted from your monthly credits"; unused credits don't roll over. — [Replit docs: Publishing costs](https://docs.replit.com/billing/deployment-pricing)
- Export: "Select the three-dot menu at the top of the file tree and choose **Download as zip**"; fallback `zip -r project.zip .` in Shell. GitHub: link GitHub account, open the Git tool, connect the repository, "Turn on auto-sync in the Git pane for two-way syncing." "Set up secrets, the database, and deployment configuration in the new project—those aren't included in the copy." — [Replit docs: Projects and files](https://docs.replit.com/help/projects-and-files)
- If the zip includes the `.git` folder, commit history comes with it. — [Replit docs: Manually copy a project](https://docs.replit.com/teams/identity-and-access-management/manually-copy-projects)

### Inferences
- **Self-hosting cost for all 10 apps:** Cloudflare Workers Paid $5 + Supabase Free ($0, if fitting into existing projects) or Pro $25 (one shared project) ≈ **$5–$30/month**, versus $25–$50/month for Lovable plus $20/month for Replit (plus any usage above included credits) — before counting Resend/Gemini/Claude usage, which is the same either way.
- Keeping one Lovable subscription only while actively using its AI editor, and syncing to GitHub, is safe: the repo survives disconnect, deletion and cancellation.
- Apps using Lovable Cloud (built-in backend) need a DB dump/restore + storage copy + Auth re-setup into the user's own Supabase project; users' passwords/accounts migration was not confirmed and should be tested.
- Replit apps likely use Replit's own Postgres/secrets; these must be exported separately (pg_dump) because the zip/Git export excludes them.

### Gaps
- Replit pricing page text was partially unreadable through the scraper; Pro monthly (non-annual) price and exact monthly credit amounts per plan were not confirmed. Replit Postgres pricing and DB export procedure were not on the fetched pages.
- Whether Lovable "Download codebase" is unavailable on Free was stated only as "(paid plans)"; GitHub sync availability on the Free plan was not confirmed.
- Whether Lovable Cloud lets you export Auth users with password hashes was not confirmed.

## Monorepo vs multi-repo for ~10 small TanStack Start apps sharing UI and Supabase helpers

### Takeaway
No authoritative source was fetched for this question in this pass; the following is practical inference. For a solo founder with ~10 small apps sharing UI and Supabase helpers, a single pnpm-workspace monorepo (apps/* + packages/ui, packages/supabase) with one Cloudflare Worker per app is usually the lower-maintenance option.

### Cited Findings
- Cloudflare bills per account, not per Worker, and service bindings between Workers carry no extra request fee — so splitting into one Worker per app (as a monorepo would produce) has no cost penalty. — [Cloudflare Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- Lovable syncs one GitHub repo per project, on one branch — [Lovable docs: GitHub](https://docs.lovable.dev/integrations/github); Replit's Git tool also connects one repository per project — [Replit docs: Projects and files](https://docs.replit.com/help/projects-and-files).

### Inferences
- Monorepo pros: shared UI/Supabase helper packages change in one commit; one dependency upgrade (TanStack Start, nitro, wrangler) across all apps; one CI workflow with path filters deploying only changed apps via `wrangler deploy` per app directory; shared generated Supabase types if apps share one project.
- Monorepo cons: Lovable/Replit two-way sync expects one repo per project, so a monorepo implies leaving those editors (or keeping a separate synced repo only for apps still being edited there); a breaking change in a shared package can affect all apps at once (mitigate with per-app pinned versions or tests).
- Multi-repo is simpler only if apps are truly independent or still being edited in Lovable; sharing code then requires publishing private npm packages or git submodules, which is heavier for one person.
- Practical sequence: import each exported Lovable/Replit repo into `apps/<name>` (e.g. with `git subtree` to keep history), extract common code into `packages/*` gradually.

### Gaps
- No official TanStack Start or Cloudflare doc on monorepo layout was fetched; recommendations are inference, not sourced.
