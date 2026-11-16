---
type: spec
status: planned
---

# Search

Find any file or folder by name, instantly and offline.

## Behaviour

- Searches the full-text index in the [remote index](remote-index.md): no network call per search. pCloud's API has no name-search method that we found, and the local index is faster anyway.
- Results show name, folder and size; choosing one reveals it in Finder at its path under the [mount](mount.md), or opens it.
- Lives in the [menu bar app](menu-bar-app.md)'s panel, keyboard-first: type, arrows, return.
- Spotlight is not used: it usually doesn't index FUSE mounts, and when it does it reads every file, which downloads them.

## Open questions

1. **Matching.** Substring, word prefix, or fuzzy? Recommendation: word prefix with SQLite FTS5, which is fast and predictable; add fuzzy only if it's missed.
