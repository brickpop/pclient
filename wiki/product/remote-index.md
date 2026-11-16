---
type: spec
status: planned
---

# Remote index

A local copy of the metadata of every file and folder in the pCloud account: id, parent, name, size, modification time, file or folder. No contents. It feeds both the [mount](mount.md) (listings and file attributes come from here, not from a network call per Finder request) and [search](search.md).

## Behaviour

- **First sync**: one `listfolder` call with `recursive=1` from the root returns the whole tree.
- **Updates**: the `diff` API method returns only the changes since a `diffid`. The app polls it on a timer and when the Mac wakes or the network returns, and applies the changes. pCloud's docs say to start over from scratch when its change history has been compacted, so a full resync is a normal path, not an error.
- **Storage**: SQLite in `~/Library/Application Support/pClient/`, with a full-text index on names. Removed on sign out.
- **Freshness** is shown in the [menu bar app](menu-bar-app.md): last sync time, or the error.

## Open questions

1. **Scale.** How long a recursive `listfolder` takes, and how big its response is, on a large account. Measured in the [API spike](../backlog/spike-pcloud-api.md).
2. **Poll interval.** Whether `diff` can block until something changes (long poll) or must be polled. Recommendation: whatever the spike shows; poll every minute otherwise.
