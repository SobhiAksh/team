# Control interfaces for a custom Sony α7S II (ILCE-7SM2) companion app (status as of Nov 2026)

## Does Sony's Camera Remote SDK (current versions) officially support the ILCE-7SM2?

### Takeaway
No. The current Camera Remote SDK (Ver. 2.02.00, 10 June 2026) does not list the ILCE-7SM2. It covers only newer bodies, from the α7S III / α7 IV / α1 generation on, plus the FX and cinema lines.

### Cited Findings
- Latest SDK is Camera Remote SDK Ver. 2.02.00, dated 2026-Jun-10. Supported models: ILX-LR1, ILCE-1M2, ILCE-1, ILCE-9M3, ILCE-9M2, ILCE-7RM6, ILCE-7RM5, ILCE-7RM4A, ILCE-7RM4, ILCE-7CR, ILCE-7SM3, ILCE-7M5, ILCE-7M4, ILCE-7CM2, ILCE-7C, ILCE-6700, BURANO, ILME-FX6V/FX6T, FX3A, FX3, FX2, FX30, PXW-Z300/Z380/Z200, HXR-NX800, BRC-AM7, ILME-FR7, ZV-E1, ZV-E10M2, DSC-RX1RM3 and DSC-RX0M2. **ILCE-7SM2 is not listed.** — [Sony Camera Remote SDK page](https://support.d-imaging.sony.co.jp/app/sdk/en/index.html)
- The first SDK release (2020) supported only the ILCE-7RM4 and ILCE-9M2 over USB. — [SGP forum, SDK announcement](https://forum.sequencegeneratorpro.com/t/sony-camera-remote-sdk-finally-available/11832)
- Community SDK-based tools (e.g. SonShell, built on SDK v2.00.00) recognise only "the full SDK model list". — [goudvuur/sonyshell](https://github.com/goudvuur/sonyshell)

### Inferences
- The SDK has never supported pre-2019 bodies. Sony has not stated a reason. The likely cause is that the SDK depends on a newer firmware-side "PC Remote" protocol and features that the α7S II (2015) never received. This is inference, not a Sony statement.
- Do not plan the SDK route for this camera.

### Gaps
- I found no Sony statement explaining why older models are excluded.

## Does the legacy Sony Camera Remote API (Wi-Fi JSON-RPC) work on the α7S II, and what can it do?

### Takeaway
The α7S II has only the Wi-Fi remote path that the "Smart Remote Control / Smart Remote Embedded" PlayMemories app provides, which is the endpoint the legacy JSON-RPC Camera Remote API talks to. The α7S-generation bodies support remote movie recording through it. Sony has retired the API itself: the official docs and SDK are no longer published, and only community wrappers and mirrors remain. I could not confirm the exact per-setting read/write list for the α7S II from a primary source.

### Cited Findings
- The Camera Remote API is a JSON-RPC-over-HTTP interface used after a direct Wi-Fi connection to the camera. — [dltHub summary](https://dlthub.com/context/source/sony-camera-remote-api)
- Community wrappers still exist, for example pysony (Python), which ships an example that "connect[s] to camera, and start[s] a video recording". — [bloodevil/sony_camera_api](https://github.com/bloodevil/sony_camera_api). Another example is the .NET implementation with SSDP discovery. — [twenzel/SonyCameraRemoteControl](https://github.com/twenzel/SonyCameraRemoteControl)
- Brian Smith (a Sony Artisan) says remote control of Movie mode via Smart Remote Control is "only supported on cameras a7S and later – not on a7 and a7R". He also calls it a still-photo app on other bodies, so support varies by model. — [Brian Smith: Using Sony Smart Remote Control](https://briansmith.com/sony-smart-remote-embbedded-app/)
- Smart Remote Embedded has its own quality settings, separate from the camera's. — [same source](https://briansmith.com/sony-smart-remote-embbedded-app/)
- A long-running StackOverflow thread on which APIs newer cameras support notes that bodies from the α6300/α7R II era used the old API. Its newer answers point to the Camera Remote SDK list, which excludes the α7S II. — [StackOverflow 49084483](https://stackoverflow.com/questions/49084483/what-apis-do-new-sony-cameras-support-i-e-a9-ilce-9-a7r2-ilce-7rm2-a7m3)
- Reviews of the α7S II note that Smart Remote Control lets you control exposure and other settings from a phone. — [Photography Blog α7S II review](https://www.photographyblog.com/reviews/sony_a7s_ii_review); [Imaging Resource review](https://www.imaging-resource.com/cameras/sony-a7s-ii-review/)
- Sony's older "Remote Camera Control" desktop software is no longer available for download. Sony now points users to Imaging Edge Desktop. — [Sony support 00021957](https://www.sony.com/electronics/support/articles/00021957)

### Inferences
- In practice the API works only if Smart Remote Control (Embedded) is installed and launched on the camera, because the camera needs that app to expose the API. With the PMCA store closed (next section), a camera without it installed cannot get it officially. The α7S II may have shipped with Smart Remote Embedded preinstalled, but I did not verify this. Check this on the specific camera body.
- From general knowledge of the API spec, which I did not verify here: the API typically offers getAvailableApiList, start/stopMovieRec, setShootMode(movie), set/getIsoSpeedRate, set/getShutterSpeed, set/getFNumber, set/getWhiteBalance, set/getExposureCompensation, focus mode on some models, and liveview via startLiveview. Picture Profile is **not** part of the public API. A developer must call getAvailableApiList on the real camera to get the true list.

### Gaps
- I could not retrieve the archived Sony "Camera Remote API beta" device/API compatibility table for ILCE-7SM2. The official download page appears to be gone, and I found no primary 2026 source.
- I have no confirmation whether ISO, shutter and WB writes work while the camera is in movie mode on the α7S II via the API.

## PlayMemories Camera Apps status and the OpenMemories / Sony-PMCA-RE community project

### Takeaway
Sony stopped selling apps and then, on 31 August 2025, ended the download service for all apps (paid and free). Apps already installed on a camera keep running, but nothing new can be installed officially. The community tools Sony-PMCA-RE and OpenMemories install apps over USB without Sony's store. Their app-install path works without modifying the firmware. They are unofficial, reverse-engineered and carry an explicit no-responsibility warning.

### Cited Findings
- Sony discontinued PlayMemories Camera Apps sales and service as of 31 August 2025 (announced 28 February 2023). — [Sony USA support 00289030 (search snippet)](https://www.sony.com/electronics/support/articles/00289030)
- Asia notice: paid-app sales ended 30 September 2023, and the download service for paid and free apps ended 31 August 2025. — [Sony Asia support](https://www.sony-asia.com/electronics/support/articles/00288923)
- Community guides exist for archiving apps before the shutdown and for installing them afterwards. — [Reddit r/SonyAlpha archive thread](https://www.reddit.com/r/SonyAlpha/comments/16pkdvu/how_to_archive_sony_playmemories_camera_apps/); [StackOverflow 76697439](https://stackoverflow.com/questions/76697439/installing-camera-apps-after-sony-shuts-down-the-playmemories-store)
- OpenMemories: Tweak can remove the 30-minute recording limit, unlock the language and PAL/NTSC settings, and enable telnet/ADB for developers. It "can only change settings that are already there and just hidden by Sony" and works by editing the camera's Backup.bin settings file. It is compatible with all PMCA-capable cameras. The repo has been unmaintained since 2018 (last release v0.11, October 2017). — [ma1co/OpenMemories-Tweak](https://github.com/ma1co/OpenMemories-Tweak)
- Its warning: "not an official Sony application… could cause harm to your hardware. If you break your camera, you get to keep both pieces. We won't take any responsibility." Modified settings persist after uninstall, and the effect of a factory reset or firmware update is not fully known. — [same](https://github.com/ma1co/OpenMemories-Tweak)
- A community rewrite, "PMCA-RE RELOADED", was posted in late 2026 as a desktop USB toolkit (Windows/Linux/macOS) for installing apps, working with firmware and changing settings. — [Reddit r/SonyAlpha (search snippet only; page could not be fetched)](https://www.reddit.com/r/SonyAlpha/comments/1xlyq99/i_rewrote_sonypmcare_free_tool_for_sony_camera/)
- Users report failures on some firmware versions, for example OpenMemories Tweak on the α7R II with firmware 4.0. — [DPReview thread](https://www.dpreview.com/forums/threads/open-memories-tweak-on-a7r2-firmware-v4-0.4168039/)

### Inferences
- For a custom app, PMCA-RE's main value would be (a) reinstalling archived Smart Remote Control to bring back the Wi-Fi API, or (b) running a custom on-camera Android app via OpenMemories-Framework. Option (b) could in theory reach internal settings, but it is unsupported research-grade work.
- Risks: warranty and support loss; possible soft-brick or settings corruption; dependence on firmware version; an abandoned upstream; and telnet/ADB opening an unauthenticated root shell over Wi-Fi. Per the brief, these notes do not include firmware-flashing instructions.

### Gaps
- I did not verify the α7S II row in OpenMemories-Framework's Cameras.md or which firmware versions work.
- I could not open the details of the 2026 "RELOADED" project.

## USB/PTP control via libgphoto2

### Takeaway
libgphoto2 lists the "Sony Alpha-A7S II (Control)" with Image Capture, Trigger Capture, Liveview and Configuration. This makes a USB-tethered laptop app feasible. The camera must be in PC Remote USB mode.

### Cited Findings
- gPhoto support table: "Sony Alpha-A7S II (Control) | Image Capture, Trigger Capture, Liveview, Configuration". The original α7S is listed the same way, plus an MTP entry. — [libgphoto2 supported cameras](http://www.gphoto.org/proj/libgphoto2/support.php)
- The commercial app Shutter+ lists for the α7S II: Wi-Fi and USB connection, exposure control, tap to focus, video recording, and transfer of JPEG and video files. It does not support RAW transfer or audio levels. This shows that third-party control over both transports works on this body. — [Shutter+ a7S II page](https://shutter.dev/docs/sony-a7s-ii)

### Inferences
- From general knowledge of libgphoto2's Sony PTP driver, which I did not verify per setting: the typical config entries are ISO, shutter speed, f-number, white balance, exposure compensation, focus mode, drive mode and a "movie" toggle for record start/stop. A developer should run `gphoto2 --list-config` against the actual camera to confirm the list. Picture Profile is generally not exposed as a writable PTP property on bodies of this generation.

### Gaps
- I have no primary-source per-property list for the ILCE-7SM2 in libgphoto2, and no confirmation that movie start/stop works over PTP on this model.

## Imaging Edge Mobile / Creators' App

### Takeaway
Sony's support list marks the ILCE-7SM2 as "Compatible, but with restrictions" for remote shooting in Imaging Edge Mobile. That remote shooting relies on the Smart Remote Control camera app. I found no source confirming Creators' App support for the α7S II.

### Cited Findings
- Imaging Edge Mobile supported-cameras list, smartphone remote shooting (Ctrl with Smartphone): "ILCE-7SM2 — Compatible, but with restrictions". — [Sony IEM supported cameras (search snippet)](https://support.d-imaging.sony.co.jp/support/cscs/l/iemproducts/index.php). The full page could not be parsed for footnotes.
- Imaging Edge Mobile offers image/video transfer, remote shooting and location info. — [Imaging Edge Mobile](https://imagingedge.sony.net/l/ie-mobile.html)
- Users report that Sony changed or removed remote features in Imaging Edge Mobile on some bodies. — [Reddit r/SonyAlpha](https://www.reddit.com/r/SonyAlpha/comments/12fan71/did_sony_just_stealthily_break_imaging_edge_mobile/)

### Inferences
- Creators' App targets newer bodies, so α7S II support is unlikely. This is unverified.

### Gaps
- I do not know the exact restrictions behind "Compatible, but with restrictions" for the α7S II.
- I have no confirmation of Creators' App compatibility for the α7S II.

## Hard limits: what cannot be changed remotely

### Takeaway
None of the official or semi-official remote paths for the α7S II (Wi-Fi API or PTP) is documented to select Picture Profiles (PP1–PP7) or to recall Memory settings M1–M3. Those almost certainly have to be set on the camera body.

### Cited Findings
- Public Camera Remote API wrappers and Sony's descriptions cover exposure, WB, recording and liveview. None that I found mention picture profile. — [pysony](https://github.com/bloodevil/sony_camera_api); [Brian Smith](https://briansmith.com/sony-smart-remote-embbedded-app/)
- Smart Remote Embedded uses its own quality settings, separate from the camera's. — [Brian Smith](https://briansmith.com/sony-smart-remote-embbedded-app/)
- OpenMemories Tweak can edit hidden entries in Backup.bin (about 15,000 entries, most of them undocumented). — [OpenMemories-Tweak](https://github.com/ma1co/OpenMemories-Tweak)

### Inferences
- A realistic "look preset" app for the α7S II could push ISO, shutter, aperture, WB and exposure compensation (and possibly focus mode), and could start/stop recording. The user would still select the Picture Profile, and any M1–M3 memory recall, on the camera. A practical workaround is to tell the user which PP slot to select on the camera.
- The only theoretical route to Picture Profile remotely is an on-camera custom app (OpenMemories-Framework) or editing Backup.bin. That route is unsupported and risky, and I found no evidence that anyone has done it.

### Gaps
- I found no primary source that explicitly states "Picture Profile / MR cannot be set remotely" for the α7S II. This conclusion comes from the absence of the setting in the API and PTP documentation and wrappers I checked. Test it on hardware with getAvailableApiList and `gphoto2 --list-config`.
