# Music / Nightlife Cluster: RaveSwitch VJ, RaveSwitch Live (VoxLive), Connect & Create AI (Tekk Rack), The Memory Loop

Method: read-only inspection of the four Lovable projects through the Lovable MCP (list_files, read_file, get_project), plus the local repos /home/user/vib-finder (branch claude/sweet-brown-asyghx) and /home/user/rennstern. Nothing was modified and no credits were spent. Line counts come from file reads where noted. Lovable "source" links below point to each project's editor page plus the file path read.

Project links:
- RaveSwitch VJ: https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f
- RaveSwitch Live: https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35
- Connect & Create AI: https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c
- The Memory Loop: https://lovable.dev/projects/791574fd-3f6b-435a-9679-1c8d9f4356a3

## What does each app really do and how mature is it?

### Takeaway
All four are real, working client-side apps and not mockups. RaveSwitch VJ is the most ambitious: a WebGL multi-camera auto-VJ with WebRTC phone cameras and a built-in Web Audio "AI" techno generator, but its RTMP streaming is a stub. RaveSwitch Live has been completely rebuilt as "VoxLive", a free autotune vocal studio with a real AudioWorklet DSP. Connect & Create AI is "TEKK RACK", a hardtekk synth with sequencer, LLM loop maker and WAV export. The Memory Loop is a finished, small, Vienna-themed sci-fi text adventure. None of them is published on Lovable.

