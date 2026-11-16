---
type: note
timestamp: 2026-11-16
tags: [dependencies]
---

# Dependency maturity

How we judge a dependency before trusting files to it.

## Desk check

For each one: how often it releases and how fast macOS issues get fixed; whether recent releases fixed data-corruption bugs; whether it's open source; whether we can pin an exact version; who maintains it.

## Where things stand (2026-11-16)

- **macFUSE kernel backend**: mature, closed source since version 4, one maintainer. Installing it on Apple silicon needs a security downgrade in Recovery mode.
- **macFUSE FSKit backend**: young. Added in 5.0 (2025-05), libfuse3 only, mount moved over XPC in 5.2, and 5.5.0 (pre-release) mitigates a data-corruption issue on macOS 26 and 27. Early rclone reports said reads and writes failed; later ones are better.
- **FSKit itself**: Apple says it's not yet on par with the kernel's filesystem API.
- **rclone**: transfers and the pCloud backend are mature; its macOS mount layer is the weak part. Not used in the current design ([mount options](mount-options.md)).
- **macFUSE licence**: restricts redistribution, so it can't be bundled. Personal use is believed fine; [to be checked](../backlog/check-macfuse-licence.md).

## Testing on the Mac

The desk check isn't enough. A test script runs against the real mount and checks: listing large folders, reading with checksums against the API's, accented file names (NFC/NFD), a 5 GB file, many small files, random byte ranges; then sleep and wake, Wi-Fi off and on, and the app killed mid-read. It runs on each macOS and macFUSE version we support and keeps its results. See the [soak test task](../backlog/mount-soak-test.md).
