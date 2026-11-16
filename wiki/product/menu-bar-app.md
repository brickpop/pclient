---
type: spec
status: planned
---

# Menu bar app

pClient is a menu bar-only app (no Dock icon), styled like heads-up: system font, vibrancy, accent color, light and dark mode, VoiceOver. See [prior art](../research/prior-art.md) for what to reuse from heads-up.

## Behaviour

- **Status item** shows the state at a glance: mounted, syncing, signed out, error.
- **Panel** (opened from the status item): [search](search.md) at the top; below it the status and stats; actions.
- **Stats**: quota used and total (from the API's `userinfo`), last index sync, block cache size, reads in progress.
- **Actions**: open the mount in Finder, resync the index, clear the cache, unmount or mount, sign out.
- **Settings**: mount point, cache size limit, ignore patterns, open at login, account.
- **Open at login** through `SMAppService.mainApp`, as heads-up's `LoginItemAdapter` does: the app itself is the login item, with no helper.
- **First run** walks through: install macFUSE if missing, enable its file system extension, sign in to pCloud, mount.
- **Settings** are a JSON file in `~/Library/Application Support/pClient/`, editable by hand; the token is never in it ([account](account.md)).
- **No global hotkey** for now: the panel opens from the status item. A hotkey for search may come later.
- **Name**: pClient. **Bundle id**: `org.brickpop.pclient`, which must never change: the Keychain item is stored under it.
