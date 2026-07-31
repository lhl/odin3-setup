# GameNative tuning source review

Reviewed **2026-07-30** for the Odin 3 (Snapdragon 8 Elite / Adreno 830). This is an audit trail for the practical process in [`README.md`](../README.md#gamenative-per-game-tuning-ladder), not a universal preset.

## Method and source priority

YouTube's automatic English captions were retrieved for all seven supplied videos and reviewed with timestamps. Automatic captions can mistranscribe component names and settings, so links point back to the videos rather than reproducing the transcripts. Claims were cross-checked against:

1. the source and in-app text for stable [GameNative v1.1.1](https://github.com/utkarshdalal/GameNative/releases/tag/v1.1.1);
2. GameNative's [README](https://github.com/utkarshdalal/GameNative), [roadmap](https://github.com/utkarshdalal/GameNative/blob/master/ROADMAP.md), release notes, and compatibility service;
3. repeatable Odin 3 results recorded in this repository;
4. device-matched community reports; and
5. broad creator/community advice.

This order matters. A working exact-device known config outranks a generic “optimized” preset, and a current stable default outranks a video made against an older interface unless a controlled test proves otherwise.

## Video transcript review

| Video | Transcript-derived contribution | Caveat |
| --- | --- | --- |
| [Stop Guessing! EVERY Game Native Setting Explained](https://www.youtube.com/watch?v=0c_opJrW2L8) — Kei's Retro Gaming, 2026-03-29 | Starts with **Use known config** ([01:36](https://www.youtube.com/watch?v=0c_opJrW2L8&t=96s)); surveys container, Proton, wrapper, Turnip, DX wrapper, renderer, memory, FEX/Box64, frame cap, startup, and affinity options | Useful menu map, but several rules are too absolute. The “one quarter of RAM” memory rule, arbitrary core disabling, newest-component preference, and blanket present-mode choices are not stable-source defaults or universal recommendations. It also discusses two different present controls as though they were one. |
| [GameNative 1.0 is HERE: Massive Gains on the AYN THOR!](https://www.youtube.com/watch?v=-FmJ91zLjNE) — Graves, 2026-06-03 | Reinforces known configs ([02:58](https://www.youtube.com/watch?v=-FmJ91zLjNE&t=178s)) and shows LSFG frame generation plus its artifacts and poor behavior from a low source rate ([04:44](https://www.youtube.com/watch?v=-FmJ91zLjNE&t=284s), [06:47](https://www.youtube.com/watch?v=-FmJ91zLjNE&t=407s)) | Performance examples are on a different device. Generated output FPS is not native/source FPS and does not remove source-frame latency. |
| [GameNative - Running PC Games On ALL Your Handhelds!](https://www.youtube.com/watch?v=CHVHFR6U9dQ) — TechDweeb, 2026-04-05 | Best general workflow of the set: 720p as efficient baseline ([05:31](https://www.youtube.com/watch?v=CHVHFR6U9dQ&t=331s)); physical-controller mapping ([08:31](https://www.youtube.com/watch?v=CHVHFR6U9dQ&t=511s)); **Use known config first** ([09:36](https://www.youtube.com/watch?v=CHVHFR6U9dQ&t=576s)); then isolated runtime, wrapper, driver, and preset tests | Advice to disable CPU 7 is only a “might help” experiment, not a default. GameNative v1.1.1 defaults to all regular-process cores and the upper half for WoW64 processes. |
| [The Truth About GameHub vs GameNative](https://www.youtube.com/watch?v=Obq5WsT80II) — blackheart909, 2026-05-20 | Illustrates how difficult GameNative appears when the known-config path is missed, then demonstrates that path successfully for Portal 2 ([14:29](https://www.youtube.com/watch?v=Obq5WsT80II&t=869s), [15:07](https://www.youtube.com/watch?v=Obq5WsT80II&t=907s)) | Not a controlled frontend benchmark: it first compares a manually configured custom/non-store game, later changes title, and reports implausibly high uncapped FPS without a frame-time analysis. Useful as a UX anecdote, not tuning evidence. |
| [Stop Lagging in Game Native: The Ultimate 60 FPS Optimization](https://www.youtube.com/watch?v=CTT1LUlxyq8) — Kei's Retro Gaming, 2026-02-07 | Short snapshot of an older “latest everything” global preset | Superseded by later releases and contradicted by this Odin's title-specific results. Do not copy its blanket component updates, 4 GB memory values, or one-core-off rule. |
| [What Kind of Android Handheld Is Best for PC Games?](https://www.youtube.com/watch?v=RyS6u-0-SOs) — TechDweeb, 2026-06-21 | Good hardware context: Adreno has the strongest custom-driver path ([01:52](https://www.youtube.com/watch?v=RyS6u-0-SOs&t=112s)); Odin 3 performance is high but Adreno 830 driver maturity still changes quickly ([11:00](https://www.youtube.com/watch?v=RyS6u-0-SOs&t=660s)) | A buying guide, not a per-game configuration guide. Broad performance classes are not compatibility guarantees. |
| [Steam Games on Android with GameNative IN 2 MINUTES!](https://www.youtube.com/watch?v=QqIChmAu2_A) — TechDweeb, 2026-03-25 | Concise install, Steam sign-in, install, quick-menu, exit, and cloud-save walkthrough | Deliberately omits troubleshooting and tuning detail. |

## Reddit guide review

The supplied [“How to optimize your GameNative Settings and Troubleshooting Tips”](https://www.reddit.com/r/GameNative/comments/1ugkl4g/how_to_optimize_your_gamenative_settings_and/) post by `Ol_Captain_Bad_Eyes` is a valuable large-sample **Adreno 750 / Lenovo Y700 Gen 3** report, not an Odin 3 preset.

### Guidance retained

- Start at 1280×720 for efficiency and increase resolution only when the title has headroom.
- Prefer the game's own FPS limiter, then GameNative's quick-menu limiter; treat `DXVK_FRAME_RATE` as a DXVK-specific fallback rather than a universal cap.
- Use the HUD to classify CPU/GPU/RAM pressure and compare the same scene and device power profile.
- Keep all normal-process cores enabled unless a title-specific report or controlled test shows otherwise.
- Match DXVK to DirectX 8/9/10/11 and VKD3D to DirectX 12.
- Move a translation preset toward compatibility when a game boots but later crashes, then move back toward performance only after correctness.
- Use System only as a separately tested graphics fallback; do not swap wrapper and Turnip simultaneously.
- Record game branch, executable, launch arguments, and manually installed redistributables when they are part of the fix.

### Guidance not adopted as a default

- **Disabling Auto-apply known config:** stable v1.1.1 enables it by default, and exact-device known configs have been decisive in this repository. Disable it only for an intentional controlled experiment.
- **Wrapper-v2 as the modern first choice:** a corrective comment notes that **Wrapper** is the current GameNative path and Wrapper-v2 is older. More importantly, v1.1.1 itself defaults Adreno 8 Elite devices to **Wrapper + Turnip Gen8 V30**. The repository process now follows the stable source.
- **A single global “optimized” config:** the post mixes tests from GameNative v0.8 through v1.0 on Adreno 750. It is useful evidence, but not a substitute for an Adreno 830 known config.
- **Extreme/performance presets first for every title:** v1.1.1 defaults FEXCore to Intermediate, and aggressive presets can boot successfully but fail in later scenes. Preserve the known preset and test directionally.
- **Quarter-RAM memory and VRAM values:** the post itself calls the RAM advice guesswork. Stable v1.1.1 defaults Max Device Memory to `0` and Wine video memory to 2048 MB. Change memory controls only for measured RAM exhaustion, one at a time.
- **Manually adding default environment variables:** v1.1.1 already includes `PULSE_LATENCY_MSEC=144`, `VKD3D_SHADER_MODEL=6_0`, and `MESA_SHADER_CACHE_MAX_SIZE=512MB`. The Reddit post also uses inconsistent/incorrect Mesa variable names in places. Do not duplicate or rename them blindly.
- **Refresh rate as a guaranteed FPS cap:** comments on the same post report exceptions, especially windowed/older titles. Confirm the actual cap with frame-time/FPS telemetry.

## Stable v1.1.1 facts that changed the process

- **Modify Default Config affects only future containers**, not installed games.
- **Auto-apply known config is enabled by default.** A known config can also fetch required components.
- For the Adreno 8 Elite family, source defaults use **Bionic, Proton 10 ARM64EC-2, FEXCore 2605 / Intermediate, Wrapper, Turnip Gen8 V30, 1280×720, Vulkan display renderer, and Essential startup**. These are no-report defaults, not a reason to overwrite a working known config.
- GameNative exposes separate **graphics-driver present mode** and **display-renderer present mode** controls. Do not set both from a generic “mailbox is best” rule; v1.1.1 itself defaults them differently.
- The in-app launch tips explicitly recommend **Test Graphics** for no-boot/blank-screen triage, **Diagnostic Run** plus shareable logs, Turnip Gen8 V25/V30 or MrPurple T26 on 8 Elite, lower resolution plus quick-menu FSR, Proton 10 for XAudio, Steam Input for control failures, and SurfaceFlinger as a separately tested renderer.
- `Unpack Files` is labeled specifically for `Application Load Error 3:0000065432`.
- Full Steam Client is a beta compatibility path that reduces performance and slows launch; experimental Bionic Steam asks users to back up saves.
- LSFG-VK requires a Bionic container, an owned/installed copy of Lossless Scaling, and is only known by the app to work on Adreno 6xx and newer. While active, it overrides GameNative's normal limiter and paces the source rate from its multiplier.

## Result

The original tuning ladder had the right safety principles—known config first, export, one change per test, and title-specific records—but underweighted diagnostic classification and over-promoted Wrapper-v2. The revised process adds Test Graphics/Diagnostic Run, a limiter order, bottleneck-directed resolution/FSR testing, separate display-renderer tests, narrow symptom fixes, and an explicit generated-versus-native FPS distinction.
