---
title: "v0.8.0 | Command and Control"
date: 2026-02-08
tags:
  - HalfEngine
type: article
---

HalfEngine v0.8.0 is here, marking the largest milestone released so far!

This update is another one of those 'mostly under-the-hood' releases, but a
crucial one at that. It introduces protocol negotiation, server-side commands,
and a new debugging tool — systems that fundamentally change how clients,
tools, and even the engine itself interact with the simulation.
<!--more-->
With these systems in place, HalfEngine now has a clear separation between
observation, tooling, and authority. While much of this work isn't immediately
visible, it lays the foundation for real interactivity, automation, and future
player-driven features.

## New Features and Bug Fixes

### Networking & Protocol

* Implemented protocol negotiation. Clients and servers now exchange version
  and capability information before any data is transferred.
  * Unknown or incompatible clients are logged and disconnected
  * Clients that fail to complete the handshake within a set timeout are
    disconnected
* Established the first version of the networking protocol. It's experimental
  and expected to evolve
* Refactored the networking subsystem to support multiple simultaneous listen
  sockets

### Server-side Commands & Tooling

* Implemented server-side commands, enabling remote clients to interact with
  the server in controlled, predefined ways
* Introduced a **Debugger** executable with elevated access to the server. The
  regular **Client** executable no longer has elevated access. In the future,
  it will also stop receiving full world snapshots (it still does currently).
* The Debugger connects through a dedicated, privileged socket
* The Debugger and Client windows are resizable now!
* ImGui window docking has been enabled to make it easier to organize the
  window layout
* Added simulation timescale controls (pause, slow-mo, fast-forward), available
  through the debugger

  > Debug access is disabled by default for security reasons and can be enabled
  > via `Engine/Config/Debug.cfg`

This system also lays the groundwork for some additional features not present
currently, including:

* Scriptable servers and server automation
* Remote tooling (the debugger being the first example)
* Deterministic replays with control injection ("steerable replays")
* Limited modding capabilities

### Engine Infrastructure

* Upgraded the serializer to support missing fields during deserialization.
  Missing data is now default-initialized to reasonable values. This makes it
  easier to maintain backwards compatibility in the future
* Introduced a lightweight, deterministic PRNG system based on
  [Xoshiro](https://github.com/Reputeless/Xoshiro-cpp/), replacing the standard
  library PRNGs for improved determinism, control and performance
* Minor internal refactoring to remove duplicated code. You might notice a new
  "System" channel in addition to "Client" and "Server" in the logs

### Bug Fixes

* Fixed a serializer bug that made it impossible to handle modified fields
* Fixed a critical bug where the credits window could not be closed by simply
  clicking the "Credits" button on the toolbar

## Breaking Changes!

* Snapshot format changes:
  * The serializer was refactored to improve long-term compatibility; as a
    result, snapshots created by v0.8+ will not be compatible with older engine
    versions.
  * Snapshots created by older versions *can* be loaded by v0.8 (the changes
    are backwards compatible)
* Network protocol changes:
  * Older engine versions can no longer communicate with the current and future
    versions
  * Cross-version behavior:
    * New client → old server: the connection will be dropped by the client due
      to invalid handshake
    * Old client → new server: the server times out waiting for the handshake

## What's Next?

The next release will build directly on the new commands system, introducing
the first forms of direct, real-time interaction with the simulation — moving
beyond observation and tooling.
