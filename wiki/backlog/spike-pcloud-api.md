---
type: task
title: "Spike: the pCloud API calls the app needs"
status: todo
priority: high
assignee:
tags: [spike]
---

Confirm the API behaves as the specs assume. Can run on Linux.

Done when each step has an answer written into [account](../product/account.md), [remote index](../product/remote-index.md) and [writes](../product/writes.md).

- [ ] Register a pCloud OAuth app; run the authorization code flow with a loopback redirect; does it return the region (API host)?
- [ ] Is there a read-only OAuth scope?
- [ ] `listfolder` with `recursive=1` on the real account: time and response size
- [ ] `diff`: the change format, whether it can long-poll, how compaction shows up
- [ ] `getfilelink` then an HTTP `Range` request: does it work, how long the link stays valid
- [ ] `userinfo`: quota fields
- [ ] Uploads: replace a file atomically, resume a large upload, the hash or modification time to detect conflicts ([writes](../product/writes.md))
- [ ] Is there an undocumented name-search method (as the web app may use)? Only for the record: [search](../product/search.md) uses the local index regardless