### Cited Findings
**RaveSwitch VJ (created 2026-06-25, last edited 2026-07-16, not published)**
- Lovable description: "browser-based VJ tool for multi-camera DJ streams with auto-mixing ... connect multiple cameras or phones directly through your browser". The screenshot shows a PROGRAM preview, presets (Hardtek Fast, Industrial SlowPan, RaveCam Chaos, Minimal Clean), framing (Fit/Safe Crop/Free Zoom), orbit paths, motion textures (Noir, Vintage, Cyber Neon, Glitch Pulse), "Try Demo (no setup)" and "Add Phone Cam" — [get_project, RaveSwitch VJ](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- Code size: about 21 mixer components (AIEnginePanel, AutopilotPanel, CapsulesPanel, CompositorPanel, HighlightsPanel, PerformancePad, RecordPanel, SafetyPanel, ScenesPanel, StreamPanel, ...), about 17 mixer lib files (store, autoMixer, audioBus, audio/analyser, recorder, webgl/renderer.ts, webgl/shaders.ts, scenes/highlights/capsules/safety/perfPad stores), src/lib/ai/{aiSynth,aiVisual,prng}.ts and src/lib/webrtc/{signaling,cameraPeer,mixerPeer}.ts. Routes: /, /mixer, /join, /join/$sessionId, /api/public/rtmp/start and /stop — [list_files, RaveSwitch VJ](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- The .lovable/plan.md specifies a GLSL pan/zoom/orbit shader with per-source aspect uniforms, a dark-techno UI pass, and an "AI Engine (offline virtual camera)": a Web Audio techno synth (kick, bass drone, hats, sweeps, seeded 16-step patterns in Minimal/Hardtek/Industrial styles) plus a WebGL2 FFT-driven visual (wavefield/industrial/fractal) exposed through canvas.captureStream(30). It says "No new npm deps ... server-side gen; RTMP work" are out of scope — [.lovable/plan.md](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- The auto-mix is beat-driven: `AutoMixerScheduler.tick()` derives a cut interval from `preset.cutBeats`, intensity and energy, then calls `triggerCut()` on beat boundaries — [src/lib/mixer/autoMixer.ts](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- Recording is real: canvas.captureStream + audio tracks into MediaRecorder at 12 Mbps (VP9/VP8/H.264), 720p/1080p/4K, downloaded as a file — [src/lib/mixer/recorder.ts](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- RTMP streaming is a stub: `rtmp.start.ts` says "TODO: spawn ffmpeg piping WebRTC canvas → RTMP. Stubbed for now." and returns a fake `stub-xxxx` streamId. The StreamPanel store defaults to `rtmp://live.twitch.tv/app` and only calls this stub — [src/routes/api/public/rtmp.start.ts, src/lib/mixer/streamStore.ts](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)

**RaveSwitch Live (created 2026-07-17, last edited 2026-09-03, not published) — confirmed to be VoxLive**
- The Lovable description still says "auto VJ and multi-cam streaming tool ... WebRTC", but it is stale. The only plan file is `voxlive-free-autotune-virtual-studio-2026-08-14.md`, which states "This replaces the earlier RaveSwitch plan as the project focus". The live screenshot shows "Sing in tune. Instantly. For free. ... Go live / Use headphones" — [get_project and .lovable/plan](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)
- No WebRTC or mixer code is left. The files are src/components/studio/* (DevicesPanel, EffectCard, KnobConsole, PitchDisplay, PresetBar, StageView, TakeLibrary, TrackPanel, VoiceControls), src/lib/{audio-engine, presets, scales, takes-storage, track-analysis(.worker), wav, demo-tracks}.ts and public/autotune-processor.js. There is one route (index) and no supabase folder — [list_files](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)
- The DSP is real. `autotune-processor.js` (about 330 lines) is a single AudioWorklet with a ring buffer, an adaptive noise gate, decimated coarse-to-fine autocorrelation pitch detection (70–1100 Hz), scale snapping by root and scale mask, a dual-tap crossfaded granular pitch shift with retune speed, formant/"character", humanize and wet/dry controls, and a feedback/howl watchdog that ducks output — [public/autotune-processor.js](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)
- Plan scope: key/scale with auto-key detection, effects (reverb, delay, compressor, doubler), backing-track upload plus demo set, multitrack take recording with re-tune, WAV/WebM export, IndexedDB take storage, presets (Natural, Pop Tune, Hard Tune, Ethereal, Robot), a stage "Live" view. "No backend, no accounts". Chrome desktop is the target — [plan](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)
- package.json has no Supabase and no AI SDK, only the standard TanStack Start + shadcn stack — [package.json](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)

**Connect & Create AI (created and last edited 2026-10-06, not published) — confirmed to be the "TEKK RACK" hardtekk synth**
- The Lovable description is generic boilerplate ("platform that enables users to build applications and integrate them with AI"). The screenshot shows "TEKK RACK — Hardtekk instrument", with Auto Maker (Warehouse/Acid/Industrial/Festival/Dark, tempo/swing/intensity knobs, "Make"), a Sound section (Screech/Acid/Stab/Pluck/Pad, drive/cutoff/res/env/ADSR), a 16-step sequencer (Kick/Hat/Clap/Riff), a "Deck · play along" (YouTube/SoundCloud/direct URL or file), "Export WAV 1×/2×/4×/8× loops" and a two-octave computer keyboard — [get_project](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- `TekkEngine` (src/lib/synth/engine.ts, about 520 lines) is a real Web Audio engine. It has a 10-voice poly synth (detuned 3-saw "screech", noise and basic waveforms) with filter envelope and ADSR, synthesized kick/hat/clap, a lookahead scheduler (120 ms, 20 ms tick) with swing, a WaveShaper drive and compressor master bus, a play-along deck through MediaElementSource with crunch/filter/rate, and `recordLoops()` that captures N loops to Float32 for WAV export (uses the deprecated ScriptProcessorNode) — [src/lib/synth/engine.ts](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- The "AI" Auto Maker is a TanStack `createServerFn` (`generateHardtekk`). It calls `https://ai.gateway.lovable.dev/v1` with `LOVABLE_API_KEY` via @ai-sdk/openai, model `openai/gpt-6-astra`, and asks for a JSON patch. On any failure it falls back to `generateLocalPatch()` ("AI is offline here — built a local tekk instead"). It has a 3.5 s in-memory rate limit — [src/lib/synth/generate.ts](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- Has vitest tests (src/test/app-routing.test.tsx) — [list_files](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)

**The Memory Loop (created 2026-10-26, last edited 2026-10-26, not published)**
- Description: "interactive sci-fi narrative set in a surreal, navigable futuristic city ... train, memory archive, root core ... clickable hotspots and keyboard controls ... branching choices ... multiple endings ... save and resume ... ambient sound". The title screen reads "// SIGNAL RECOVERED · VIENNA · LOOP ∞ ... You wake on a night train that never arrives. The city outside is almost Vienna." It notes "Fictional narrative · saved locally in your browser" — [get_project](https://lovable.dev/projects/791574fd-3f6b-435a-9679-1c8d9f4356a3)
- The engine (src/lib/game/engine.ts, about 170 lines) is a pure, deterministic state machine: seeded mulberry32 RNG, a per-seed door code, stats (coherence/signal/glitch), fragments/flags, location requirements, choice effects, endings, a "loop again" option and a hardened `validateSave()`. It has a unit test file (engine.test.ts). Content lives in story.ts, and there are CityCanvas/GameScreen components and localStorage saves — [src/lib/game/engine.ts and list_files](https://lovable.dev/projects/791574fd-3f6b-435a-9679-1c8d9f4356a3)

### Inferences
- Maturity ranking: VoxLive DSP and Tekk Rack engine are the most production-grade audio code. RaveSwitch VJ is broad and visually advanced, but its headline "streaming" feature is fake. Memory Loop is complete but small.
- The two duplicates to flag: (1) "RaveSwitch Live" is not a duplicate of RaveSwitch VJ any more, but its name and Lovable description are misleading and should be renamed to VoxLive at migration. (2) Tekk Rack's synth and RaveSwitch VJ's `aiSynth.ts` overlap: both are Web Audio techno generators with seeded 16-step patterns (Minimal/Hardtek/Industrial vs Warehouse/Acid/Industrial). TekkEngine is the stronger one and should become the single shared engine.

### Gaps
- I did not read every RaveSwitch VJ component (mixer.tsx, renderer.ts, shaders.ts, mixerPeer.ts) line by line, so exact LOC for those and whether every panel (Highlights, Capsules, Safety) is fully wired are unverified.
- I did not read VoxLive's audio-engine.ts or takes-storage.ts, so how much of the plan (reverb/doubler, multitrack re-tune, IndexedDB) is implemented versus planned is inferred from file names only.
- I did not open story.ts, so Memory Loop's content length (number of locations and endings) is unknown.
- Nothing was runtime-tested. All maturity claims come from code reading.

## Is each one purely client-side, or does it need servers?

### Takeaway
VoxLive and The Memory Loop are 100% client-side. Tekk Rack is client-side except one optional LLM server function that already has a local fallback. RaveSwitch VJ is client-side for local cameras, demo and recording, but phone cameras need Supabase Realtime for signalling and in practice a TURN server. Real streaming to Twitch/YouTube needs a media server, because RTMP from a Worker is not possible.

### Cited Findings
- RaveSwitch VJ signalling uses Supabase Realtime broadcast channels `raveswitch:${sessionId}` with hello/bye/offer/answer/ice messages. The ICE config is Google STUN only (`stun:stun.l.google.com:19302`) with no TURN server — [src/lib/webrtc/signaling.ts](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- `CameraPublisher` (phone side) adds tracks to an RTCPeerConnection and sends an offer about 600 ms after joining, so it is pure peer-to-peer from phone to mixer browser — [src/lib/webrtc/cameraPeer.ts](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- RaveSwitch VJ includes `src/integrations/supabase/*` and `supabase/config.toml` and depends on `@supabase/supabase-js` and `qrcode` (for the phone join QR) — [package.json](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- VoxLive: "Everything runs in the browser; takes are stored locally ... No backend, no accounts" — [plan](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35)
- Tekk Rack's only server code is `generateHardtekk` (Lovable AI gateway). The engine and WAV export are browser-only — [generate.ts / engine.ts](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- Memory Loop saves to the browser ("saved locally in your browser") and its engine is pure functions — [get_project / engine.ts](https://lovable.dev/projects/791574fd-3f6b-435a-9679-1c8d9f4356a3)

### Inferences
- Without TURN, phone cameras on mobile data or venue Wi-Fi behind symmetric NAT will often fail to connect. A club deployment needs TURN (for example Cloudflare Realtime TURN or a hosted coturn).
- To make "hosts stream live" real, the RTMP stub has to be replaced by WHIP egress from the browser to a media service (for example Cloudflare Stream Live, which accepts WHIP/RTMP and re-streams). ffmpeg cannot run inside a Cloudflare Worker, so this is the L-sized part.
- Supabase Realtime signalling can stay as is if the VJ is folded into VibeCoin, because VibeCoin already uses Supabase. As a standalone app it would need its own Supabase project, or a Cloudflare Durable Object for signalling.

### Gaps
- I did not check pricing or limits of Cloudflare Stream/Realtime TURN in this pass (no web research was done; scope was code-grounded).

## Best integration target, concrete approach and effort

### Takeaway
VibeCoin is the natural home for three of the four as a "Studio" area for hosts and artists (Tekk Rack, VoxLive, and the RaveSwitch auto-VJ in record-only mode first). Rennstern is a poor fit: its "streamseeds" feature is file transfer, not live streaming. The Memory Loop is off-strategy and only fits as an optional After Dark mini-game or an archive.

### Cited Findings
- VibeCoin routes already include artists.$slug, hosts.$slug, host, community, match, wallet, vienna-techno, vienna-live-music. src/lib has autopilot, live (an event-source ingestion pipeline: providers, evidence, pipeline.server), social.functions and ai-gateway.server.ts — [local repo /home/user/vib-finder/src](file:///home/user/vib-finder/src)
- VibeCoin's `src/lib/live` is about event-data "live sync" (sources, jobs, verification), not live video. No livestream or Twitch/RTMP code exists. The only "youtube" match is a social-platform enum in a migration — [/home/user/vib-finder/src/lib/live.functions.ts](file:///home/user/vib-finder/src/lib/live.functions.ts)
- VibeCoin's `ai-gateway.server.ts` already abstracts providers (lovable, gemini, claude, openai) with env-chosen models — [/home/user/vib-finder/src/lib/ai-gateway.server.ts](file:///home/user/vib-finder/src/lib/ai-gateway.server.ts)
- Rennstern "Streamseeds" is described in code as "Rennsterns eigener Datei-Transfer" (its own file-transfer service), with file-size/expiry/IP limits and sponsor ads. `RennstreamLivePanel` lists "rennstream.rennstern.com transfers (sent from the Mac app)". The video wall is CyberVideoWall.tsx (556 lines) plus admin.video-wall — [/home/user/rennstern/src/lib/streamseeds.ts](file:///home/user/rennstern/src/lib/streamseeds.ts); [/home/user/rennstern/src/components/admin/RennstreamLivePanel.tsx](file:///home/user/rennstern/src/components/admin/RennstreamLivePanel.tsx)
- VibeCoin docs note the Supabase free plan's two projects are already used by Rennstern and Rennstream — [/home/user/vib-finder/docs/VIBECOIN_EDITION.md](file:///home/user/vib-finder/docs/VIBECOIN_EDITION.md)
- All four Lovable apps share the same TanStack Start + Tailwind v4 + shadcn/ui + Radix base as VibeCoin, so components drop in without a framework change — [package.json of each project](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)

### Inferences (verdicts)
| Project | Verdict | Feature in VibeCoin | Approach | Effort |
|---|---|---|---|---|
| Connect & Create AI (Tekk Rack) | Integrate into VibeCoin | `/studio/tekk`: artists and hosts make a hardtekk loop or teaser, export WAV, attach it to an event or Community post; reward Vibe Coins for posting a loop | Copy src/lib/synth/* and components/synth/* into `src/features/studio/tekk` (client-only, lazy-loaded route with `ssr:false`). Port `generateHardtekk` onto VibeCoin's existing `ai-gateway.server.ts` (drop the hard-coded Lovable gateway and model). Replace ScriptProcessor recording with AudioWorklet or OfflineAudioContext later | S |
| RaveSwitch Live (VoxLive) | Integrate into VibeCoin as a secondary studio tool, or keep standalone; rename to VoxLive | `/studio/vox`: artist vocal toy and karaoke-night tool, "record a shout-out for your event" | Copy components/studio, lib/*, use-vox-studio and **public/autotune-processor.js** (must stay a static asset loaded through `audioWorklet.addModule`). Client-only route. No backend | S–M |
| RaveSwitch VJ | Integrate into VibeCoin in two phases (Rennstern only as a secondary consumer) | Phase 1: `/studio/vj` for hosts. Phone-cam auto-VJ plus the built-in AI visual, recorded to a 1080p clip and posted as an event aftermovie or Social Autopilot asset. Phase 2: "Live now" on host and event pages through WHIP to a stream provider | Phase 1: copy mixer, webgl and webrtc libs. Reuse VibeCoin's Supabase client for Realtime signalling (channel prefix `vibe-vj:`). Add TURN. Gate host-only behind auth. Phase 2: replace the RTMP stub with WHIP egress to a managed live service, plus a playback embed and an `is_live` field on events and hosts. Alternative: separate subdomain `vj.<domain>` as its own Worker if the bundle size hurts VibeCoin | Phase 1 M, Phase 2 L |
| The Memory Loop | Keep standalone (static page) or archive. Optional low-priority VibeCoin tie-in | Possible "After Dark" Easter-egg game (Vienna night-train theme fits) where finishing an ending awards Vibe Coins | Pure client code. Could be dropped in at `/after-dark/loop` with engine + story + CityCanvas. A coin reward would need a server-validated claim, since saves are client-side and forgeable | S to embed; S extra for the reward |

- Recommended shared structure: create one `packages/audio` (or `src/features/studio/audio`) holding TekkEngine as the single synth, WAV encoder (both Tekk Rack and VoxLive have their own wav.ts), and a beat/energy analyser. Then delete RaveSwitch VJ's `aiSynth.ts` and feed TekkEngine's `MediaStreamDestination` into the VJ audioBus instead. This removes the main duplicate.
- Rennstern: do not merge these into Rennstern. Its "streamseeds" and "rennstream" are transfer/delivery tools, not live video, so there is no streaming stack to reuse. The only plausible Rennstern link is to feed RaveSwitch VJ recordings into the CyberVideoWall or reels as content, which is an export-and-upload path rather than a code merge.

### Gaps
- Bundle-size impact of adding WebGL/WebRTC/AudioWorklet routes to VibeCoin's Worker was not measured.
- Whether VibeCoin's hosts and artists actually want creation tools (product demand) is not evidenced in code. VIBECOIN_EDITION.md does not mention a studio.

## Any Lovable-only dependencies that block migration?

### Takeaway
Nothing blocks migration. The Lovable-specific pieces are a dev-only Vite config package, Lovable error-reporting helpers, and (in Tekk Rack only) the Lovable AI gateway. VibeCoin already lives with the same first two and already has a multi-provider AI gateway to swap in.

### Cited Findings
- All four use `@lovable.dev/vite-tanstack-config` as a devDependency (2.7.6 in RaveSwitch VJ, 2.13.1 in VoxLive) and `nitro` 3.0 beta. VibeCoin also uses `@lovable.dev/vite-tanstack-config` 2.26.5 — [package.json of each](https://lovable.dev/projects/40e13dec-0ccb-4347-9878-cb752f506c35); [/home/user/vib-finder/package.json](file:///home/user/vib-finder/package.json)
- Each project ships src/lib/lovable-error-reporting.ts, error-capture.ts and error-page.ts (the Lovable template boilerplate) — [list_files of each project](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- Tekk Rack's AI maker requires `LOVABLE_API_KEY` and the `ai.gateway.lovable.dev` endpoint, but it degrades to a local generator when the key is missing — [src/lib/synth/generate.ts](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)
- RaveSwitch VJ has a committed `.env` file and Lovable-managed Supabase (supabase/config.toml, integrations/supabase/*). I did not open .env, so no secrets were read — [list_files](https://lovable.dev/projects/20684a92-2326-4db4-a18b-a6700dccb24f)
- Tekk Rack's package.json list was not read, but generate.ts dynamically imports `@ai-sdk/openai` and `ai` — [generate.ts](https://lovable.dev/projects/07717610-0059-45d5-bb3b-892baac1903c)

### Inferences
- Migration chores: drop the lovable-error-reporting files (VibeCoin already has its own copies), point the RaveSwitch VJ Supabase client at VibeCoin's project (no tables seem to be needed, only Realtime broadcast), remove the RaveSwitch `.env` from git history at migration, and switch Tekk Rack to VibeCoin's ai-gateway.
- Cloudflare Workers caveats: AudioWorklet and WebGL are browser-side and unaffected. The RTMP route cannot work on Workers as designed, so it is replaced by WHIP to a managed service. The in-memory `lastCall` rate limiter in generate.ts is per-isolate on Workers and should move to VibeCoin's existing rate-limit approach.

### Gaps
- I did not inspect RaveSwitch VJ's supabase/types.ts for any DB tables, so it is not certain that only Realtime is used. The signalling code suggests that, but other stores (highlights, capsules) were not read.
- I did not check whether any project has a connected GitHub repo already (Lovable MCP get_project does not expose it).
