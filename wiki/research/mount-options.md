---
type: note
timestamp: 2026-11-16
tags: [decision]
---

# Mount options

Every way we found to put pCloud in Finder, and why we chose a read-only libfuse3 filesystem of our own on macFUSE's FSKit backend. The decision lives in the [mount spec](../product/mount.md).

## The options

| Option | How it works | Verdict |
|---|---|---|
| rclone `mount` + macFUSE kernel extension | rclone's VFS cache, served through macFUSE's kext | **Fallback.** Mature, but the kext needs a security downgrade in Recovery mode on Apple silicon. Homebrew's rclone has `mount` removed, so it would mean bundling the official binary. |
| rclone `mount` + macFUSE FSKit | Same, without the kext | **Doesn't work today.** FSKit needs libfuse3; rclone mounts through cgofuse, which uses libfuse2 on macOS (its libfuse3 support covers Linux and FreeBSD only). |
| rclone `nfsmount` | rclone runs an NFS server on localhost; macOS's NFS client mounts it | **Rejected.** The NFS server listens on a localhost TCP port with no authentication: any local process can mount the account. Also had Finder write bugs and an open NFC/NFD filename issue. |
| FUSE-T | FUSE without a kext; NFS internally, FSKit on macOS 26 | **Not chosen.** Reported working with rclone through FSKit, but less proven, and NFS-based in its classic mode. Worth a look if macFUSE disappoints. |
| Our own FSKit module | Native Apple framework | **Not possible for free.** Needs restricted entitlements and so a paid Apple Developer account. See [Apple signing](apple-signing.md). |
| File Provider extension | What iCloud, Dropbox and Google Drive use; lives in `~/Library/CloudStorage` | **Not now.** The most native long-term answer, but needs a paid account, probably Xcode, and isn't a mount (a symlink could stand in). |
| **Our own libfuse3 filesystem + macFUSE FSKit** | The app serves the filesystem; macFUSE's signed extension turns it into FSKit | **Chosen.** No kext, no paid account, no subprocess, nothing exposed. Read-only makes it small: list, stat, open, read, with a block cache. Shares its metadata with search. |

## Why read-only first

Writes are where data gets lost: upload queues, conflicts, partial writes, cache eviction of unsent data. rclone solves those and we would be rewriting them. Read-only keeps our filesystem small and keeps pCloud safe while macFUSE's FSKit backend matures (5.5.0 was still fixing a data-corruption bug). Writes are wanted soon (decided 2026-11-16): the plan is our own write path, kept simple ([writes](../product/writes.md)), with rclone + kext as the fallback if it proves too hard.

## Remote control without exposure

An earlier design ran rclone as a child process and drove it through its remote-control API for stats. Rejected: that API can delete files and needs a TCP port or socket. Had rclone stayed, the private alternatives were its JSON logs on a pipe the app owns (`--use-json-log --stats`), `SIGHUP` to drop cached listings and `SIGUSR2` to toggle the bandwidth limit. With our own filesystem the question is gone.

## Sources

- [rclone issue 7503: nfsmount, Finder writes, NFC/NFD](https://github.com/rclone/rclone/issues/7503)
- [rclone: Homebrew mount stub](https://git.frostfs.info/satyamisme/rclone/src/branch/gzip/cmd/cmount/mount_brew.go)
- [rclone forum: mounting on macOS using NFS](https://forum.rclone.org/t/mounting-on-macos-using-nfs-is-now-on-master/42194)
- [rclone forum: macFUSE FSKit backend](https://forum.rclone.org/t/macfuse-fskit-backend/53037)
- [rclone forum: rclone mount with fuse-t via FSKit](https://forum.rclone.org/t/macos-rclone-mount-with-fuse-t-via-fskit/53608)
- [cgofuse issue 104: libfuse3 and FSKit on macOS](https://github-users.yhajoa.workers.dev/winfsp/cgofuse/issues/104)
- [macFUSE 5.0.0](https://macfuse.github.io/2025/05/05/macfuse-5.0.0.html), [5.1.0](https://macfuse.github.io/2025/10/30/macfuse-5.1.0.html), [5.5.0 notes](https://newreleases.io/project/github/macfuse/macfuse/release/macfuse-5.5.0)
- [fuse-t 1.1.0 notes](https://newreleases.io/project/github/macos-fuse-t/fuse-t/release/1.1.0)
