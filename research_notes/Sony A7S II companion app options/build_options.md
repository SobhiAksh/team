# Build options for a DIY Sony α7S II companion app (2026)

## Which build approaches exist, and which can talk to the camera over Wi-Fi/USB?

### Takeaway
No approach can realistically "push" a look preset into an α7S II: the camera's control paths are an old Wi-Fi HTTP/JSON API and USB/PTP (via libgphoto2), neither confirmed to set picture profiles. A browser PWA cannot reach the camera at all on iPhone (no WebUSB) and is unreliable elsewhere. The practical design is a phone-friendly PWA for presets and checklists (the filmmaker dials settings in by hand) plus desktop scripts for post-production.

### Cited Findings
- Sony's original Camera Remote API is HTTP + JSON and **Wi-Fi only**. — [Stack Overflow](https://stackoverflow.com/questions/26004721/sony-camera-remote-api)
- Sony replaced the Camera Remote API with a new Camera Remote SDK and announced that the old API's provision "will be closed". — [DPReview](https://www.dpreview.com/news/0419921057/sony-releases-software-development-kit-sdk-for-remote-camera-access/)
- The current Camera Remote SDK / Camera Remote Toolkit gives high-level remote-control functions for supported Sony cameras. — [Sony SDK](https://support.d-imaging.sony.co.jp/app/sdk/en/index.html), [Sony Pro](https://pro.sony/ue_US/digital-imaging/camera-remote-toolkit)
- PlayMemories Camera Apps (the α7S II-era in-camera app platform) were shut down. Users are archiving the apps and sideloading them with community tools. — [Reddit r/SonyAlpha](https://www.reddit.com/r/SonyAlpha/comments/16pkdvu/how_to_archive_sony_playmemories_camera_apps/)
- libgphoto2 supports USB control of related Sony bodies (e.g. ILCE-7M2: capture, liveview, configuration). An α7S II user reported having no luck setting zoom or focus through gphoto2. — [gPhoto support list](http://www.gphoto.org/proj/libgphoto2/support.php), [gphoto2 issue #533](https://github.com/gphoto/gphoto2/issues/533)
- WebUSB works only in Chromium browsers (Chrome, Edge, Opera, Samsung). It is not supported in Firefox, Safari on macOS or Safari on iOS, and WebKit is "opposed" to it. — [MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API), [TestMu](https://www.testmuai.com/learning-hub/webusb-browser-support/)
- iOS PWAs do not support Web USB, Web Bluetooth, Web Serial, Web NFC or background processing. — [MagicBell 2026](https://www.magicbell.com/blog/pwa-ios-limitations-safari-support-complete-guide)

### Inferences
- **PWA / web app:** this is the best fit for (a) a preset library and (b) shot lists and checklists, because it works offline and on any phone. It cannot control the camera. Calling the camera's Wi-Fi HTTP API from a browser would also run into mixed-content (HTTPS page calling an HTTP device) and CORS problems. Treat camera control as out of scope.
- **AI app builders (Lovable, Bolt, Replit, Glide):** these produce web apps, so they have the same no-camera limits as a PWA. They are good for quickly building the preset and checklist UI. They are not suited to heavy local file processing, because they run in the cloud and footage lives on local drives.
- **Native apps (Flutter/React Native):** these can open raw sockets or USB, so in theory they could use the legacy Wi-Fi API. However, the API is deprecated and probably does not expose picture-profile settings. The effort and the €99/yr iOS fee buy little benefit.
- **Desktop scripts (Python + ffmpeg + whisper.cpp, optionally gphoto2):** these are the only realistic route for (c) post-production automation. They run locally with no hosting cost.

### Gaps
- I could not confirm whether the α7S II is on the new Camera Remote SDK's supported list (likely not, given its age) or which settings the legacy API exposes. In particular, I could not confirm whether Picture Profile parameters such as gamma, color mode or black level can be set remotely. Treat remote preset application as unconfirmed or unlikely.

## Costs: developer fees, hosting, AI-builder subscriptions, free tiers

### Takeaway
The whole stack can run at €0/month: a PWA on a free static host plus local scripts. Paying for one month of an AI builder (about $20–25) is optional. App-store fees only apply if the filmmaker goes native. DaVinci Resolve automation now needs Studio, a one-time $295.

### Cited Findings
- Apple Developer Program: $99/year. — [Apple](https://developer.apple.com/programs/)
- Google Play: $25 one-time registration, with no renewal. — [SplitMetrics](https://splitmetrics.com/blog/google-play-apple-app-store-fees/)
- Lovable: free tier of 5 credits/day (max 30/mo); Pro $25/mo (100 credits). Bolt: free tier of 300K tokens/day (1M/mo); Pro $25/mo (10M tokens). Replit: free Starter; Core $20/mo billed annually, about $25 monthly. — [Creatr 2026 comparison](https://getcreatr.com/ai-app-builder-pricing-comparison-2026), [Lovable guide](https://lovable.dev/guides/bolt-vs-replit-vs-lovable), [Jet Admin, July 2026](https://www.jetadmin.io/blog/replit-pricing-in-2026-plans-credits-and-when-to-consider-jet-admin/)
- Real-world use often costs more than the sticker price: some active Replit builders report spending $50–150/mo once agent usage grows. This is anecdotal. — [Vibe Coding Academy](https://www.vibecodingacademy.ai/blog/lovable-vs-bolt-vs-replit-comparison-2026)
- Sources disagree on the exact Replit Core price ($17, $18 or $20 billed annually), because the price was cut in February 2026. — [Tech Insider](https://tech-insider.org/replit-vs-lovable-vs-bolt-new-2026/)

### Inferences
- Free static hosting such as GitHub Pages, Netlify or Cloudflare Pages is enough for a single-user PWA. I did not verify their 2026 free tiers; see Gaps.
- Sources quote prices in USD. EU buyers usually pay VAT on top.

### Gaps
- I did not fetch 2026 pricing for Glide or for the static hosts' free tiers.

## Free open-source building blocks

### Takeaway
ffmpeg (LUTs and proxies), whisper.cpp (offline transcription) and free checksum offload tools cover nearly all of (c) at no cost. **Major 2026 change:** DaVinci Resolve 21.1 removed external scripting from the free version.

### Cited Findings
- ffmpeg's `lut3d` filter applies .cube LUTs, for example `ffmpeg -i in.mp4 -vf "lut3d=file=my.cube" ...`. Batch shell loops for converting log footage are documented. — [ffmpeg cookbook](https://ffmpeg-cookbook.com/en/articles/lut3d-filter/), [Jeff Geerling 2026](https://www.jeffgeerling.com/blog/2026/apply-lut-color-grade-with-ffmpeg/)
- An older ffmpeg bug report says lut3d output was "slightly wrong" compared with other tools. This matters little for proxies or previews but is worth knowing for final grades. — [FFmpeg trac #2847](https://trac.ffmpeg.org/ticket/2847)
- whisper.cpp is an MIT-licensed C/C++ port of Whisper. It runs fully offline and is optimized for Apple Silicon (Metal/Core ML). — [GitHub ggml-org/whisper.cpp](https://github.com/ggml-org/whisper.cpp), [PromptQuorum review](https://www.promptquorum.com/power-local-llm/whisper-cpp-review)
- **DaVinci Resolve v21.1 removed scripting support from the free version.** Studio ($295) is now needed; the only other option is staying on v21.0. — [Puget Systems, Sep 2026](https://www.pugetsystems.com/blog/2026/09/10/how-davinci-resolve-free-v21-1-scripting-changes-affect-puget-bench/), [Reddit](https://www.reddit.com/r/davinciresolve/comments/1wes5m9/did_davinci_resolve_free_version_211_break/)
- Before 21.1, the free version allowed scripting only from the internal console. External Python scripting was already Studio-only according to forum replies. — [Blackmagic forum](https://forum.blackmagicdesign.com/viewtopic.php?f=12&t=230652)
- The Resolve scripting API is Python (`DaVinciResolveScript`) and can create projects, import media and so on. — [ResolveDevDoc](https://resolvedevdoc.readthedocs.io/en/latest/API_intro.html)
- Free verified-offload options: Stow (free copy and verification software, 2026); WrangleBot (open source, runs as a local server, less intuitive); FastCopy (Windows, free, optional verify pass). Paid tools such as Hedge, ShotPut Pro and Silverstack use xxHash, MD5 or SHA. — [Newsshooter](https://www.newsshooter.com/2026/07/15/stow-free-copy-verification-software/), [Reddit r/Filmmakers](https://www.reddit.com/r/Filmmakers/comments/1i5cc26/i_tried_every_media_transfer_checksum_program/), [DFI Rentals 2026](https://www.dfirentals.com/news/best-on-set-footage-offload-software-2026), [ClipSweeper](https://clipsweeper.com/blog/hedge-vs-silverstack-vs-shotput)

### Inferences
- An rsync or Python copy step with an xxHash or MD5 verify pass can be written by AI in under 100 lines, but the free tools above already do this, so building it isn't necessary.
- For LUT application, ffmpeg is free and good enough for proxies or dailies. For final grades, apply the LUT inside free Resolve by hand rather than scripting it.
- OpenColorIO: not researched here; see Gaps.

### Gaps
- I did not verify OpenColorIO configs or tooling for S-Log2/S-Gamut LUTs (the α7S II's log profile), and I did not verify Sony's official LUT downloads.

## Realistic MVP effort for a non-programmer with AI help

### Takeaway
I found no rigorous source on time-to-MVP. The figures below are estimates. Preset and checklist PWA: about a weekend to a week. Python + ffmpeg/whisper.cpp post-production scripts: about 1–2 weeks of part-time work. Native camera-control app: weeks to months with an uncertain result. Not recommended.

### Cited Findings
- AI builders are marketed as building apps from plain-language prompts with hosting included. Credit or token burn rises with project size. — [Creatr](https://getcreatr.com/ai-app-builder-pricing-comparison-2026)
- Builder pricing comparisons point to a "production gap" between a quick prototype and a robust app. — [StackBinary](https://stackbinary.io/insights/lovable-bolt-replit-prototype-to-production-cost)

### Inferences (estimates, not sourced)
- **PWA (presets, shot lists, checklists):** 1–3 days with an AI builder's free or Pro tier, or with Claude/ChatGPT writing a single-file app. Local storage or export/import as JSON. Cost €0–25.
- **Desktop scripts:** 1–2 weeks part-time. Expect the most time to go on installing Python, ffmpeg and whisper.cpp and handling paths and edge cases. Cost €0, or $295 if Resolve automation is wanted.
- **Native Flutter/React Native with Wi-Fi control:** high risk because the API is deprecated and the α7S II's support is unclear. €99/yr for iOS distribution, though sideloading or Android avoids this.

### Gaps
- I found no measured, sourced data on how long non-developers take to reach an MVP with AI tools.
