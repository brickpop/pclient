---
type: spec
status: planned
---

# Architecture

## Shape

- **One process, owning everything.** The app holds the pCloud token, talks to the pCloud API, keeps the [remote index](remote-index.md) and serves the [mount](mount.md) as a libfuse3 filesystem. No subprocess, no rclone, no local server.
- **Nothing is exposed.** No TCP port, no Unix socket, no remote-control API. Other processes see only the mounted folder, read-only. See [mount options](../research/mount-options.md) for why rclone's remote-control API and `nfsmount` were rejected on these grounds.
- **Core and shell split, as in heads-up** ([prior art](../research/prior-art.md)): a `Core` module with all the logic, built and tested on Linux, and a macOS `App` module (SwiftUI and AppKit, Keychain, libfuse3 glue). The manifest adds the shell only on macOS.
- **Build without Xcode**: Swift Package Manager, a `justfile`, `tools/make-app.sh` assembling and signing the `.app` ad-hoc with the hardened runtime, as heads-up does.

## Dependencies

- **macFUSE 5.x** (the only one), installed by the user: its licence forbids bundling it. The app detects it and guides the install and the step of enabling its file system extension in System Settings. See [dependency maturity](../research/dependency-maturity.md).
- **libfuse3** comes with macFUSE. With the hardened runtime, macOS refuses to load a library signed by another team, so the app needs `com.apple.security.cs.disable-library-validation` (not a restricted entitlement: ad-hoc signing can carry it). See [Apple signing](../research/apple-signing.md).
- **SQLite**, from the system, for the index.

## Requirements

- Apple silicon.
- macOS: see open question 1.

## Open questions

1. **Minimum macOS.** macFUSE's FSKit backend mounts "local" volumes on macOS 15.4 and later and "non-local" ones only on macOS 26. Which kind a libfuse3 mount is, and so whether macOS 15.4 works, is for the [FSKit spike](../backlog/spike-fskit-mount.md) to answer. Recommendation: 15.4 if the spike passes there, otherwise 26.
2. **Library validation.** Disable it for the whole app, or run the filesystem in a helper process inside the bundle that alone carries the entitlement, keeping the main app strict? Recommendation: whole app first; split only if it proves to matter.
