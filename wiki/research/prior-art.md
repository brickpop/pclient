---
type: note
timestamp: 2026-11-16
---

# Prior art

## The official pCloud client

The reason for this project: it hangs, logs out on its own and has to be restarted. It mounts at `~/pCloud Drive` with its own FUSE layer, so our [mount](../product/mount.md) must detect a clash at that path.

## Rclone Browser

A Qt GUI that runs `rclone` commands (one process per listing, copy or mount) and parses their human-readable output. On macOS it needs macFUSE. Its stats broke whenever rclone changed its output format, and it looks unmaintained since about 2020. Lesson: don't parse human-readable output of another program.

## heads-up (`../heads-up`)

The developer's own menu bar app, the model for this one's build and UI. Worth reusing:

- `HeadsUpCore` (logic, built and tested on Linux) and `HeadsUpApp` (macOS shell) in one Swift package; the manifest adds the shell only on macOS.
- `justfile` recipes (`app`, `test`, `lint`, `build`), `tools/make-app.sh` (ad-hoc signing, hardened runtime), `tools/setup-swift.sh` (pinned Linux toolchain), `tools/lint.sh` (no Apple imports in the core, entitlements and Info.plist rules).
- `LSUIElement` menu bar app, `StatusItem.swift`, `HUDStyle` and button styles.
- `KeychainSecretStore` ([credential handling](credential-handling.md)) and `LoginItemAdapter` (`SMAppService.mainApp`).
- Settings as JSON in Application Support; a `CSQLite` system library module.

Not needed here: the ⌥Space HUD with tabs, chat, focus timer and the rest.

Copy the patterns into this repo; extract a shared Swift package only once both apps clearly need the same code.
