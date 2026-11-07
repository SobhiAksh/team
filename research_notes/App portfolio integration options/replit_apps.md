# Replit app inventory (9 apps)

Research notes, collected 2026-11-07. I only listed and read files on Replit; no app was changed, updated or published.

Every built app comes from the same Replit template: React 18, Vite, Wouter, TanStack Query, shadcn/ui, Tailwind, Framer Motion, and an Express server with Drizzle/Postgres wiring (`DATABASE_URL`). That wiring only matters where the server actually has routes.

---

## 1. Magic Motion Pack (`1a5ba5d6-209b-4163-9cee-bbca50ce2da7`)
- **Status:** Empty. The root holds only an empty `.agents/` folder. There is no code, package.json or replit.md.
- **Real vs mock:** Nothing was ever generated.
- **Overlap:** None.

## 2. Motion Magic Pack (`00724530-03dd-44a9-b5d6-80ab05725ce0`)
- **Status:** Empty. Same as above: only an empty `.agents/`.
- **Overlap:** None.

### Magic Motion Pack vs Motion Magic Pack
Both are empty, so they have no differences. In practice they are duplicates: two abandoned starts of the same idea. Both can be deleted, and nothing needs migrating.

---

## 3. Film Agent Pro (`1c7e981a-e920-4371-86fd-0c547e483db2`)
- **Purpose:** "Werbepass Filmmaker Agent". Clients get video-production quotes through an AI booking chat. Werbepass subscription tiers Silver, Gold and Platinum give 40/50/65% discounts.
- **Stack:** Express backend and Postgres via Drizzle.
  - Tables: `werbepass_plans`, `booking_requests` (includes `chatHistory` jsonb), `equipment_pricing`, `service_packages`, `portfolio_items`, `background_videos`, `support_requests`, `media_assets`, plus chat and auth models in `shared/models`.
- **External APIs (by name only):**
  - `AI_INTEGRATIONS_OPENAI_API_KEY` and `AI_INTEGRATIONS_OPENAI_BASE_URL`: gpt-5.1 streaming chat and gpt-image-1.
  - Stripe via the Replit connector (`server/stripeClient.ts`, `server/webhookHandlers.ts`, queries against a `stripe.products`/`stripe.prices` schema).
- **Real vs mock:** Real.
  - Bookings, media assets and background videos have full CRUD.
  - The booking chat streams over SSE.
  - It has Stripe subscription checkout and a customer portal.
  - The price estimator (`/api/estimate-pricing`) is a hard-coded formula.
