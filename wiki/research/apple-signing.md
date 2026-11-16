---
type: note
timestamp: 2026-11-16
tags: [signing]
---

# Apple signing and entitlements

What we can build without a paid Apple Developer account.

## Why Apple's frameworks need a paid account

The APIs for FSKit modules and File Provider extensions are public, and the code compiles. What stops it running is signing: both are app extensions that need **restricted entitlements** (the FSKit module entitlement, App Groups). macOS honours those only when a provisioning profile issued to a developer team backs them. An ad-hoc signed app has no team, so macOS refuses to load the extension. A free Apple ID's "Personal Team" in Xcode can make development profiles, which might run on one's own Mac only; untested, and it ties the build to Xcode.

## How macFUSE gets us FSKit anyway

macFUSE's author holds a paid account and ships its FSKit extension signed, entitled and notarized. Our app is a client of that extension through libfuse3, so it needs no restricted entitlement itself: we use macFUSE's signature. The cost: we get only what FUSE can say and macFUSE passes through, and we depend on one maintainer's closed-source product ([dependency maturity](dependency-maturity.md)).

## What our app does need

- **Hardened runtime**, ad-hoc signed, as heads-up.
- **`com.apple.security.cs.disable-library-validation`**: the hardened runtime refuses libraries signed by another team, and `libfuse3.dylib` is signed by macFUSE's author. Not restricted, so ad-hoc signing can carry it. It weakens protection against injected code a little ([architecture](../product/architecture.md), open question 2).
- **Releases** are not notarized while there is no paid account: users approve the app once per version in System Settings › Privacy & Security, as with heads-up.
