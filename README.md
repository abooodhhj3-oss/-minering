# EldenCraft for Elden Ring 1.17 — test build 3

An experimental compatibility port of SocketByte's EldenCraft 0.25.5 for the exact executable you measured. Original EldenCraft is by Adam Grzegorzewski (SocketByte), under MIT. Minecraft Ring on Melty is credited to Melty; this package has not been imported into or approved by Melty.

Target: App 1.17 / product 2.7.0.0, executable 87,024,720 bytes, SHA-256 `d1a84083c6c7c7902162ff098f7d86812839aa6b3575959398857e539c488134`. Build 23850278, depot 1245621 and manifest 8950790353897081369 were supplied by you; the executable hash is the enforced identity.

The port retains upstream gameplay: real Minecraft terrain, mining and block placement with your Elden Ring character, native movement/collision, combat, interactions, camera passthrough and campaign integration. This build changes compatibility and startup verification, without adding new gameplay systems. Test 2 reads executable version metadata from a bounds-checked on-disk PE, installs fault recording before verification, writes synchronous startup stages, waits for the real host window, and collects recent Elden Ring crash events.

## First test

1. Close both games. Extract the **entire** ZIP to an ordinary local folder, such as `C:\Games\EldenCraft117`. Run `EldenCraft.cmd` from there. Do not run files from inside the ZIP. No administrator permission should be required.
2. If the file picker appears, select your measured `Game\eldenring.exe`. An appmanifest is not required. Keep the installation's normal `Game` folder structure, which ModEngine2 expects.
3. The launcher automatically installs portable Prism, Java, Fabric API and ReShade from the official URLs with pinned hashes. ModEngine2 is bundled and verified. It does not install anything system-wide or replace game files. Internet access is required for initial Minecraft installation and sign-in.
4. In Prism, open **Accounts → Manage Accounts → Add Microsoft** and sign in with the account that owns Minecraft Java. This login is the step only you can do. Close Prism after signing in; the launcher continues.
5. Minecraft opens the dedicated EldenCraft world. Elden Ring starts offline through ModEngine2, with Easy Anti-Cheat off. Steam ownership/authentication remains in place. **Create a new Elden Ring character for testing.** ModEngine2 uses regular Elden Ring saves; it does not provide upstream me3's separate `EldenCraft.sl2`. Verified save backups are made before every launch in `%LOCALAPPDATA%\EldenCraft117\backups`.
6. In Elden Ring, enter the character's world. Check Minecraft passthrough, movement/camera, mining one block, placing it, fighting one enemy, using an interaction prompt, then quitting/restarting. The native module will disable a hook if its exact signature differs. Stop and collect diagnostics if it fails or crashes; do not turn off checks.
7. Run `Troubleshoot.cmd` and send the support ZIP it produces. It collects component logs and redacts common account tokens and user paths. Send one real screenshot or short clip if both games work; public release media must show this port running.

Runtime files are isolated under `%LOCALAPPDATA%\EldenCraft117`; existing EldenCraft runtime settings/worlds are not reused. Regular Elden Ring saves are the exception noted above. `EldenCraft.cmd check` verifies the game identity and release without starting either game. For an explicit path, run `EldenCraft.cmd play -GamePath "D:\Games\ELDEN RING\Game\eldenring.exe"`.

## What passed here

All 55 measured code guards were imported from your two reports. Six relative operand changes were reviewed and kept as exact target bytes. Camera/menu vtable destinations were checked against your report. The pinned official 1.17 SDK supplies its declarations; one additional talk-event method is resolved from its SDK vtable at runtime and exact-byte checked. The SDK field visibility patch does not change memory layout.

Source-sheet preflight is clean. Windows x64 core and loader DLLs compile with Rust 1.99.0 and authenticated Debian MinGW. Under Wine 10, 495 core tests and five loader tests passed. These exercise native logic, not an Elden Ring process. The generated launch arguments, PowerShell syntax, TOML escaping and missing loader exit-code handling and rejection of an absent/wrong/windowless host passed portable checks. Four additional Windows tests validate file-backed PE metadata acceptance and rejection of wrong versions, products and languages. The Minecraft bridge and compositor are unchanged, hash-verified official EldenCraft 0.25.5 release artifacts; the native port was built from source.

**Evidence from your test 1 support ZIP:** ModEngine2 launched the exact measured game, ReShade and the native loader were injected, and Minecraft started. Elden Ring then exited before creating a graphics window; the native log stopped during verification. This does not prove the cause was metadata access. Test 2 removes that unsafe resource-read path and adds evidence to diagnose any remaining early exit.

**Still untested:** test 2 on Windows, graphics/compositor behavior, game memory layouts and gameplay. The cloud has no Elden Ring installation or Windows graphics session. ReShade and Fabric's domains are blocked here, so their pinned downloads execute automatically on your PC but have not been downloaded or installed here. Melty is also network-blocked: no Melty remix draft, recipe validation, one-click check, upload or publication occurred. Do not call this build game-verified or published.

`PORT-PROVENANCE.json` identifies every built/reused input. `port-source.zip` contains source, sheets and generators. No game binaries, saves, accounts, keys or generated gameplay media are included. Keep `LICENSE`, `THIRD_PARTY_NOTICES.md` and `licenses/` with redistributed files.

## Diagnostics fix 1

Test 2 failed to bind diagnostics parameters because generated settings preceded param(). This revision initializes settings after the parameter block. Script AST/parameter binding checks and full noninteractive collector execution with a synthetic PE fixture passed on Linux PowerShell 7.4.6. Windows PowerShell 5.1 and Windows Event Log access remain untested here. Native DLLs are identical to test 2; no native rebuild or new tools were needed.

To diagnose the already-failed test 2 launch, extract this ZIP and run its Troubleshoot.cmd. It reads the existing LocalAppData/EldenCraft117 logs and creates a support ZIP. You do not need to repeat the game launch first. Upload that support ZIP to continue the early-exit investigation.

## Test 3: recorded memory failure

The latest support ZIP confirms the core disabled itself with `out of memory` before metadata parsing. Test 2 allocated the entire 87 MB executable, and the SDK could allocate another copy. Test 3 hashes/parses an exact-length read-only disk mapping without an executable-sized heap buffer; version/hash/signature/offline guards remain. The persistent loader now waits for the core to verify identity before any SDK task lookup and fails closed on rejection/timeout. Minecraft's maximum heap is reduced from 4096 to 2048 MB, with 1024 MB minimum, to leave more memory for Elden Ring on this 16 GB PC. Diagnostics now include process memory, available physical/virtual memory and instance.cfg. Windows event collection returned RPC server unavailable in the uploaded report, so those records did not establish the game-exit cause.

Close all Minecraft and Prism windows before using test 3 so the new heap cap takes effect. Extract the entire ZIP into a new folder, run EldenCraft.cmd and allow up to 90 seconds. Existing verified downloads/account/world are reused. If Elden Ring opens, enter a new test character's world and check composition, movement, mining/placement, combat/interactions and restart. If it fails, run this package's Troubleshoot.cmd and send its ZIP. The recorded allocation failure is addressed in code, but success in the game remains unverified.

Cloud checks passed: 495 core + 5 loader tests under Wine, including exact-length mapped SHA256 (excluding page padding), empty-file rejection, mapped/disk PE product/version/language cases, and success/rejection/timeout/unknown verification states. Portable PowerShell checks confirm 1024/2048 heap settings, parameter binding, window checks and filtered event selection. The full Linux fixture collector produces a valid ZIP. No new tools were installed for test 3.