- **Size:** About 13 pages (home, booking, dashboard, admin, pricing, thank-you, help/faq, help/guides, help/support, legal/*) and about 22 API routes.
- **Latest notable features:** Stripe subscriptions, plus admin-managed media assets and background videos.
- **Overlap:** Very high with **Rennstern** (booking, chat, admin, reels/portfolio, equipment pricing). Its equipment pricing is close to Rennstern's gear marketplace. Strong candidate to merge into Rennstern: the AI booking agent, Werbepass tiers and Stripe flow.

## 4. Sonic Scribe (`3bae1d25-76c2-4cfc-b9fe-05b9f663d23b`)
- **Purpose:** "SonicScript", which claims to turn audio into sheet music. You upload a file, give a URL or record, pick an instrument, and see notes, frequencies and AI insights.
- **Stack:** Express, multer (50MB uploads) and Postgres with one table, `transcriptions` (notes and frequencyData stored as JSON).
- **External APIs (by name only):** `ASSEMBLYAI_API_KEY`, `PERPLEXITY_API_KEY` (sonar model).
- **Real vs mock:** Mostly fake as music transcription.
  - On upload it sends the audio to AssemblyAI, which is speech-to-text. Each spoken word is then mapped to a note with an arbitrary formula.
  - If no speech is found, the notes are random.
  - The URL and recording routes always return random mock notes.
  - Persistence and the Perplexity "music theory" and "feeling" insights are real.
- **Size:** 1 page (home), 8 API routes.
- **Overlap:** Loosely with **Trend Weaver Studio** (AI music). The only part worth keeping there is the Perplexity mood/theory analysis. The transcription core is not real.

## 5. Magickfeld (`ce99dd15-999a-45b4-ae6f-3770f7f6b01b`)
- **Purpose:** A spiritual platform with Human Design charts, a "shamanic spell/ritual generator", daily guidance, frequency, and a directory of local practitioners/healers.
- **Stack:** Front end only in practice. `server/routes.ts` is the empty template, and no external APIs are called. There is a `db/` folder, but no routes use it.
- **Real vs mock:** Mock. The spell generator uses `setTimeout` and always returns the same hard-coded ritual.
- **Size:** 6 pages (home, human-design, spells, daily-guidance, frequency, practitioners), 0 API routes.
- **Overlap:** None with the six products. It is a standalone niche.

## 6. Future Oasis (`d77526d9-e188-41ae-9a33-1090872014a2`)
- **Purpose:** "NEON DUBAI", a 3D city builder. You zone a 20x20 grid and evolve the city from 1970 to 2077 across 4 stages.
- **Stack:** Express and Postgres with one table, `cities` (grid stored as jsonb). Rendering is in `client/src/components/game/Scene.tsx`, which I didn't open (probably three.js). No external APIs.
- **Real vs mock:** Real save/load/delete with a 30-second autosave. The simulation itself is random growth.
- **Size:** 1 page (Game), 5 CRUD routes.
- **Overlap:** None.

## 7. Hex Game (`a606a6c5-207d-4156-907a-47f4a9c1ac7b`)
- **Purpose:** "XONOS", a hex-tile idle economy game.
  - You build a farm → mill → bakery chain with synergy bonuses and energy management.
  - It uses a simulated "WLD" token with 18% staking APY.
  - "Queen Chronus" tutorial pop-ups guide the player.
- **Stack:** Front end only. The server routes are the empty template. The game is in `client/src/lib/game.ts` plus `client/src/pages/home.tsx`, with no persistence.
- **Real vs mock:** The game logic works client-side. State is lost on reload, and WLD is simulated, not on-chain.
- **Size:** 1 page, 0 routes.
- **Origin:** Built from a pasted German spec, `attached_assets/Pasted-Du-bekommst-jetzt-ein-fertiges-spielbares-XONOS-Hex-Gam_1766554811922.txt`.
- **Overlap:** Only thematically with **VibeCoin**'s token/rewards. There is no shared code.

## 8. Prayer Tracker (`03b8eb2f-dfcb-4cde-b235-af0a7ddc8677`)
- **Purpose:** A tracker for the five Islamic prayers, sunnah prayers and fasting. It computes prayer times with the `adhan` library.
- **Stack:** Front end only. The server routes are empty and there is no persistence; checkmarks live in React state.
- **Real vs mock:** Prayer times are real but fixed to London. The Hijri date "14 Ramadan 1446" is hard-coded.
- **Size:** 1 page, 0 routes.
- **Overlap:** None.

## 9. Bee Hive Ecosystem (`53da25bd-c0c5-4ea8-9734-60c27a6bd98e`)
- **Purpose:** Apparently meant to be the XONOS hex game. Its `attached_assets` holds the same pasted XONOS spec (`..._1766555030864.txt`, about 4 minutes after Hex Game's).
- **Stack:** Blank template. `App.tsx` has no routes, the only page is not-found, and the server routes are empty.
- **Real vs mock:** Nothing is built.
- **Overlap:** Effectively an empty duplicate of Hex Game. It can be deleted.

---

## Overlap with existing products
| Existing product | Overlapping Replit app(s) |
|---|---|
| Rennstern (film agency: reels, booking, chat, admin, gear) | **Film Agent Pro**: strong overlap in booking, AI chat, admin, portfolio and equipment pricing; adds Stripe tiers |
| Rennstream (large file transfer) | None |
| VibeCoin / VibeFinder | Hex Game (XONOS): token and rewards theme only |
| RaveSwitch (auto-VJ) | None |
| Trend Weaver Studio (AI music) | Sonic Scribe: Perplexity music mood/theory insights only |
| Rennstern Film Hub (job leads/CV emails) | None |

## Summary table
| App | Status | Backend/DB | Recommendation |
|---|---|---|---|
| Magic Motion Pack | Empty | – | Delete |
| Motion Magic Pack | Empty, duplicate of the above | – | Delete |
| Film Agent Pro | Real, mature (~13 pages, ~22 routes) | Postgres (8+ tables), OpenAI, Stripe | Merge into Rennstern |
| Sonic Scribe | Partly real; "transcription" is speech-to-text or random | Postgres (1 table), AssemblyAI, Perplexity | Optional: insights only, into Trend Weaver |
| Magickfeld | UI mock (6 pages) | None | Standalone or archive |
| Future Oasis | Small real 3D game | Postgres (`cities`) | Standalone |
| Hex Game (XONOS) | Client-only game | None | Standalone; thematic link to VibeCoin |
| Prayer Tracker | Client-only prototype | None (`adhan`) | Standalone |
| Bee Hive Ecosystem | Empty scaffold (XONOS spec) | None | Delete, duplicate of Hex Game |
