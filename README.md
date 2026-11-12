# Digimon Story: Time Stranger (Switch) — recomp experiment

> **Status: experiment / feasibility test. Nothing here runs the game yet.**

This repository is a fork of [LASRecomp-Android-Plus](https://github.com/Nycolazs/LASRecomp-Android-Plus)
(MIT, by Nycolazs, built on [LAS Recomp](https://github.com/peiikai/LASRecomp) by peiikai). The goal is to test whether
the same static-recompilation approach can be applied to the Nintendo Switch version of
*Digimon Story: Time Stranger* and run on Android.

## What is NOT in this repository (on purpose)

- No game, no `.nsp` / `.xci` / `.nca` / `.nso`, no `romfs` / `exefs`
- No `prod.keys` / `title.keys`
- No recompiled game code (anything derived from the game binary)
- No ready-made APKs that contain the game

You need your **own legally dumped copy** of the game. Never commit any of the files above.
`.gitignore` blocks the usual ones, but check before you push.

## What is in here

Everything is still the upstream base (a Link's Awakening recomp): the recompiler/lifter (`tools/`), the runtime
(NVN on Vulkan, Switch OS services, SDL3, `runtime/`) and the Android app (`android/`). The upstream README is kept at
`docs/upstream/README-LASRecomp-Android-Plus.md`; the on-phone build notes are in `docs/android-ondevice-build.md`.

## What still has to be done for Digimon (not started)

1. **Feasibility test:** can `tools/gen.py` / `tools/lifter.py` lift the game's main binary without errors?
2. **Game address tables:** the base keeps per-build address tables (`runtime/game_addresses/`) and game-specific
   patches (`tools/patches.py`). An unknown build ID is an error, so these must be found for Digimon's binary.
3. **Importer:** the in-app importer is tied to the upstream game's title ID and skips updates; Digimon needs its
   update (Ver. 1.2.1) applied on top of the base.
4. **Rendering / performance work** on a real phone (target device: POCO F5, Adreno 725, 12 GB), driven by logs.

## CI

The upstream CI runs only when started by hand (Actions tab → CI → Run workflow). It builds the app without any game
code.

## License

MIT, see `LICENSE` (the upstream copyright notice is kept). The license covers this code only; it grants nothing for
Nintendo's or Bandai Namco's game.
