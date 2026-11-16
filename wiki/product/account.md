---
type: spec
status: planned
---

# Account and credentials

Signing in to pCloud and keeping the credential safe. See [credential handling](../research/credential-handling.md) for the options weighed.

## Behaviour

- pCloud has no API keys. Access is an **OAuth access token**, which does not expire, so nothing ever refreshes or writes it back.
- The token lives **only in the login Keychain**: a generic password item under the app's bundle id, never synchronizable, readable without a prompt only by the app that created it. Same approach as heads-up's `KeychainSecretStore`.
- **No file ever holds the token**: no config file, no `envsubst` template, no command-line argument. It stays in the app's own memory, because the app is the only process that uses it ([architecture](architecture.md)).
- **Region**: pCloud accounts live in the US (`api.pcloud.com`) or the EU (`eapi.pcloud.com`). The API host is stored next to the token (not secret) and every call goes there.
- **Sign out** removes the Keychain item, unmounts and clears the [remote index](remote-index.md).

## Open questions

1. **How to obtain the token without rclone.** Options: (a) register our own pCloud OAuth app (free, at pCloud's developer site) and run the authorization code flow with a loopback redirect, which also returns the region; (b) log in with email and password through the API, which gets complicated with two-factor authentication; (c) borrow rclone's client id, which isn't ours to use. Recommendation: (a). The [API spike](../backlog/spike-pcloud-api.md) confirms the flow.
2. **Read-only token.** Whether pCloud OAuth offers a read-only scope is unknown. Without one, read-only is enforced by the app, not the server.
3. **Ad-hoc signing and the Keychain.** Each ad-hoc build is a new app to macOS, so it asks for the login password the first time it reads the token after an update. Same as heads-up; goes away with a stable signing identity.
