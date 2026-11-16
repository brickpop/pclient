---
type: spec
status: planned
---

# Writes

Creating, changing, renaming and deleting files through the [mount](mount.md). Wanted soon after the read-only first version (decided 2026-11-16). This is where a cloud filesystem loses data, so the design favours simple and safe over clever.

## Behaviour (proposed)

- **Whole-file uploads.** A file being written lives in a local staging folder. When it is closed (or synced), the whole file is uploaded; the remote file is replaced only once the upload has completed. No partial remote writes.
- **Upload queue on disk**, surviving quit, crash and restart. Staged files are never evicted from the cache until uploaded.
- **Conflicts.** Before replacing a remote file, check it is still the version the local copy started from (pCloud file hash or modification time, via the [remote index](remote-index.md)). If it changed meanwhile, upload as a separate "conflicted copy" next to it and say so; never overwrite silently.
- **Renames, moves, folder creation and deletes** map to single API calls, applied at once and reflected in the index.
- **Deletes go to pCloud's trash**, as deletes in pCloud do, so they can be undone there.
- **[Ignore patterns](mount.md)**: matching files can be created locally (Finder needs `.DS_Store`) but are never uploaded.
- **Quitting** with uploads pending asks first, as heads-up's `QuitQuestion` does; the [menu bar app](menu-bar-app.md) shows the queue.

## Open questions

1. **Our own write path or rclone?** Our own keeps one process, nothing exposed and FSKit; rclone's VFS is proven but only runs on macFUSE's kernel extension today ([mount options](../research/mount-options.md)). Recommendation: our own, as above; revisit if the [soak test](../backlog/mount-soak-test.md) shows it can't be made safe.
2. **Large files.** Whether pCloud's upload API can resume an interrupted upload of a large file, or it must start over. To check in the [API spike](../backlog/spike-pcloud-api.md).
3. **Files open in two places.** An app editing a file while a newer version arrives from another device: show the new one only after the file is closed? Recommendation: yes.
