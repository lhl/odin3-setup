# AYN Odin 3 setup

Personal setup record and recovery runbook for the AYN Odin 3.

> [!IMPORTANT]
> Checkboxes in this document are a plan, not a claim that a step has been completed. Update the device record and setup log as work is done.
>
> Last source review: **2026-07-30**. Android emulators (including the very new Xbox ports), GameNative, GameHub Lite, ClusterTune, PULSE, and Armada are moving quickly, so read the current release notes before installing or updating anything.

## Proposed setup

- **Android:** primary general-purpose OS.
- **Emulation on Android:** use the official stable AArch64 build of [RetroArch](https://www.retroarch.com/?page=platforms) for classic systems, standalone emulators for newer systems, and [Obtainium](https://github.com/ImranR98/Obtainium) to track trusted upstream APKs.
- **Android game launcher:** use GPL-3.0 **[NeoStation](https://github.com/misobadev/neostation-frontend)** for the unified controller-first library and GameNative frontend-sync integration; retain GameNative and every emulator as independently usable apps.
- **PC games on Android:** start with [GameNative](https://github.com/utkarshdalal/GameNative); keep [GameHub Lite](https://github.com/Producdevity/gamehub-lite) as a game-specific fallback.
- **PC streaming:** on a Windows host, prefer [Artemis](https://github.com/ClassicOldSong/moonlight-android) with [Apollo](https://github.com/ClassicOldSong/Apollo) for automatic virtual-display handling; use official [Moonlight](https://github.com/moonlight-stream/moonlight-android) with [Sunshine](https://github.com/LizardByte/Sunshine) for the conservative cross-platform path.
- **Android power tuning:** the current device configuration uses [PULSE](https://github.com/keiretrogaming/pulse) for frequency/power control. Do not run ClusterTune or manual underclock scripts alongside it; a stock baseline still needs to be recorded for comparison.
- **Linux:** test [Armada](https://github.com/virtudude/armada) from a dedicated microSD card first.
- **Dual boot:** consider an internal Armada/Android split only after both environments have been tested and the storage allocation is clear.

This order matters because Armada's first internal-storage split **factory-resets Android**. If internal dual boot is the goal, do only a minimal Android/GameNative test before the split, then perform the final Android setup after the reset.

## Status

- [ ] Record the device and Android build details.
- [ ] Apply any intended stock Android update before changing the bootloader.
- [ ] Back up Android data and recovery material.
- [x] Install the initial emulator apps: RetroArch, DuckStation, HakuX, ScummVM, Dolphin, and XenDroid.
- [ ] Install only the required RetroArch cores and create the emulation library layout.
- [ ] Configure and test each emulator directly before adding it to the unified library.
- [x] Install NeoStation and configure its GameNative Steam frontend-sync marker folder.
- [x] Install and test GameNative: 80 Days launches, but still needs a handheld controller mapping; OlliOlli remains incompatible in testing.
- [ ] Configure and test one PC-streaming client/host pair, if needed.
- [ ] Record a stock Android performance, temperature, fan, and battery baseline.
- [x] Configure PULSE for a 60 FPS target, Aggressive Park, Efficient 11 W envelope, Smart Fan, and 60 Hz; do not run ClusterTune alongside it.
- [ ] Prepare a host computer with current Android Platform-Tools.
- [ ] Test Armada from a dedicated microSD card.
- [ ] Verify Android and Armada boot switching.
- [ ] Decide whether to remain on SD boot or install Armada internally.
- [ ] If using internal dual boot, complete the Android factory reset and final Android setup.
- [ ] Record installed versions and successful game configurations below.

## Device record

| Item | Value |
| --- | --- |
| Model | AYN Odin 3 |
| SoC | Qualcomm **SM8750** / Snapdragon 8 Elite |
| Android `ro.soc.model` | TODO (expected `CQ8725S`) |
| Variant (RAM/storage) | TODO |
| Android build number | TODO |
| Android security patch | TODO |
| Internal storage available at start | TODO |
| Android allocation after dual boot | TODO / not applicable |
| Armada allocation after dual boot | TODO / not applicable |
| Armada image/version | TODO / not installed |
| Armada ABL backup location | TODO / not created |

Do not put the device serial number, account recovery codes, passwords, or Steam tokens in this repository.

## What to have on hand

- A reliable USB data cable and charger.
- A host computer and microSD card reader.
- Current [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools), including `fastboot`.
- A dedicated **64 GB or larger, A2-rated** microSD card for Armada.
- Optional: a separate microSD card for Android game storage. Flashing Armada erases its card, so do not use the only copy of an Android game library.
- Enough backup space for Android files and both original ABL images.

## Android baseline

Before installing games or modifying the boot path:

1. Charge the Odin 3 and verify the controls, Wi-Fi, Bluetooth, audio, charging, display, microSD slot, and any dock while it is still stock.
2. Record the Android build information in the table above.
3. Apply any stock OTA update that we intend to use **before** flashing the custom ABL. Before taking a later Android OTA with a custom ABL installed, check the current Armada/ROCKNIX guidance first.
4. Back up saves, downloads, screenshots, authenticator/recovery information, and any emulator configuration that cannot be recreated.
5. Install current Android Platform-Tools on the host and confirm:

   ```sh
   adb --version
   fastboot --version
   ```

6. Keep sideloading permission restricted. Enable **Install unknown apps** only for the file manager or browser used for a verified APK, then disable it again.

## Android emulation

**Decision: yes, install RetroArch—but do not try to make it handle every system.** Use RetroArch for classic consoles, handhelds, arcade, and old computers; use well-maintained standalone emulators where their Android interface, performance, or per-game settings are better. There is no single authoritative emulator store. The practical combination is **Google Play for current official listings plus Obtainium for upstream APK releases**. F-Droid is useful for a few FOSS emulators, not as the complete catalog.

> [!IMPORTANT]
> Emulators do not include games, console firmware, encryption keys, or BIOS images. Dump those from hardware and media we own, follow the laws that apply where the device is used, and never commit copyrighted dumps to this repository. Avoid “ROM downloader,” preconfigured emulator, and random APK sites.

### Stores, catalogs, updaters, and launchers

These solve different problems:

| Tool | What it is | Use here |
| --- | --- | --- |
| **Google Play** | Store with Android signing/update continuity and paid apps | Prefer it when the emulator project identifies its listing as official and the listing is current |
| **[Obtainium](https://github.com/ImranR98/Obtainium)** | Updater that follows a project's release page; not an app-review service | Preferred for official GitHub/GitLab/Forgejo/site APKs that are absent or stale on Play |
| **[F-Droid](https://f-droid.org/en/about/)** | FOSS catalog whose default builds are independently built and signed | Good optional source for [RetroArch](https://f-droid.org/packages/com.retroarch/) or [Lemuroid](https://f-droid.org/packages/com.swordfish.lemuroid/), but its emulator selection is limited |
| **[Obtainium Emulation Pack](https://github.com/RJNY/Obtainium-Emulation-Pack)** | Community-maintained import file containing many Obtainium source configurations | Useful as a menu or to add selected entries; do not treat every included fork, mirror, nightly, or experimental app as endorsed |
| **[EmuReady](https://www.emuready.com/listings)** | Community compatibility/settings database | Check title-, device-, emulator-, and version-specific reports; it does not distribute ROMs or replace emulator sources |
| **NeoStation / ES-DE / Daijishō / other launchers** | Library frontends that organize games and launch other apps | Use NeoStation here; add each emulator only after it launches that system correctly on its own |

Recommended source workflow:

1. Install Obtainium from its [official latest release](https://github.com/ImranR98/Obtainium/releases/latest), using `app-release.apk`. Its project publishes package ID `dev.imranr.obtainium` and signing-certificate SHA-256 `B3:53:60:1F:6A:1D:5F:D6:60:3A:E2:F5:0B:E8:0C:F3:01:36:7B:86:B6:AB:8B:1F:66:24:3D:A9:6C:D5:73:62`.
2. Add an emulator's **official project URL** to Obtainium and select stable releases only. Inspect the actual source URL, package ID, release notes, APK asset filter, and architecture before the first install.
3. The standard Odin 3 build is AArch64/ARM64. Do not select x86, 32-bit ARM, VR, dual-screen, benchmark-spoofing, or nightly variants unless there is a specific reason.
4. If using the Emulation Pack, import the [current standard JSON](https://github.com/RJNY/Obtainium-Emulation-Pack/releases/latest), not its dual-screen file, then remove or ignore apps we did not choose. In particular, some entries use third-party mirrors or community forks; install DuckStation from its official Play listing rather than a mirror.
5. Keep each app on one signing/source track. Android normally refuses an update signed with a different certificate, so switching between Play, upstream, and F-Droid builds may require a data export, uninstall, and reinstall. Never bypass a signature mismatch or casually enable Obtainium's package-ID-change option.
6. Obtainium needs **Install unknown apps** permission to apply updates. That persistent privilege is a tradeoff: review every update prompt and its release notes, or revoke the permission between update sessions.

The pack saves source-configuration work; it does not independently audit APK code or make every project safe. Prefer upstream stable releases over rehosted APKs, abandoned builds, and “performance” forks whose changes cannot be reviewed.

### Obtainium Emulation Pack audit

The standard Emulation Pack reviewed on 2026-07-30 contains **58 entries, including 26 emulators**. Importing it is not the same as installing or endorsing all of them; its current default checks only installed apps plus “Track Only” entries. Use it to avoid hand-writing source rules, then make deliberate choices:

| Pack entry or group | Decision for this Odin 3 | Why |
| --- | --- | --- |
| RetroArch AArch64, Dolphin, Flycast, PPSSPP, ScummVM, Azahar | **Use when the matching system is needed** | These point to the official project/site; remain on stable releases |
| DuckStation | **Ignore/remove the pack entry; install from official Google Play** | The pack currently follows a third-party Cloudflare Worker mirror rather than an official DuckStation distribution URL |
| melonDS, melonDS Nightly, MelonDualDS | **Install stable melonDS only** | Nightly adds regression risk; MelonDualDS is for dual-screen hardware, not the Odin 3 |
| Azahar and Citra MMJ | **Azahar first; Citra MMJ only per-game** | Azahar is the current mainline choice; MMJ is an optimization/compatibility fork |
| NetherSX2, NetherSX2-Turnip, Play!, ARMSX2 | **Start with plain NetherSX2; evaluate ARMSX2 as the active FOSS path** | These are separate PS2 tradeoffs, not cumulative upgrades; the Turnip build bundles a modified driver-loading path |
| Eden and Citron Neo | **Eden stable Standard APK first; Citron only per-game** | Both are Switch emulator projects; duplicate installs mean duplicate firmware, keys, caches, and saves to manage |
| Xemu Android/X1 BOX, HakuX, XenDroid | **On-demand experiments only** | X1 BOX and HakuX emulate the original Xbox; XenDroid emulates Xbox 360. None emulates Xbox One |
| Cemu, aPS3e, RPCSX | **Do not baseline-install** | These are early Android Wii U, PS3, and PS4/PS5 projects respectively; RPCSX is not an RPCS3 build |

Also inspect the chosen release asset after every new import. Architecture filters and a saved APK index can select the wrong variant if an upstream project changes asset order. For Eden on this device, choose the stable **Standard APK**, not Legacy or a benchmark/game-spoof package.

### System-by-system emulator set

Install only rows matching games we actually own. More emulators mean more update channels, private save locations, duplicate shader caches, and configuration to back up.

The tables below are the practical target list for this setup—not every machine ever emulated on Android. **Installed** records only that the app is present; versions, BIOS/firmware, controls, storage, and a known game still need to be configured and tested. For RetroArch systems, “core TODO” means RetroArch is installed but that individual core has not yet been recorded as installed.

Status key: **✅ installed**, **🟨 host app installed/core TODO**, **⬜ not installed**, **— no local install recommended**.

#### Nintendo

| System | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| Game & Watch | RetroArch: **GW** | MAME for LCD games needing different artwork/device support | 🟨 RetroArch installed; core TODO |
| NES / Famicom Disk System | RetroArch: **Nestopia UE** | Mesen for an accuracy- or title-specific issue | 🟨 RetroArch installed; core TODO |
| SNES / Super Famicom | RetroArch: **Snes9x Current** | bsnes for maximum accuracy | 🟨 RetroArch installed; core TODO |
| Game Boy / Game Boy Color | RetroArch: **Gambatte** | SameBoy for accuracy | 🟨 RetroArch installed; core TODO |
| Game Boy Advance | RetroArch: **mGBA** | GBA.emu or Pizza Boy A for a standalone UI/link-specific need | 🟨 RetroArch installed; core TODO |
| Pokémon Mini | RetroArch: **PokeMini** | Standalone PokeMini ports only if needed | 🟨 RetroArch installed; core TODO |
| Virtual Boy | RetroArch: **Beetle VB** | Virtual Virtual Boy | 🟨 RetroArch installed; core TODO |
| Nintendo 64 | **[Mupen64Plus FZ](https://play.google.com/store/apps/details?id=org.mupen64plusae.v3.fzurita)** | RetroArch: Mupen64Plus-Next or ParaLLEl | ⬜ Not installed |
| Nintendo DS / DSi | Stable **[melonDS Android](https://github.com/rafaelvcaetano/melonDS-android)** | RetroArch melonDS core; NooDS is developmental | ⬜ Not installed |
| Nintendo 3DS | Stable **[Azahar](https://github.com/azahar-emu/azahar)** Vanilla build | Citra MMJ as a per-game fallback | ⬜ Not installed |
| GameCube | Official stable **[Dolphin](https://dolphin-emu.org/download/)** | Avoid old MMJ/MMJR forks unless one title requires one | ✅ Dolphin installed; config/test TODO |
| Wii | Official stable **Dolphin** | Per-game Wii Remote, Nunchuk, or Classic Controller profiles | ✅ Dolphin installed; config/test TODO |
| Wii U | Experimental **[Cemu Android port](https://github.com/SSimco/Cemu)** | No mature Android alternative | ⬜ Not installed; on-demand only |
| Nintendo Switch | Stable **[Eden](https://eden-emu.dev/downloads/)** Standard APK | [Citron Neo](https://github.com/citron-neo/emulator), then Strato, per game | ⬜ Not installed; on-demand only |

#### Sega

| System | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| SG-1000 | RetroArch: **Genesis Plus GX** | Gearsystem | 🟨 RetroArch installed; core TODO |
| Master System | RetroArch: **Genesis Plus GX** | Gearsystem or MD.emu | 🟨 RetroArch installed; core TODO |
| Game Gear | RetroArch: **Genesis Plus GX** | Gearsystem | 🟨 RetroArch installed; core TODO |
| Genesis / Mega Drive | RetroArch: **Genesis Plus GX** | PicoDrive or MD.emu | 🟨 RetroArch installed; core TODO |
| Sega CD / Mega CD | RetroArch: **Genesis Plus GX** | PicoDrive; match game and BIOS region | 🟨 RetroArch installed; core TODO |
| Sega 32X | RetroArch: **PicoDrive** | No better general Android standalone choice | 🟨 RetroArch installed; core TODO |
| Sega Saturn | RetroArch: **Beetle Saturn** | Paid [Saturn.emu](https://play.google.com/store/apps/details?id=com.explusalpha.SaturnEmu) or [Yaba Sanshiro 2](https://play.google.com/store/apps/details?id=org.devmiyax.yabasanshioro2) | 🟨 RetroArch installed; core TODO |
| Dreamcast | **[Flycast](https://github.com/flyinghead/flycast)** standalone | RetroArch Flycast core or paid Redream | ⬜ Not installed |
| Naomi / Naomi 2 / Atomiswave | **Flycast** standalone | RetroArch Flycast core | ⬜ Not installed |

#### Sony

| System | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| PlayStation 1 | Official Play build of **[DuckStation](https://play.google.com/store/apps/details?id=com.github.stenzek.duckstation)** | RetroArch: SwanStation | ✅ DuckStation installed; BIOS/config/test TODO |
| PlayStation 2 | **[NetherSX2 Patch](https://github.com/Trixarian/NetherSX2-patch)** for established compatibility | [ARMSX2](https://github.com/ARMSX2/ARMSX2) active FOSS path; Play! independent fallback | ⬜ Not installed |
| PSP | **[PPSSPP](https://www.ppsspp.org/download/)** | PPSSPP Gold has identical emulation and funds development | ⬜ Not installed |
| PS Vita | Experimental **[Vita3K current builds](https://github.com/Vita3K/Vita3K-builds/releases/latest)** | Check the [official compatibility tracker](https://vita3k.org/compatibility.html); no peer alternative | ⬜ Not installed; on-demand only |
| PlayStation 3 | Experimental **[aPS3e](https://github.com/aenu1/aps3e)** | Official RPCS3 has no Android build; old RPCS3-Android is archived | ⬜ Not installed; on-demand only |
| PlayStation 4 / 5 | No practical local Android baseline | RPCSX Android UI is a developer experiment; use console remote play | — No local install recommended |

#### Microsoft Xbox

| System | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| Original Xbox | **[HakuX](https://github.com/rfandango/hakuX)** on this Adreno device | [X1 BOX](https://github.com/izzy2lost/xemu) for its setup flow, later fixes, Insignia, or per-game compatibility | ✅ HakuX installed; firmware/config/test TODO |
| Xbox 360 | Experimental **[XenDroid](https://github.com/rfandango/XenDroid)** | [aX360e](https://play.google.com/store/apps/details?id=aenu.ax360e.free); neither is official Xenia | ✅ XenDroid installed; config/test TODO |
| Xbox One / Series X\|S | No practical local Android emulator | [Xbox Remote Play](https://www.xbox.com/consoles/remote-play) or paid [XBXPlay](https://play.google.com/store/apps/details?id=com.grill.xbxplay) | — No local install recommended |

#### Atari, NEC, SNK, arcade, and other consoles

| System | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| Atari 2600 | RetroArch: **Stella** | Stella 2014 only for unusually weak hardware | 🟨 RetroArch installed; core TODO |
| Atari 5200 | RetroArch: **Atari800** | a5200; both need appropriate owner-dumped system files | 🟨 RetroArch installed; core TODO |
| Atari 7800 | RetroArch: **ProSystem** | A7800/MAME for a title-specific issue | 🟨 RetroArch installed; core TODO |
| Atari Lynx | RetroArch: **Handy** | Beetle Lynx | 🟨 RetroArch installed; core TODO |
| Atari Jaguar | RetroArch: **Virtual Jaguar** | No strong Android alternative; expect limited compatibility | 🟨 RetroArch installed; core TODO |
| ColecoVision | RetroArch: **Gearcoleco** | blueMSX | 🟨 RetroArch installed; core TODO |
| Intellivision | RetroArch: **FreeIntv** | Configure keypad mappings per game | 🟨 RetroArch installed; core TODO |
| Panasonic 3DO | RetroArch: **Opera** | No better general-purpose Android option is needed | 🟨 RetroArch installed; core TODO |
| PC Engine / TurboGrafx-16 | RetroArch: **Beetle PCE Fast** | Beetle PCE for greater accuracy | 🟨 RetroArch installed; core TODO |
| PC Engine CD / TurboGrafx-CD | RetroArch: **Beetle PCE Fast** | Beetle PCE; requires an owner-dumped system card | 🟨 RetroArch installed; core TODO |
| PC Engine SuperGrafx | RetroArch: **Beetle SuperGrafx** | Also supports ordinary PCE/PCE-CD content | 🟨 RetroArch installed; core TODO |
| NEC PC-FX | RetroArch: **Beetle PC-FX** | No stronger Android standalone option | 🟨 RetroArch installed; core TODO |
| Neo Geo AES / MVS | RetroArch: **FinalBurn Neo** | MAME4droid Current | 🟨 RetroArch installed; core TODO |
| Neo Geo CD | RetroArch: **NeoCD** | FinalBurn Neo where supported | 🟨 RetroArch installed; core TODO |
| Neo Geo Pocket / Color | RetroArch: **Beetle NeoPop** | RACE | 🟨 RetroArch installed; core TODO |
| WonderSwan / Color | RetroArch: **Beetle Cygne** | WonderDroid Ultra | 🟨 RetroArch installed; core TODO |
| Arcade | RetroArch: **FinalBurn Neo** first | **[MAME4droid Current](https://github.com/seleuco/MAME4droid-Current)** for broader hardware; match every ROM set to its emulator version | 🟨 RetroArch installed; core TODO |

#### Computers, engines, and legacy mobile platforms

| System/platform | Recommended | Other options | Current status |
| --- | --- | --- | --- |
| Amiga | RetroArch: **PUAE** | UAE4ARM on a title-specific basis | 🟨 RetroArch installed; core TODO |
| Amstrad CPC | RetroArch: **Caprice32** | CrocoDS | 🟨 RetroArch installed; core TODO |
| Atari ST | RetroArch: **Hatari** | Hataroid | 🟨 RetroArch installed; core TODO |
| Commodore 64 | RetroArch: **VICE x64sc** | VICE x64 for speed | 🟨 RetroArch installed; core TODO |
| MSX | RetroArch: **blueMSX** | fMSX is easier to configure | 🟨 RetroArch installed; core TODO |
| ZX Spectrum | RetroArch: **Fuse** | Speccy | 🟨 RetroArch installed; core TODO |
| DOS PC | RetroArch: **DOSBox Pure** | Magic DOSBox for a standalone touch-oriented UI | 🟨 RetroArch installed; core TODO |
| Sharp X68000 | RetroArch: **PX68k** | Specialist multi-disk/keyboard setup | 🟨 RetroArch installed; core TODO |
| NEC PC-98 | RetroArch: **Neko Project II Kai** | Specialist multi-disk/keyboard setup | 🟨 RetroArch installed; core TODO |
| Point-and-click adventures | Standalone **[ScummVM](https://www.scummvm.org/downloads/)** | RetroArch ScummVM core | ✅ ScummVM installed; library/config/test TODO |
| PICO-8 | **[PICO-8 Android wrapper](https://github.com/Macs75/pico8-android)** | Winlator; both require a purchased PICO-8 copy | ⬜ Not installed |
| Flash games/animation | Official **[Ruffle Android](https://github.com/ruffle-rs/ruffle-android)** | Closed-source Swiff for its Flashpoint/controller frontend | ⬜ Not installed |
| Java ME | **[J2ME Loader](https://github.com/nikita36078/J2ME-Loader)** | JL-Mod | ⬜ Not installed |
| Symbian / N-Gage | **[EKA2L1](https://github.com/EKA2L1/EKA2L1)** | No comparable mature Android alternative | ⬜ Not installed |

#### Xbox: what the similarly named apps actually emulate

The names are easy to misread:

- **X1 BOX does not mean Xbox One.** It and HakuX emulate the **2001 original Xbox** by packaging/forking xemu for Android.
- **XenDroid emulates Xbox 360.** It is based on Xenia Edge; it is unrelated to the original-Xbox xemu core despite the similar “X” names.
- There is no practical Xbox One or Series emulator for Android. Stream those consoles instead.

Recommended order:

1. For an **original Xbox** title, test HakuX first because it is an X1 BOX fork specifically optimized for Adreno GPUs. Use X1 BOX if its Android-first setup wizard, later base fixes, Insignia support, or a particular title works better. They have separate package IDs and can coexist, but keeping both also duplicates large virtual HDDs and configuration.
2. Supply only this device's legally dumped **MCPX boot ROM, flash/BIOS, Xbox HDD image, and compatible XISO game image**. Upstream xemu does not support CHD or a raw Redump-style ISO directly. Back up the virtual HDD because original-Xbox saves live inside it.
3. These Android apps are community ports. The [official xemu FAQ](https://xemu.app/docs/faq/#q-are-there-any-plans-for-an-android-or-ios-port) still says there is no official mobile port, so a desktop xemu compatibility result is useful evidence, not a guarantee.
4. For **Xbox 360**, test XenDroid from its own GitHub release/Obtainium rule. It requires at least Snapdragon 8 Gen 2 and Adreno 740; the Odin 3 exceeds that floor, but many games can still fail or run slowly. Start with the stock Qualcomm driver and one known-compatible title.
5. If XenDroid fails, aX360e's official Google Play free/donation builds are the other credible current Android path. XenDroid originated from aX360e but was later rebased on Xenia Edge, so they can have different game results.
6. Official [Xenia](https://github.com/xenia-project/xenia), Xenia Canary, and Xenia Edge do not publish normal Android releases. Do not install a random APK merely branded “Xenia Android.” [Xenon](https://github.com/xenon-emu/xenon) is a desktop low-level research emulator where games still do not run, not an Odin alternative.

Keep Wii U, PS3, PS4/PS5, and Switch emulation out of the baseline until a specific title requires one. XenDroid is already installed, but treat it as an Xbox 360 experiment rather than a proven part of the setup. Do not add its alternatives unless a specific game test justifies them; record the exact emulator, driver, and result.

NetherSX2's Patch and Classic variants share the same package identity and cannot normally coexist. On this powerful Adreno device, start with current Patch/4248 as its maintainer recommends; export app data before swapping to Classic/3668 for a game-specific regression. ARMSX2 is now a fast-moving, native-ARM64 PCSX2 fork rather than merely a project to watch, but its young ARM JIT and rapid release cadence still warrant per-game validation and backups. Add NetherSX2-Turnip only if a specific stock-driver problem justifies its modified loader; do not assume bundled Turnip is universally faster. Do not install unrelated APKs merely named “AetherSX2” or “NetherSX2.”

### RetroArch starter configuration

1. Install the **official stable AArch64 APK** from RetroArch's site, optionally tracked through Obtainium. The Google Play version is explicitly marked outdated by RetroArch. F-Droid is also a valid automatic-update route, but choose it at first install and stay on that signing track.
2. In **Online Updater**, update core info files, assets, controller profiles, databases, overlays, shaders, and then the required cores only.
3. A useful starter core set is:
   - **arcade:** FinalBurn Neo; add MAME only for games outside that catalog;
   - **Atari:** Stella, Atari800, ProSystem, and Handy as needed;
   - **NES / SNES:** Nestopia UE / Snes9x Current;
   - **GB/GBC / GBA / Game & Watch / Virtual Boy / Pokémon Mini:** Gambatte / mGBA / GW / Beetle VB / PokeMini;
   - **Master System, Genesis, Sega CD / 32X:** Genesis Plus GX / PicoDrive;
   - **PC Engine / SuperGrafx / PC-FX:** Beetle PCE Fast / Beetle SuperGrafx / Beetle PC-FX;
   - **Neo Geo and related handhelds:** FinalBurn Neo, NeoCD, Beetle NeoPop, and Beetle Cygne;
   - **3DO / ColecoVision / Intellivision:** Opera / Gearcoleco / FreeIntv;
   - **PlayStation:** SwanStation as a RetroArch fallback to standalone DuckStation;
   - **Nintendo 64:** Mupen64Plus-Next as a fallback to standalone Mupen64Plus FZ;
   - **Saturn / Dreamcast:** Beetle Saturn / Flycast; and
   - **old computers:** DOSBox Pure, PUAE, Caprice32, Hatari, VICE x64sc, blueMSX or fMSX, and Fuse only as needed.
4. Set one known `system`/BIOS directory. Verify owner-dumped BIOS filenames and hashes against the emulator/core documentation rather than downloading a “BIOS pack.” Arcade BIOS files often belong with the matching ROM set instead.
5. Map the built-in Odin controls once, then define a hotkey-enable button plus menu, quit, fast-forward, save, and load combinations that cannot fire during normal play. Do not bind an unmodified face/shoulder button to destructive save/load actions.
6. Enable saving configuration on exit only after understanding the hierarchy. Use **core**, **content-directory**, or **game overrides** for exceptions rather than continually changing global settings.
7. Prefer native in-game saves for long-term progress. Save states can break after a core update and generally are not portable between emulators; back up both before major updates.
8. Test one game per core directly in RetroArch before importing the library into a frontend.

[Lemuroid](https://github.com/Swordfish90/Lemuroid) is a much simpler Libretro-based alternative. Use it **instead of** RetroArch if automatic setup matters more than granular cores, shaders, overrides, and frontend integration; installing both adds duplicate scans and saves without much benefit.

### Library, launcher, and backup layout

Use the microSD card as **portable storage** for ROM/disc images so it remains readable on a computer. Have Android format/validate the card, and confirm its filesystem supports files larger than 4 GiB before copying DVD-era images. This Android library card remains separate from the dedicated Armada boot card.

#### Android launcher comparison

**Decision: use [NeoStation](https://github.com/misobadev/neostation-frontend) first.** It is the best service-independent, open-source match for this mostly GameNative library: GPL-3.0, controller navigable, organized by systems, able to record favorites/play time/last played, and preconfigured to launch GameNative Steam, Epic, GOG, Amazon, and custom-game marker files. It is still beta, so keep the underlying apps usable outside the launcher and back up its database/configuration before major updates.

| Launcher | Android app source/license | GameNative path | Recents and organization | Decision here |
| --- | --- | --- | --- | --- |
| **[Argosy](https://github.com/rommapp/argosy-launcher)** | GPL-3.0 source | Deep GameNative and Steam integration | Recent, most played, favorites, collections, platforms | **Do not use without RomM.** Current v2.4.1 onboarding requires a RomM server login despite its README describing local-only use; source has no skip path |
| **[Cannoli](https://github.com/CannoliHQ/cannoli)** | GPL-3.0 source | No individual GameNative-game integration | Deliberately minimal console/game lists; no game switcher, play tracker, scraper, or themes | Excellent retro-only MinUI-style option, but wrong for this PC-heavy library |
| **[Cocoon](https://github.com/inssekt/CocoonFE)** | App source/license not published in its public configuration repository | GameNative player definitions; some marker setup is manual | Favorites/recent smart folders, play time, manually ordered pages/grid | Attractive controller-first beta, but not a verifiable FOSS choice |
| **[Console Launcher](https://github.com/likeich/console-launcher)** | App source/license not published; repository contains themes/configuration | Bundled GameNative launch definitions | Unified apps, games, platforms, and folders with extensive customization | Capable console shell, but source-unavailable and its 2.x line is beta |
| **[Daijishō](https://github.com/TapiocaFox/Daijishou)** | App is explicitly closed-source; MIT covers the public assets repository | Community GameNative player definitions; marker extensions may need adjustment | Platforms, favorites, genres, activity widgets, and continue-play | Mature and free, but less PC-centric and not open source |
| **[iiSU](https://github.com/iisu-network/iiSU)** | App source/license not published | GameNative app shortcut; no mature documented per-game integration | Home shortcuts, collections, Android apps, and ES-DE metadata import | Visually polished but still alpha; do not baseline it |
| **[NeoStation](https://github.com/misobadev/neostation-frontend)** | **GPL-3.0 source** | **Native definitions for GameNative's `.steam`, `.epic`, `.gog`, `.amazon`, and `.pcgame` exports** | Favorites, last played, play time, systems, and collections | **Chosen.** Neutral and FOSS, with the required direct-launch path; accept beta churn |
| **[Pegasus](https://github.com/mmatyas/pegasus-frontend)** | GPL-3.0 source | Possible with custom metadata and Android intent commands | Favorites/play statistics/last played are available to themes | Mature and flexible, but the most manual configuration and no integrated scraper |
| **[RetroHrai](https://github.com/retrohrai/Releases)** | Explicitly proprietary | GameNative/GameHub/Winlator shortcuts | Collections, favorites, recent, custom platforms, scraping | Promising public beta, but not open source |
| **[ES-DE](https://es-de.org/)** | Android release is paid and partially closed-source; desktop releases are GPL | Excellent built-in support for all current GameNative marker types | Automatic Favorites and Last Played, custom collections, sorting, mature themes | Most polished fallback if Android source openness stops being a requirement |

Paid [Beacon](https://play.google.com/store/apps/details?id=com.radikal.gamelauncher) remains a simpler closed-source alternative, not part of the open-source shortlist. A frontend normally updates its last-played history only for games launched through that frontend; launching directly inside GameNative or an emulator may not update NeoStation's history.

#### NeoStation and GameNative setup

NeoStation's configured **ROM folder is the library root**, not GameNative's real Steam installation directory and not the `steam` marker directory itself. The working layout is:

```text
/storage/emulated/0/ROMs/       # select this directory in NeoStation
├── steam/
│   ├── Example Game.steam      # tiny text file containing only its numeric Steam App ID
│   └── Another Game.steam
├── epic/                       # *.epic markers
├── gog/                        # *.gog markers
├── amazon/                     # *.amazon markers
├── windows/                    # *.pcgame markers for GameNative custom games
├── psx/
└── other-system-folders/
```

Do **not** point NeoStation at `Android/data/app.gamenative/.../Steam/steamapps/common` or another directory containing the actual PC game installations.

1. Create a lowercase `steam` directory inside the same stable ROM-library root used for the console subfolders. Create `epic`, `gog`, `amazon`, and `windows` only when those sources are needed.
2. In **GameNative → Settings → Interface → Frontend Sync**, map each source to its matching shared directory:
   - Steam → `ROMs/steam`
   - Epic Games → `ROMs/epic`
   - GOG → `ROMs/gog`
   - Amazon → `ROMs/amazon`
   - Custom Game → `ROMs/windows`
3. Confirm the Frontend Sync dialog and run **Resync all** if offered. GameNative creates one text marker per installed game; for example, `Example Game.steam` contains only that game's numeric app ID. These files are launch records, not game data.
4. In NeoStation's directory settings, remove any directly selected `steam` directory and select its parent `ROMs` root. NeoStation requires the root to contain recognized system subdirectories such as lowercase `steam`, `psx`, and `gamecube`.
5. Grant NeoStation the Android file access it requests, update its system definitions, and rescan. The Steam system should appear when at least one valid `.steam` marker exists.
6. Confirm the Steam system's player is **Standalone GameNative (Steam)**. Use the matching GameNative player for each other store. NeoStation reads the ID from the marker and sends GameNative's `app.gamenative.LAUNCH_GAME` intent with the matching game source.
7. Launch one installed game from NeoStation, return cleanly, and verify that last-played/play-time data updates. The marker integration was confirmed working on this Odin; individual game compatibility still belongs to GameNative.
8. If a source does not appear, verify that the marker has the exact extension rather than `.txt`, contains a non-empty ID, sits in the correctly named child directory, and was created before the latest NeoStation rescan.

#### General library and backup rules

1. Use one folder per system and consistent filenames. Point every standalone emulator at those shared **game** folders, but let each app manage its own database/cache.
2. Configure and launch a title inside its emulator first. Only then set NeoStation's player/emulator association and test direct launching and clean return to the launcher.
3. Keep BIOS/firmware/keys out of cloud services unless the service and applicable rights are understood. They are not configuration files to publish.
4. Locate and export each emulator's **in-game saves, memory cards, configuration, and keys**. A launcher does not centralize these, and Android may delete app-private data on uninstall.
5. Back up saves separately from the replaceable ROM library. Do not assume cloud sync is conflict-safe while Android and Armada can both modify a save.
6. Back up NeoStation's own configuration/database and scraped media separately. They can be rebuilt, but they contain favorites, player choices, metadata edits, and launch history.

For supported emulators on this Snapdragon/Adreno device, a per-app Mesa **Turnip** driver can sometimes fix a specific rendering issue. Start with the stock Qualcomm driver, add a custom driver only inside the affected emulator, record its exact version per game, and keep a stock-driver profile for recovery. Never flash an emulator Turnip package as a global Android GPU driver. The commonly referenced builds at [AdrenoToolsDrivers](https://github.com/K11MCH1/AdrenoToolsDrivers/releases/) are third-party and a newer build is not automatically better for the Odin 3's GPU. At this review, that project's A8xx support was explicitly “very beta and hacky,” was reported not to work correctly with Switch emulators, and could leave other apps crashing until a reboot; treat the Odin 3's Adreno 830 stock driver as the default.

### Emulation test log

| System/game | Emulator/core + version | Game format | BIOS/firmware version | Renderer/driver | Resolution/settings | Result |
| --- | --- | --- | --- | --- | --- | --- |
| TODO | TODO | TODO | TODO / not required | Stock / TODO | Defaults | TODO |

## PC game and desktop streaming

**Decision: neither Android client is inherently much faster for ordinary gamepad streaming.** Artemis is a feature-rich Moonlight Android fork, so at the same host, codec, resolution, frame rate, and bitrate the underlying network/decode path is broadly similar. Host encoding, a wired host connection, Wi-Fi conditions, and the Odin's decoder normally matter more. Artemis becomes meaningfully better when its additional input, external-display, or Apollo integration features are needed.

### Android client choice

| | Official Moonlight | Artemis |
| --- | --- | --- |
| Basic low-latency game streaming | Recommended conservative baseline | Comparable core streaming behavior |
| Host compatibility | Primary client for Sunshine; broad platform ecosystem | Currently works with Sunshine and Apollo; future fork divergence remains possible |
| Resolution and bitrate controls | Standard presets and controls | Adds arbitrary resolutions and bitrates |
| Touch, mouse, and desktop use | Basic modes | Multiple mouse/touchpad modes, local cursor, trackpad gestures, keyboard helpers, and clipboard sync with Apollo |
| Built-in virtual controls | Standard Moonlight controls | Custom/importable controls and additional gamepad options |
| Dock, monitor, or AR-glasses use | Standard Android display behavior | Dedicated external-display mode with resolution/refresh detection, scaling, rotation, pan/zoom, and SBS 3D options |
| Apollo-specific integration | Basic compatible streaming | Virtual-display controls, server commands, clipboard sync, OTP/deep-link pairing, and input-only workflows |
| Best role here | Simple controller-first streaming | Feature-rich Odin, docked-display, touch, and desktop use |

Both clients have different Android package IDs and can coexist. For a stock Odin 3, use Artemis's **non-root APK**. Pair both with the same host and A/B test a repeatable scene before attributing a frame-pacing problem to either client.

### Host choice

| | Sunshine | Apollo |
| --- | --- | --- |
| Project role | Mainstream upstream GameStream-compatible host | Sunshine fork designed to pair closely with Artemis |
| Platforms | Strong Windows, Linux, macOS, and FreeBSD path | Cross-platform base, but its integrated virtual display is currently Windows-only |
| Ordinary Moonlight-compatible streaming | Yes | Yes |
| Headless/native client resolution | Requires a physical/dummy display or a separately managed virtual-display solution | Built-in SudoVDA display automatically matches the client's requested resolution and refresh rate |
| Per-client display behavior | Depends on the external display solution | Fixed virtual-display identity per client lets Windows remember that client's layout/settings |
| Additional integration | Standard Sunshine protocol and client management | Granular client permissions, connection/disconnection commands, clipboard sync, input-only mode, and Artemis controls |
| Best role here | Stable, broad, cross-platform baseline | Convenient Windows/Odin or headless-host setup |

Recommended pairings:

1. **Fresh Windows host for the Odin 3:** Artemis + Apollo. Apollo can create a temporary 1920×1080/120 Hz display matching the built-in screen and remove it when the session ends.
2. **Existing reliable Sunshine host:** do not migrate for performance alone. Try Artemis against Sunshine if its Android-side features are useful; Apollo-only integration will remain unavailable.
3. **Conservative or multi-platform setup:** Moonlight + Sunshine.
4. **Linux host:** use Sunshine; Apollo's primary virtual-display advantage does not yet apply there.
5. **Mixed pairs:** Artemis currently streams from Sunshine, and Moonlight can perform ordinary streaming from Apollo, but only the matched Artemis/Apollo pair exposes all fork-specific controls.

Install hosts and clients only from their upstream projects, keep them current, and read security advisories before updating—these applications provide screen capture, remote input, and network services. Do not expose a host web interface directly to the public Internet; prefer WireGuard/Tailscale or another authenticated VPN for remote access. On Windows, remove or disable competing virtual-display tools before enabling Apollo's SudoVDA path, as Apollo explicitly warns that overlapping display solutions cause conflicts.

For the initial Odin test, wire the host by Ethernet, request 1920×1080 at 120 FPS, and start with HEVC or AV1 only when the host has matching hardware encoding. Compare the same codec, bitrate, game scene, host display, and Odin performance mode. A higher reported FPS is not useful if game speed, frame delivery, latency, or display refresh does not match.

### Streaming test log

| Date | Client/version | Host/version | Host GPU/encoder | Resolution/FPS/bitrate | Network | Decode/latency/frame pacing | Result |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TODO | Artemis or Moonlight / TODO | Apollo or Sunshine / TODO | TODO | 1920×1080 / 120 / TODO | Host Ethernet; Odin TODO | TODO | TODO |

## Android power and thermal tuning

**Decision: start stock, then prefer ClusterTune for a conservative underclock.** The Odin 3 is already fast, so tuning is useful only when measurements show that a game can keep its target frame rate with lower CPU caps. PULSE offers much more automation and control, but also has more overlapping settings to validate.

> [!CAUTION]
> “No root required” does not mean these are ordinary low-privilege apps. On supported AYN firmware, ClusterTune and PULSE use the built-in privileged `PServer` service to run commands as root and write protected kernel `sysfs` controls. Install only verified upstream APKs and never run two tuning services at the same time.

### What underclocking changes

- A CPU underclock lowers the maximum frequency available to one or more CPU clusters.
- It is **not undervolting**, does not replace Android's adaptive governor, and does not directly lower the GPU clock.
- It can reduce power, heat, and fan noise when a workload does not need the removed CPU headroom.
- It can reduce frame rate or worsen frame pacing in CPU-bound games, emulators, shader compilation, and background tasks.
- A lower instantaneous wattage does not automatically mean longer battery life if the same job takes much longer. Measure the full workload.

Keep Android's stock performance and fan controls as the baseline. Generic kernel-manager apps normally require Magisk/root and compatible kernel interfaces; they add no benefit for this stock, PServer-based plan.

### Tool choice

| Option | Controls | Privilege path | Role here |
| --- | --- | --- | --- |
| **Stock Odin controls** | Vendor performance and fan modes | System | Baseline and recovery state |
| **ClusterTune** | Per-cluster CPU maximums, saved profiles, quick tile, optional reapply on boot | PServer; `su` fallback on rooted devices | Recommended simple underclock |
| **PULSE** | CPU/GPU caps, AutoTDP, fan, display, RGB, per-app automation, and OSD | PServer | Advanced alternative after baseline testing |
| **Manual Odin 3 scripts** | Fixed CPU maximums | Android's **Run script as root** | Transparent fallback/troubleshooting, not concurrent with an app |

[O2P Tweaks](https://github.com/FeralAI/o2ptweaks.app) is mainly an Odin 2 Portal audio/UI/root utility, not the preferred Odin 3 underclocking tool.

### ClusterTune setup

ClusterTune is GPL-2.0, requires Android 12+, and is explicitly hardware-tested by its project on the Odin 3. It only caps CPU frequency; its project states that it does not undervolt.

1. Capture a stock baseline before installing it:
   - one repeatable game/save/scene;
   - fixed resolution, refresh rate, brightness, frame cap, fan mode, and Android performance mode;
   - a 20–30 minute run from a similar battery level;
   - average/low frame rate if available, maximum temperature, average power, fan behavior, and battery change.
2. Download the APK and matching `.sha256` file from the [latest ClusterTune release](https://github.com/AurelioB/ClusterTune/releases/latest), then verify them on the host:

   ```sh
   # Linux: calculate the hash, then compare it with the .sha256 file
   sha256sum ClusterTune-*.apk

   # macOS: calculate the hash, then compare it with the .sha256 file
   shasum -a 256 ClusterTune-*.apk

   # Windows PowerShell: calculate the hash, then compare it with the .sha256 file
   Get-FileHash .\ClusterTune-*.apk -Algorithm SHA256
   ```

3. Install the APK, revoke the temporary sideload permission, and confirm that it detects the Odin 3's CPU policies. Android may identify this SoC as `CQ8725S` even though the Linux/Armada device table calls it SM8750.
4. Leave **reapply on boot** and any automatic app/profile switching disabled for initial testing.
5. Record the original maximum shown for each cluster.
6. Apply only the bundled **Small Underclock** first. Repeat the identical baseline workload and compare frame pacing—not just an FPS counter.
7. If Small is stable but saves too little power, test Medium as a separate run. Do not jump directly to Large or change the vendor performance/fan mode in the same test.
8. Enable boot or per-app automation only after several clean launches, sleep/wake cycles, and reboots.
9. If a profile causes instability or severe slowdown, stop the workload and reboot. With boot reapply disabled, the kernel/vendor defaults should return; verify the displayed maximums before continuing.

The current bundled Odin 3 (`CQ8725S`) profiles were:

| Profile | CPU policy 0 (6 cores) | CPU policy 6 (2 cores) |
| --- | ---: | ---: |
| Small | 2.7456 GHz | 3.0720 GHz |
| Medium | 2.2272 GHz | 2.2464 GHz |
| Large | 1.7856 GHz | 1.9584 GHz |

These are project presets, not guarantees or universal recommendations. Re-check the current bundled profile and release notes because values can change. At this source review, the latest stable ClusterTune release was **v0.3.1**.

### PULSE alternative

PULSE is a GPL-2.0 fork of ClusterTune with explicit Odin 3 support. It adds CPU and GPU caps, a feedback-based AutoTDP controller, fan control, power tiers, display controls, RGB controls, per-app settings, and an in-game telemetry overlay. Its maintainer also discloses substantial AI assistance in development.

Use it instead of—not alongside—ClusterTune when those additional controls are worth the added complexity:

1. Reset/reboot out of any ClusterTune or manual-script profile, disable its boot automation, and uninstall or disable it before PULSE testing.
2. Download and verify the APK from the [latest PULSE release](https://github.com/keiretrogaming/pulse/releases/latest).
3. Start with the HUD/telemetry only and confirm its readings against the stock baseline.
4. Grant **Usage Access** only if using per-app/AutoTDP features, and **Display over other apps** only if using the OSD.
5. Keep the stock fan controller initially. Then enable one PULSE function at a time—for example, an Efficient AutoTDP profile with a frame target the game already reaches.
6. Do not simultaneously change CPU caps, GPU caps/floors, fan curve, render scale, refresh rate, and frame target; an all-at-once result cannot be diagnosed or safely compared.
7. Be careful with CPU/GPU **floors**: raising minimum frequencies can increase idle/load power even when maximums are reduced.
8. Treat PULSE's watt figures as software power envelopes/ceilings, not a hardware TDP setting; its own documentation notes that Snapdragon exposes no programmable wattage cap.

At this source review, the latest PULSE release was **v1.19.6**. It is newer and broader than ClusterTune, not inherently safer or more efficient for every game.

#### Current PULSE configuration

| Recorded date | PULSE version | FPS target | Core parking | Power tier/envelope | Fan control | Refresh rate | Status |
| --- | --- | ---: | --- | --- | --- | ---: | --- |
| 2026-07-30 | TODO | 60 FPS | Aggressive Park | Efficient / 11 W | Smart Fan | 60 Hz | Active current configuration |

The 11 W value is PULSE's software control target/envelope rather than a programmable hardware TDP. Existing per-game power observations do not yet record whether this profile was active; confirm that field before treating them as comparable results.

### Manual-script fallback

The [original Odin 3 CPU underclock scripts](https://github.com/TheOldTaylor/Odin3-CPU-Underclock) use **Odin settings → Run script as root** and inspired ClusterTune. They are useful for inspecting the simple `sysfs` changes or recovering a minimal workflow, but an app is easier to audit operationally because it displays the active caps. The scripts normally reset on a power cycle. Never apply them while ClusterTune or PULSE automation is active.

### Tuning test log

| Date | Game/workload | Tool/profile | FPS/low | Max temperature | Average power/battery change | Fan/noise | Result |
| --- | --- | --- | ---: | ---: | ---: | --- | --- |
| TODO | TODO | Stock baseline | TODO | TODO | TODO | TODO | TODO |
| 2026-07-30 | Current mixed workloads | PULSE: Efficient / 11 W; Aggressive Park; Smart Fan; 60 Hz | Target 60 / lows TODO | TODO | TODO | Smart Fan / TODO | Active; controlled baseline still pending |

Stop a test if the device shows crashes, display corruption, repeated driver resets, unexpected shutdowns, charging faults, or controls that cannot be restored. Return to stock settings before an Android OTA, before troubleshooting an unrelated issue, and before comparing Armada behavior.

## PC gaming on Android

These tools run Windows PC games locally through Wine, x86-to-ARM translation, and graphics translation. They are not native Android ports and do not guarantee that DRM, anti-cheat, launchers, codecs, or every game will work.

### GameNative or GameHub Lite?

**Decision: use GameNative first.** It has the cleaner provenance and the more complete documented library experience. Use GameHub Lite only when a particular title works materially better there.

| | GameNative | GameHub Lite |
| --- | --- | --- |
| Project form | GPL-3.0 Android project, with several clearly disclosed proprietary runtime shims | Community Smali patch set and rebuilt APK based on proprietary GameHub |
| Library support | Steam, Epic, GOG, and Amazon | GameHub PC emulator/local import; upstream GameHub integrates Steam and Epic |
| Convenience | Cloud saves, automatic community configs, controller/touch support, Steam DLC/workshop/branch support | Accountless GameHub shell, offline support, reduced app and permission set claimed by the project |
| Transparency caveat | Most application code is available, but the release is not completely open source; store sessions are held locally | The patches are inspectable, but the full underlying application source is not available and the project has no conventional open-source license |
| Privacy caveat | Mandatory anonymous compatibility events include game/config/performance data; additional usage analytics can be disabled | Project claims to remove upstream telemetry, but its community rebuild and API path still require trust |
| Best role here | Default Android PC-game frontend | Compatibility/performance fallback for individual games |

Both applications may handle valuable store sessions. “Accountless” in GameHub Lite means no separate GameHub account is required; connecting a store library can still require that store's authentication. Keep Steam Guard and equivalent account protections enabled, use only upstream release links, and revoke sessions immediately if login behavior looks wrong.

### GameNative known configs and EmuReady

GameNative already has its own automatic community-configuration system, but it is **not the same database as EmuReady**. The current client calls GameNative's `api.gamenative.app/api/best-config` service with the game name, store, GPU renderer, and Android build flavor. It can return an exact-GPU, GPU-family, fallback, or no match. No EmuReady service is referenced by the current GameNative client, so treat the two recommendation sources as independent unless their projects explicitly announce backend data sharing.

| Path | What it does | Role here |
| --- | --- | --- |
| GameNative **Auto-apply known config** | Enabled by default; applies a validated GameNative recommendation when the first container is created for a Steam, GOG, Epic, or Amazon game | **Primary path** |
| Game options → **Use known config** | Re-fetches and applies GameNative's current recommendation to an existing game; it may also install required runtime/driver components | Retry after an update or when the original container predates a useful report |
| [GameNative compatibility](https://gamenative.app/compatibility/) | Shows GameNative/Winlator reports and device/GPU results | Check before downloading a large game |
| [EmuReady handheld listings](https://www.emuready.com/listings) filtered to Windows + GameNative | Independent community reports with GameNative-specific driver, wrapper, runtime, resolution, and input fields | Cross-check and troubleshooting source |
| EmuReady Beta **Launch Tool** | Sends GameNative an external launch intent with a report-derived temporary container override; GameNative asks afterward whether to save or discard it | Optional experimental A/B path |
| GameNative **Export config** → EmuReady report import | EmuReady can read a GameNative JSON export to prefill a new community report | Contribute a verified result; this direction does not update GameNative automatically |

The free stable EmuReady app currently states that it does **not** include the early-access Setup Wizard or Launch Tool. Where an EmuReady interface offers a GameNative JSON download, it can instead be applied through that game's **Import config** option. Prefer a report from the same GameNative version and Adreno 830/Odin 3; do not translate a GameHub or Winlator recipe field-for-field because component names, supported versions, and container layouts differ.

The Launch Tool integration is useful for a temporary comparison rather than a default setup. It is explicitly an early-access feature, and the current GameNative external-intent parser accepts only a subset of all modern container fields. Test the report-derived configuration without changing anything else, then choose **Save** only after a clean launch and representative gameplay; otherwise choose **Discard** to restore the original container. Export the working GameNative config before replacing it with any imported recommendation.

### GameNative setup

1. Look up the first test game in the [GameNative compatibility database](https://gamenative.app/compatibility/) and optionally the [EmuReady handheld listings](https://www.emuready.com/listings). Start with a small title reported to work on comparable Adreno hardware.
2. Download the **standard** APK from the [latest GameNative release](https://github.com/utkarshdalal/GameNative/releases/latest). Do not use the `legacy-xr` build; that release is intended for Quest headsets.
3. Verify the APK's SHA-256 digest against the digest published with the GitHub release asset/API:

   ```sh
   # Linux
   sha256sum gamenative-*.apk

   # macOS
   shasum -a 256 gamenative-*.apk

   # Windows PowerShell
   Get-FileHash .\gamenative-*.apk -Algorithm SHA256
   ```

4. Copy the APK to the Odin 3, install it, and remove the temporary sideload permission afterward.
5. Sign in only through the expected store flow and complete two-factor authentication. GameNative's policy says credentials and session data remain on-device and authentication goes directly to each store, but this is still a third-party client that must be trusted.
6. Under **Settings → Emulation**, leave **Auto-apply known config** enabled for the initial test. Install the first game to internal storage and note whether GameNative reports an exact-GPU, GPU-family, or fallback match.
7. Keep that selected configuration unchanged for the first run and test:
   - launch and clean exit;
   - Odin controls, analog triggers, and both sticks;
   - audio and frame pacing;
   - sleep/resume behavior;
   - cloud-save upload and download, if the game supports it.
8. If it fails, export the current container before using **Use known config**, importing an EmuReady JSON, or manually changing settings. Change one path or compatibility setting at a time and record the result below.
9. Review **Settings → Info → Usage Analytics**. This disables optional analytics, not the mandatory anonymous compatibility events described in the project's README and [privacy policy](https://github.com/utkarshdalal/GameNative/blob/master/PrivacyPolicy/README.md).

At the time of this review, the latest stable release was **v1.1.1**. Always prefer the current stable release and read its notes; do not pin this setup permanently to that version.

### GameNative per-game tuning ladder

Treat a working exact-device or known configuration as the baseline, not as a collection of independently optimal components. Export it before tuning, change one field at a time, and keep the first configuration that is both correct and efficient. A newer component is a candidate—not an automatic upgrade—and a launch-only check is insufficient: test audio, controls, frame pacing, a demanding area, clean exit, and power draw.

1. **Establish the functional baseline.** Start unchanged with an exact Adreno 830/Odin 3 known config when available; otherwise use the closest credible same-GPU report. Do not replace several components before confirming what already works.
2. **Update the CPU translator first when performance is poor.** Try the latest stable FEXCore while preserving its known-good preset and every other setting. This was the decisive improvement for Geometry Wars 3 and part of the efficient Hyper Light Drifter result. Keep the older version if the update regresses correctness. FEXCore and Box/Box64 are alternative translation paths for the relevant executable architecture, not upgrades to stack together; compare them only after preserving a working baseline.
3. **Tune graphics one layer at a time.** On Adreno, test Wrapper-v2 with the latest stable Turnip compatible with Adreno 830. If it crashes or renders incorrectly, retain that Turnip and try Wrapper; if the problem remains, restore the known config and test the System Vulkan driver. Do not change the wrapper and Vulkan driver in the same run. “Latest Turnip” is not universally best—Geometry Wars 3 stopped working when its working graphics path was changed, while Hyper Light Drifter benefited from Turnip Gen8 V30.
4. **Choose Proton by title, not globally.** Proton 10 ARM64EC is a sensible no-report starting point and the current GameNative path that automatically extracts bundled XAudio/XACT/X3DAudio DLLs; use it first for missing audio. That extraction requires the game's DirectX redistributable files and does not make Proton 10 universally fastest or most compatible. Test Proton 11 for a title-specific improvement, but retain the known version when changing Proton breaks the game.
5. **Optimize only after correctness.** Compare resolution, full-screen/windowed behavior, frame cap, translation preset, startup-service level, and optional Steam mode separately. Use the performance HUD to distinguish a CPU/translator bottleneck from GPU load, and compare wattage only under the same PULSE profile, display brightness, refresh rate, game scene, and frame cap.
6. **Record the final exception.** Keep exact Proton, FEXCore or Box/Box64, wrapper, Turnip/System driver, resolution, and any environment-variable changes for every game that differs from its known/default setup.

The practical no-report target is therefore **Proton 10 ARM64EC + current stable FEXCore + Wrapper-v2 + a current Adreno 830-compatible Turnip**, but it is only a starting hypothesis. The working known config always outranks that target, and fallbacks should be tested independently rather than applied as a bundle.

### GameHub Lite fallback

GameHub Lite is a modified, community-signed build of GameHub rather than a fully source-built application. Treat it as a fallback, not as a second default launcher.

1. First check the title and suggested configuration in [EmuReady's PC compatibility listings](https://www.emuready.com/pc-listings).
2. Download only from the [GameHub Lite GitHub releases](https://github.com/Producdevity/gamehub-lite/releases/latest) and verify the release asset digest.
3. Use the plain `GameHub-Lite-vX.Y.Z.apk` build initially. The `antutu`, `ludashi`, and `pubg` variants spoof other package names to trigger vendor performance policies; that can change power, heat, and scheduling behavior and is unnecessary for a baseline test.
4. Use **v5.1.8 or newer**. Version 5.1.8 fixed diagnostic logs that could expose Steam tokens, refresh tokens, access tokens, or login URLs. Never post a GameHub/GameHub Lite log without reviewing and redacting it.
5. Install one small test game and compare the same resolution, graphics settings, frame cap, and power mode used in GameNative.
6. Keep whichever frontend is more reliable for that title. Avoid storing duplicate large installs unless the A/B comparison is intentional.

At the time of this review, the latest release was **v5.1.8**. A newer release still needs its own security and change-log review.

### GameNative working-game logs

“Out of the box” means the installed game launched and was playable with the Odin controls without a manual per-game runtime, driver, wrapper, or input override beyond GameNative's automatically or explicitly applied known config; note when that config was required. Record exact per-game runtime, driver, wrapper, and translation-layer versions when they differ from the known/default configuration. Power and performance are observed results, not controlled benchmarks, until the Odin performance/fan mode, brightness, resolution, frame cap, measurement source, and test duration are also recorded.

#### Fully working out of the box

| Test date | Game/source | Per-game changes | Observed power | Performance | Result/notes |
| --- | --- | --- | --- | --- | --- |
| 2026-07-30 | 140 / Steam `242820` | None | **≈2 W** | **60 FPS** | **Fully working out of the box** |
| 2026-07-30 | 2064: Read Only Memories / Steam `330820` | None | **<2 W** | **60 FPS** | **Fully working out of the box** |
| 2026-07-30 | Celeste / Steam `504230` | None | **<2 W** | **60 FPS** | **Fully working out of the box** |
| 2026-07-30 | DuckTales: Remastered / Steam `237630` | Known config applied; no additional manual changes | **≈2 W** | **60 FPS** | **Fully working out of the box with known config** |
| 2026-07-30 | FINAL FANTASY VII REMAKE INTERGRADE / Steam `1462040` | None | TODO | TODO | **Fully working out of the box** |
| 2026-07-30 | FPV.SkyDive / Steam `1278060` | Known config applied; no additional manual changes | **<4 W** | **60 FPS** | **Fully working out of the box with known config** |
| 2026-07-30 | Geometry Wars: Retro Evolved / Steam `8400` | None; default configuration | **<2 W** | **60 FPS** | **Fully working out of the box** |
| 2026-07-30 | Loop Hero / Steam `1282730` | Known config applied with the current latest-default components | **≈2–2.5 W** | **60 FPS** | **Fully working out of the box with known config** |
| 2026-07-30 | Super Hexagon / Steam `221640` | None | **<2 W** | **60 FPS** | **Fully working out of the box** |
| 2026-07-30 | Tetris Effect: Connected / Epic Games | None | **<4 W** | **60 FPS** | **Fully working out of the box** |

#### Working with configuration or caveats

| Test date | Game/source | Required configuration/caveat | Observed power | Performance | Result/notes |
| --- | --- | --- | --- | --- | --- |
| 2026-07-30 | 80 Days / Steam `381780` | Keyboard/mouse input works, but no usable native Odin controller path was observed; configure and validate Steam Input or a GameNative physical-controller mapping | TODO | TODO | **Game launches and runs** |
| 2026-07-30 | Analogue: A Hate Story / Steam `209370` | Keyboard-focused interface, including a simulated command line; no handheld-friendly mapping recorded | **≈2–3 W** | Frame rate fluctuates substantially despite very low CPU/GPU utilization | **Technically works, but is a poor fit for controller-first handheld play.** The frame-rate variation has no observed practical impact on this interface-driven game |
| 2026-07-30 | Cairn / Steam `1588550` | Medium graphics preset; no graphical workaround recorded | **≈11 W** | **60 FPS** | **Runs with major graphical glitches:** mountain surfaces appear striped, and sunlight can blow out the entire image to white |
| 2026-07-30 | Delores: A Thimbleweed Park Mini-Adventure / Steam `1305720` | Same input limitation as 80 Days: configure and validate a controller-to-keyboard/mouse mapping | TODO | TODO | **Game launches and runs** |
| 2026-07-30 | Geometry Wars 3: Dimensions Evolved / Steam `310790` | Start from **Use Known Config** and change only FEXCore to 2605; retain the known-config Proton and graphics driver | **≈2 W** | **60 FPS** | **Works well after the FEXCore update.** The unchanged known config was choppy and inefficient; substituting Proton 11 or changing the graphics driver caused the game not to work |
| 2026-07-30 | Hyper Light Drifter / Steam `257850` | Proton 11 ARM64EC; Turnip Gen8 V30; FEXCore 2605 | **≈2–3 W** | **60 FPS** | **Works well with the recorded configuration.** The previous configuration consumed ≈9 W and could not sustain 60 FPS |
| 2026-07-30 | Koral / Steam `896750` | Effective configuration TODO; audio is completely absent | **≈4 W** | **60 FPS** | **Runs smoothly but without sound; XAudio troubleshooting pending** |
| 2026-07-30 | Later Alligator / Steam `966320` | Map the physical controller to mouse input | **<2 W** | **60 FPS** | **Works after configuring controller-to-mouse input** |
| 2026-07-30 | Oxenfree / Steam `388880` | Known config baseline; current default components and Proton 11 also work; map the physical controller to mouse input | TODO | TODO | **Runs correctly after configuring controller-to-mouse input** |
| 2026-07-30 | STEINS;GATE / Steam `412830` | Started from **Use Known Config** (including Proton 9 ARM64EC and System GPU driver); 1280×720 container; **Unpack Files** enabled; FEXCore updated to 2605; launcher set to Windowed, then Full Screen selected from the game's own menu after launch | **<2 W** | **60 FPS** | **Works after substantial configuration.** Does not require the Steam client with Unpack enabled; selecting Full Screen in the external launcher produces a black screen |

##### STEINS;GATE launch sequence

1. Apply GameNative's **Use Known Config** as the starting point. The tested base included Proton 9 ARM64EC and the System GPU driver; do not substitute the global Proton or Turnip defaults before establishing this baseline.
2. Leave the official executable as `Launcher.exe`; launching `Game.exe` directly stops with “Please launch from launcher.”
3. In **Edit Container**, use a 1280×720 screen, enable **Unpack Files**, and update FEXCore to 2605. The tested unpacked path removes the launcher's Steam Application Load Error without running the full Steam client.
4. In `Launcher.exe`, select **Windowed**. Center the window if necessary and start the game.
5. Only after the game itself has launched, select **Full Screen from the game's own menu**. This fills the display correctly.
6. Do **not** select Full Screen in the external launcher: that path renders a black screen even though audio starts.
7. The resulting configuration was measured at under 2 W and 60 FPS. Re-check the Unpack state after a game update or file verification before diagnosing a regression.

### GameNative compatibility-problem log

Record every attempt with a date and change only one variable between runs. “Compatible” is a report, not proof that the same store build, GameNative version, GPU, runtime, driver, and wrapper combination works on the Odin 3.

| Date | Game/source | Compatibility evidence | Config/runtime tests | Graphics/translation tests | Observed result | Next evidence/action |
| --- | --- | --- | --- | --- | --- | --- |
| 2026-07-30 | OlliOlli / Steam `274250` | GameNative labels it compatible, but the API result checked this date was only a **fallback**: GOG build on Adreno 650, not Steam on Adreno 830 | Both default and manually applied known config; Proton 9, 10, and 11 | Adreno `v805`, `Turnip_Gen8_V25`, and Turnip Gen8 V30; baseline Vulkan with DXVK `2.4.1-gplasync` | Every tested combination failed: either a launch crash or black screen | Run **Play with Diagnostics**, share/inspect the diagnostic log, record the exact GameNative/runtime package versions and failure stage, then test any wrapper change separately |
| 2026-07-30 | POOLS / Steam `2663530` | No working Odin 3 configuration recorded | TODO | Multiple GPU drivers tested; exact versions TODO | Game appears to run at **<3 W**, but rendering remains completely black with every tested driver | Run **Play with Diagnostics**; record the tested Proton, wrapper, and driver versions and whether menus/audio/input operate behind the black image |

### Storage interaction with Armada

- An Armada SD image consumes its dedicated card. It is not the same card as a normal Android games volume.
- If Android PC games live on microSD and Armada also boots from microSD, plan to swap between two clearly labeled cards.
- If Armada is installed internally, the microSD slot can remain dedicated to Android game storage, but Android receives only the internal capacity selected during the dual-boot split.
- Budget for game downloads, Wine containers/prefixes, shader caches, Android apps, and update headroom before choosing how much internal storage Android keeps.

## Armada Linux

[Armada](https://github.com/virtudude/armada) is a SteamOS-like ARM64 distribution built on Fedora bootc with device support from ROCKNIX. It provides ARM64 Steam, FEX, Proton, KDE desktop mode, OTA updates, and an Armada Control Decky plugin.

Upstream currently lists the **AYN Odin 3 / SM8750 as tested**, but also labels Armada prototype software. Flashing the required custom ABL can brick the device or damage Android, internal installation repartitions storage, and updates remain experimental.

At the time of this review, the latest image release was **20260725**. Use the [latest Armada release](https://github.com/virtudude/armada/releases/latest) and its current, release-pinned instructions rather than assuming these steps can never change.

### What “dual boot” looks like

| Layout | Android | Armada | Internal repartition/reset | Linux boot requirement | Recommended use |
| --- | --- | --- | --- | --- | --- |
| **Armada on SD** | Remains on internal storage | Dedicated microSD | No repartition or Android factory reset; **custom ABL is still flashed to both ABL slots** | Armada card inserted | First test and safest rollout |
| **Armada alongside Android internally** | Keeps a chosen share of internal storage | Uses the remainder | First split normally **factory-resets Android** | No card after installation | Long-term dual boot after validation |

The custom ROCKNIX ABL supplies the OS selection:

- Enter its menu with **VOL−** during boot, navigate with **VOL−/VOL+**, and select with **POWER**.
- Select the default Android or Linux boot mode in that menu.
- When Linux is the default, hold **VOL+** during boot for a one-time Android override.
- With internal Armada installed, the ABL prefers internal Linux over the Armada SD card.

### Recommended rollout

1. Keep Android internal and run Armada from SD.
2. Confirm that Android still boots and that the ABL backup is copied off-device.
3. Test Armada Wi-Fi, audio, controls/triggers, charging, suspend, display, Steam, a small game, and any required dock.
4. Keep using SD boot for a few sessions and review current Odin 3 issues.
5. Only then decide whether the convenience of internal Armada is worth an Android factory reset and reduced storage for each OS.

### 1. Download and verify Armada

1. Download `armada-YYYYMMDD.img.gz` from the [latest release](https://github.com/virtudude/armada/releases/latest).
2. Compare its SHA-256 value with the checksum in that release:

   ```sh
   # Linux
   sha256sum armada-*.img.gz

   # macOS
   shasum -a 256 armada-*.img.gz

   # Windows PowerShell
   Get-FileHash .\armada-*.img.gz -Algorithm SHA256
   ```

3. Flash the compressed image to the dedicated 64 GB-or-larger A2 microSD card with [Balena Etcher](https://etcher.balena.io/) as currently recommended upstream. Triple-check the target disk; flashing erases it.
4. Safely eject the card and insert it into the Odin 3.

### 2. Back up and flash the correct ABL

> [!CAUTION]
> The Odin 3 uses **SM8750**. Do not run the SM8550 or SM8650 script. Armada warns that flashing the wrong SoC's ABL can brick the device.

1. Boot Android with the flashed Armada card inserted.
2. Copy the `rocknix_abl` directory from the card to the **root of Android internal storage**.
3. In Android's built-in **Run script as root** tool, browse to `rocknix_abl/SM8750`.
4. Run `backup_abl.sh`.
5. Confirm that the folder now contains both `abl_a.img` and `abl_b.img`.
6. Copy those two files to the host computer and to a second backup location. Keep them together and label them with this device and date. Optionally record their host-side SHA-256 hashes.
7. Only after verifying both off-device backups, run `flash_abl.sh` from the same **SM8750** folder.

The scripts back up and replace both `abl_a` and `abl_b`; losing those device-specific originals removes an important stock-recovery path.

### 3. First boot from SD

1. Reboot while holding **VOL−** to enter the custom ABL menu.
2. Set the device model to **AYN Odin 3**.
3. Set/toggle the boot mode to **Linux**.
4. Choose **Start**.
5. Wait through the intro. The current SD path can show a black screen for 30–60 seconds before Steam appears; do not assume it has failed immediately.
6. Complete Steam's language, timezone, Wi-Fi, and login setup. A second long black screen may occur when Steam restarts.

### 4. Secure and validate Armada

Armada currently ships with user `armada` and password `armada`. SSH starts disabled.

1. Enter Desktop Mode, open a terminal, and change the password:

   ```sh
   passwd
   ```

2. Do not enable SSH in Armada Control until the default password has been changed.
3. Use the **Beta** update channel for normal use; upstream describes **Preview** as the bleeding-edge channel. OTA updates are still experimental, so keep the working SD/recovery path available.
4. In **Armada Control**, select a conservative power profile and calibrate the sticks/triggers if needed.
5. Validate and record:

   - [ ] Wi-Fi and Bluetooth
   - [ ] Built-in audio and 3.5 mm audio
   - [ ] Sticks, D-pad, buttons, L2/R2, and haptics
   - [ ] Charging while active, suspended, and powered off
   - [ ] Power button suspend/wake and idle drain
   - [ ] Display brightness and color
   - [ ] Steam download, launch, and cloud save
   - [ ] Desktop Mode and return to Gaming Mode
   - [ ] USB/dock/external display, if required
   - [ ] One-time Android boot with **VOL+**
   - [ ] Persistent Android/Linux selection in the **VOL−** menu

### 5. Optional internal dual boot

> [!WARNING]
> A fresh **Install alongside Android** repartitions internal storage and factory-resets Android. Back up everything first. A failed/interrupted install can require a host computer and `fastboot` recovery.

1. Boot Armada from the SD card.
2. Switch to **Desktop Mode**.
3. Open **Armada Installer** from the **System** menu.
4. Choose **Install alongside Android** and select how much storage Android keeps; Armada receives the rest.
5. Let the installer finish without interruption.
6. Power off completely, remove the Armada SD card, and power on. Internal storage has boot priority.
7. Boot Android, complete its factory-reset setup, and restore/reinstall GameNative and game data.
8. Re-test both boot paths and update the device record.

If Armada or ROCKNIX was already installed, the installer may offer **Reinstall / Switch to Armada**, which replaces the existing Linux installation without resizing or wiping Android. Read the exact option and current upstream instructions before confirming it.

### Day-to-day booting

- **Normal default:** the ABL starts the selected default OS.
- **One-time Android boot while Linux is default:** hold **VOL+** during startup.
- **Change the default:** hold **VOL−**, change the boot mode in the ABL menu, then choose **Start**.
- **SD layout:** insert the Armada card for Linux.
- **Internal layout:** no Armada card is needed; internal Linux takes precedence over an inserted Armada card.

### Recovery, reinstall, and removal

Keep the Armada SD card, host Platform-Tools, and original `abl_a.img`/`abl_b.img` backups even after an internal install.

#### Force an internal install back to SD

This erases Armada's internal **boot partition**, not the complete Linux installation:

1. Power off, then hold **VOL−** while powering on and remain in the bootloader.
2. Connect the host and confirm the device is visible:

   ```sh
   fastboot devices
   ```

3. Run the upstream recovery command:

   ```sh
   fastboot erase ROCKNIX
   ```

4. Reboot with the Armada SD card inserted. It should now boot the SD copy.
5. Use **Armada Installer** from the SD copy to reinstall or remove the internal installation.

#### Remove internal Armada and return the space to Android

From the SD-booted Armada Installer, choose **Remove & Restore Android**. This removes the Armada/ROCKNIX install and returns the internal disk to Android; Android factory-resets on its next boot.

Treat restoring the internal partition layout and restoring the **stock ABL** as separate operations. After Android is restored and working, copy `rocknix_abl` back to internal storage and place this device's saved `abl_a.img` and `abl_b.img` in its `SM8750` folder. The image's `restore_backup_abl.sh` can then write those images back to both slots.

That is another high-risk bootloader write: use only the exact backups created from this Odin 3, confirm both files and their hashes before proceeding, keep a second copy, and re-check current upstream recovery guidance before running it. Never substitute another device's ABL images.

### Current caveats to check

Armada's release documentation calls out experimental updates, occasional 30–60 second black screens, intermittent red tint, and higher idle drain from its suspend approach. Open Odin 3 reports at the time of this review also include:

- [standby/sleep charging](https://github.com/virtudude/armada/issues/143);
- [dock/external display output](https://github.com/virtudude/armada/issues/148);
- [haptics](https://github.com/virtudude/armada/issues/134);
- [L2/R2 triggers](https://github.com/virtudude/armada/issues/121);
- [wired audio](https://github.com/virtudude/armada/issues/120); and
- [SDR brightness](https://github.com/virtudude/armada/issues/171).

These are reports, not proof that every Odin 3 or every build is affected. Review the [current open Odin 3 issues](https://github.com/virtudude/armada/issues?q=is%3Aissue+is%3Aopen+Odin+3) and the latest release notes before installing or updating.

## Setup log

| Date | Component | Version/build | Action | Result/notes |
| --- | --- | --- | --- | --- |
| TODO | Android | TODO | Baseline recorded | TODO |
| TODO | Obtainium | TODO / not installed | Upstream APK updater configured | TODO |
| 2026-07-30 | RetroArch | TODO / installed | Stable AArch64 app installed | Required cores, controls, BIOS, and game tests pending |
| 2026-07-30 | DuckStation | TODO / installed | App installed | BIOS, controls, storage, and PS1 test pending |
| 2026-07-30 | HakuX | TODO / installed | App installed | Original-Xbox firmware, virtual HDD, controls, and game test pending |
| 2026-07-30 | ScummVM | TODO / installed | App installed | Library, controls, and game test pending |
| 2026-07-30 | Dolphin | TODO / installed | App installed | GameCube/Wii controls, storage, and game tests pending |
| 2026-07-30 | XenDroid | TODO / installed | App installed | Xbox 360 configuration and game test pending |
| 2026-07-30 | Argosy | v2.4.1 / evaluated | First-run flow tested | Not selected: current app requires RomM login and exposes no server-free onboarding path |
| 2026-07-30 | NeoStation | TODO / installed | ROM root and GameNative Steam Frontend Sync configured | `.steam` marker discovery/integration confirmed; version and per-emulator launch tests pending |
| 2026-07-30 | GameNative | TODO / installed | Steam Frontend Sync and initial game testing | 140, 2064: Read Only Memories, Celeste, DuckTales: Remastered, and FFVII Remake Intergrade work out of the box; STEINS;GATE works after a workaround; Cairn has major glitches; 80 Days and Delores need keyboard/mouse; OlliOlli fails; cloud-save tests pending |
| TODO | GameHub Lite | TODO / not installed | Fallback test | TODO |
| TODO | ClusterTune | TODO / not installed | CPU-cap test | Not in use; do not run alongside PULSE |
| 2026-07-30 | PULSE | TODO / installed | Frequency/power profile configured | Active: 60 FPS target, Aggressive Park, Efficient 11 W, Smart Fan, and 60 Hz |
| TODO | Armada SD | TODO | Flashed and booted | TODO |
| TODO | ROCKNIX ABL | TODO | Backed up/flashed | TODO |
| TODO | Armada internal | TODO / not installed | Dual-boot install | TODO |

## References

### Android emulation

- [RetroArch Android installation documentation](https://docs.libretro.com/guides/install-android/)
- [RetroArch downloads](https://www.retroarch.com/?page=platforms)
- [Obtainium repository](https://github.com/ImranR98/Obtainium)
- [Obtainium Emulation Pack](https://github.com/RJNY/Obtainium-Emulation-Pack)
- [F-Droid security and signing model](https://f-droid.org/docs/Security_Model/)
- [Android Emulation Starter Guide](https://retrogamecorps.com/2022/03/13/android-emulation-starter-guide/)
- [EmuReady handheld compatibility listings](https://www.emuready.com/listings)
- [ES-DE Android documentation](https://gitlab.com/es-de/emulationstation-de/-/blob/master/ANDROID.md)
- [Dolphin official releases](https://dolphin-emu.org/download/)
- [PPSSPP official downloads](https://www.ppsspp.org/download/)
- [Azahar repository and Android installation options](https://github.com/azahar-emu/azahar)
- [melonDS Android repository](https://github.com/rafaelvcaetano/melonDS-android)
- [Flycast repository](https://github.com/flyinghead/flycast)
- [NetherSX2 Patch repository](https://github.com/Trixarian/NetherSX2-patch)
- [ARMSX2 official site](https://armsx2.net/)
- [Vita3K current cross-platform build releases](https://github.com/Vita3K/Vita3K-builds/releases/latest)
- [Cemu experimental Android port](https://github.com/SSimco/Cemu)
- [Eden downloads and Android build guidance](https://eden-emu.dev/downloads/)
- [HakuX original-Xbox Android fork](https://github.com/rfandango/hakuX)
- [X1 BOX original-Xbox Android fork](https://github.com/izzy2lost/xemu)
- [XenDroid Xbox 360 Android port](https://github.com/rfandango/XenDroid)
- [aX360e Xbox 360 Android project](https://github.com/aenu1/ax360e)
- [Official xemu mobile-port FAQ](https://xemu.app/docs/faq/#q-are-there-any-plans-for-an-android-or-ios-port)
- [Official Xenia repository](https://github.com/xenia-project/xenia)
- [aPS3e experimental Android PS3 port](https://github.com/aenu1/aps3e)
- [RPCSX experimental Android UI](https://github.com/RPCSX/rpcsx-ui-android)
- [Emulation General Wiki: emulators on Android](https://emulation.gametechwiki.com/index.php/Emulators_on_Android)

### Android PC gaming

- [GameNative repository](https://github.com/utkarshdalal/GameNative)
- [GameNative releases](https://github.com/utkarshdalal/GameNative/releases/latest)
- [GameNative compatibility database](https://gamenative.app/compatibility/)
- [GameNative privacy policy](https://github.com/utkarshdalal/GameNative/blob/master/PrivacyPolicy/README.md)
- [GameNative third-party notices](https://github.com/utkarshdalal/GameNative/blob/master/THIRD_PARTY_NOTICES)
- [GameHub Lite repository](https://github.com/Producdevity/gamehub-lite)
- [GameHub Lite releases](https://github.com/Producdevity/gamehub-lite/releases/latest)
- [GameHub Lite v5.1.8 token-log security fix](https://github.com/Producdevity/gamehub-lite/releases/tag/v5.1.8)
- [EmuReady PC compatibility listings](https://www.emuready.com/pc-listings)

### Android tuning

- [ClusterTune repository](https://github.com/AurelioB/ClusterTune)
- [ClusterTune releases](https://github.com/AurelioB/ClusterTune/releases/latest)
- [ClusterTune Odin 3 bundled profiles](https://github.com/AurelioB/ClusterTune/blob/main/app/src/main/assets/bundled_profiles/CQ8725S.json)
- [PULSE repository](https://github.com/keiretrogaming/pulse)
- [PULSE releases](https://github.com/keiretrogaming/pulse/releases/latest)
- [Original Odin 3 CPU underclock scripts](https://github.com/TheOldTaylor/Odin3-CPU-Underclock)
- [O2P Tweaks](https://github.com/FeralAI/o2ptweaks.app)

### Armada and recovery

- [Armada repository and current instructions](https://github.com/virtudude/armada)
- [Armada releases](https://github.com/virtudude/armada/releases/latest)
- [ROCKNIX ABL](https://github.com/ROCKNIX/abl)
- [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools)
