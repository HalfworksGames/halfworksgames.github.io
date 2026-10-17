---
title: "v0.6.0 | Hard Reset"
date: 2026-01-22
tags:
  - HalfEngine
type: article
---

HalfEngine v0.6.0 is here with one of the most important features in a
simulation engine: the ability to save state and resume it later.

This update introduces the first iteration of the unified serialization and
persistence system that will form the backbone of the engine.

<!--more-->

Current functionality:

* The full simulation state is serialized to disk at fixed time intervals
* The server resumes from the last saved state on startup, if available
* Save files are versioned and validated before loading
* Save files are portable across platforms. Windows saves work on Linux and
  vice versa

This system also lays the foundation for several core features such as:

* Crash recovery without losing world state
* Persistent player accounts
* Replays and offline inspection of simulation state
* Cross-version save compatibility through data migration where applicable

This is a low-visibility but critical milestone for the engine. Getting this
right early ensures future features can be built on a stable, long-term
foundation.

Currently, this system is only used for on-disk world saves while it is being
tested "in the wild". The format is designed with streaming in mind, and the
networking / replication layer will be migrated to use the same pipeline once
it stabilizes.

**What's next?** The next update will focus on logging, tracing, and
introspection — adding proper diagnostics to make debugging and future user
reports much more reliable.
