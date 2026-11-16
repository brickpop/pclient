---
type: task
title: "Spike: a libfuse3 filesystem on macFUSE's FSKit backend"
status: todo
priority: high
assignee:
tags: [spike]
---

The riskiest assumption in the design ([mount](../product/mount.md), [mount options](../research/mount-options.md)): that a Swift app can serve a read-only libfuse3 filesystem through macFUSE's FSKit backend. Needs a Mac with macFUSE 5.x installed and its file system extension enabled.

Done when each step has an answer written into the specs it concerns.

- [ ] A minimal read-only "hello world" filesystem in Swift over libfuse3, mounted with `-o backend=fskit`
- [ ] Ad-hoc signed with the hardened runtime: does `disable-library-validation` let it load `libfuse3.dylib`? ([architecture](../product/architecture.md), question 2)
- [ ] Can it mount at `~/pCloud Drive`, or only under `/Volumes`? ([mount](../product/mount.md), question 1)
- [ ] Does it work on macOS 15.4 as well as 26? ([architecture](../product/architecture.md), question 1)
- [ ] Finder browses it, Quick Look previews a file, `cat` reads it, and a write fails cleanly
- [ ] With writes allowed in a test build: Finder can create, rename, save over and delete a file (FSKit's write support is the youngest part of macFUSE; [writes](../product/writes.md) come soon)
- [ ] A read that blocks (simulated slow network) doesn't hang Finder
