---
title: "v0.5.0 | Tactile Feedback"
date: 2026-01-04
tags:
  - HalfEngine
type: article
---

HalfEngine v0.5.0 is out with a bunch of improvements, bug fixes and QoL
features<!--more-->, including:

- Configuration file support. The files have an INI-like format and have the
  `.cfg` extension. They are stored under `Engine/Config/`
- Essential networking parameters have been exposed in the configuration files.
  Addresses and ports can now be customized
- Improved the Agent list window layout
- Implemented a property inspector that displays the selected Agent's
  properties
- Selected agents are now highlighted in the 3D viewport
- Fixed a bug where ImGui windows would not block mouse input

**What's next?** The upcoming update will include the first iteration of the
persistence system that enables the server to resume the simulation after
shutting down.
