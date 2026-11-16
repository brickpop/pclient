---
type: task
title: Scaffold the Swift package and build tools from heads-up's patterns
status: backlog
priority: medium
assignee:
tags: [setup]
---

Set up the repo as described in [architecture](../product/architecture.md) and [prior art](../research/prior-art.md): `Core` and `App` modules, `justfile`, `tools/make-app.sh`, `tools/setup-swift.sh`, `tools/lint.sh`, Info.plist and entitlements, CI. Name: pClient, bundle id `org.brickpop.pclient` ([menu bar app](../product/menu-bar-app.md)).

- [ ] Package, justfile and tools, adapted from heads-up
- [ ] `just test` and `just lint` pass on Linux; `just app` builds on the Mac
