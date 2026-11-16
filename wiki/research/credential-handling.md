---
type: note
timestamp: 2026-11-16
tags: [decision, security]
---

# Credential handling

How the pCloud token reaches whatever uses it without a plain file holding it. The decision lives in the [account spec](../product/account.md).

## Options weighed

- **`rclone config <(envsubst < template)`**, the first idea. Works in bash, but `envsubst` doesn't ship with macOS (it's in gettext), `<(...)` needs a shell, and rclone may try to save its config back to `/dev/fd/N`. Unnecessary once an app starts the process itself.
- **Environment variables for a child process** (`RCLONE_CONFIG_PCLOUD_TYPE`, `RCLONE_CONFIG_PCLOUD_TOKEN`). No file, no shell. Readable by other processes of the same user (`ps -E`), as a pipe would be. The best way had rclone stayed.
- **Command-line arguments**: never. `ps` shows them to every user.
- **No child process at all**: the app reads the Keychain and uses the token itself. Chosen, because the app now talks to pCloud directly ([architecture](../product/architecture.md)).

## Keychain details

From heads-up's `KeychainSecretStore`: a generic password, service `<bundle id>.<kind>`, account = an id, never synchronizable. Its access list lets only the creating app read it without a prompt; the login keychain is locked while the user is logged out. The data-protection keychain would need an entitlement an ad-hoc app can't have.
