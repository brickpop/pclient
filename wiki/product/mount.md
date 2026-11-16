---
type: spec
status: planned
---

# Mount

pCloud appears as a folder in Finder, **read-only**. The app serves it itself as a libfuse3 filesystem, through macFUSE's FSKit backend (`-o backend=fskit`): no kernel extension, no rclone. See [mount options](../research/mount-options.md) for every alternative and why this one.

## Behaviour

- **Mount point** is a setting, `~/pCloud Drive` by default. If something is already mounted there (such as the official pCloud app), the app says so and doesn't mount.
- **Listings and attributes** come from the [remote index](remote-index.md).
- **Reads** fetch a download link with `getfilelink` and read the byte ranges asked for, through a **block cache on disk** with a size limit set in Settings, oldest blocks evicted first.
- **Read-only in the first version**, enforced by the filesystem: every write call fails with "read-only file system". [Writes](writes.md) follow soon. Read-only first because it removes the hard, data-losing parts (upload queue, conflicts, partial writes) while macFUSE's FSKit backend proves itself.
- **Ignore patterns**: a global list in Settings, hidden from listings. Defaults: `._*`, `.DS_Store`, `.Spotlight-V100`, `.Trashes`, `.fseventsd`. While the mount is read-only, the list hides ones uploaded from other machines; once [writes](writes.md) exist, files matching it are also never uploaded.
- **Lifecycle**: mounted at app start once signed in, unmounted at quit. A mount left behind by a crash is force-unmounted at the next start. After sleep or a network change the mount stays; reads wait and retry, then fail with an I/O error rather than hang Finder.
- **Not supported**: pCloud Crypto folders (encrypted on the client with pCloud's own scheme).

## Open questions

1. **Can FSKit mount in the home folder?** Reports suggest FSKit mounts may be confined to `/Volumes`. If so, mount there and put a symlink at `~/pCloud Drive`. The [FSKit spike](../backlog/spike-fskit-mount.md) answers it.
