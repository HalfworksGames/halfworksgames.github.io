---
title: "v0.1.0 | Systems Online"
date: 2025-12-19
tags:
  - HalfEngine
type: article
---

Most game worlds only come to life when a player is nearby; they are
essentially barren or "frozen" when no one is looking. HalfEngine is a custom
simulation engine designed to power persistent, autonomous sandboxes.

<!--more-->

The goal is a world where the simulation runs continuously on a
server, regardless of player presence. Here, NPCs (Agents) are driven by their
own goals, needs, and desires, rather than just waiting for a player to trigger
a script.

Achieving this at scale is a significant technical challenge involving complex
networking, synchronization, and persistence. I previously spent several months
developing a private prototype to validate these concepts. That prototype
proved the feasibility of using GOAP (Goal-Oriented Action Planning) and
persistent server-side states.

However, I learned (the hard way) that building in a vacuum is a recipe for
burnout. To turn this vision into something real, I am restarting the project
with a new philosophy: Build in Public. Version Aggressively. Document
Everything.

Today's release marks v0.1.0, the "Systems Online" milestone. It is
intentionally minimal, acting as the foundational bedrock for everything to
follow and checks the foundational checkboxes:

* Decoupled Architecture: Strict separation between the simulation (Server) and
  the visualization (Client).
* The Handshake: Successful UDP-based connection using GameNetworkingSockets.
* Cross-Platform Baseline: Verified builds for both Windows and Linux.

**What's Next?** This build is a tech demo meant to verify the pipeline. It
isn't a "game" yet, but it is a working foundation. The immediate roadmap for
the upcoming versions includes:

* ImGui integration for real-time server debugging and introspection.
* Implementation of the simulation loop and basic agent spawning.
* Persistent world state (saving and restoring the world across server
  restarts).
* Configuration file support for easy customization of the engine.
