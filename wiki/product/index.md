# Product

**pClient** (bundle id `org.brickpop.pclient`): a menu bar app for macOS that replaces the official pCloud client, which hangs and logs out. It mounts pCloud as a folder in Finder, read-only in its first version with writes following soon, and searches it by file name.

- [Architecture](architecture.md): how the pieces fit, what the app depends on, and what it never exposes.
- [Account and credentials](account.md): signing in to pCloud and keeping the token in the Keychain.
- [Remote index](remote-index.md): the local copy of pCloud's file tree, shared by the mount and search.
- [Mount](mount.md): pCloud as a read-only folder in Finder, through macFUSE.
- [Writes](writes.md): creating, changing and deleting files through the mount.
- [Search](search.md): finding files by name, instantly and offline.
- [Menu bar app](menu-bar-app.md): the UI, stats, settings and starting at login.
- [Technical debt](technical-debt.md): accepted workarounds and limitations, and how to fix each.
