---
title: "v0.11.0 | Distant Echoes"
date: 2026-06-12
tags:
  - HalfEngine
type: article
---

After a brief hiatus to attend to personal matters, development on HalfEngine
has resumed, touching the backend, SDK and tooling. HalfEngine 0.11.0
introduces a few user-facing features and a handful of experiments that will
shape future direction.
<!--more-->
## Changes

* Implemented support for conventional paths.
  * `%USERPROFILE%\AppData` on Windows and XDG spec compliant on Linux.
* Agent state is now separated per agent.
* The server now tracks and isolates specific *Runs.*

  > A **Run** is a session or playthrough of the simulation. A **timeline** of
  > events.
* Linux builds are now produced on a Rocky 9 build machine targeting glibc
  2.35. The builds should work on any reasonably recent Linux distribution.
* Fixed a few minor bugs server-side.

## Experiments & R&D

An engine without the ability to talk to the outside world and work with
third-party tools is of little use, more so a headless backend. I have spent
some time experimenting with bridging third-party content creation pipelines
and tools with the engine.

The goal is twofold:

* **Content Creation:** This is done offline by the developer(s). The goal is
  to establish a pipeline that enables the user to use Blender and other
  third-party DCC tools to build maps and seamlessly import them into
  HalfEngine with minimal friction. This has the benefit of the user being able
  to use tools they're already familiar with and saves us development time by
  not having to build and support a full-featured level editor from scratch.
* **Runtime Integration**: This is done online while the engine is running. The
  goal is to enable third-party engines and tools to talk to the HalfEngine
  runtime through a standardized protocol via a custom SDK that exposes the
  functionality to the third-party tools.

### Content Creation

The current plan of achieving the first goal is by establishing a scene
specification which details how a scene must be laid out and what objects may
exist within. Next, an addon for Blender will be built that provides tools for
the content creator to build the scene. Finally, an offline tool that
preprocesses the scene exported from Blender will be built that converts the
export into a format suitable for runtime consumption. This solves the backend
problem.

The frontend will need to consume the same data too. Otherwise the two sides
would not be able to agree on what the world looks like. That can be achieved
by implementing a custom import step in Unreal Editor that processes the
custom, HalfEngine specific data and converts them into a representation that
Unreal understands before the Unreal importer throws them away. The data
includes things such as entity IDs which are used to address objects across the
frontend and backend.

### Runtime Integration

This means that one can build programs and tools that can talk to HalfEngine at
runtime. Examples of such tools are the current implementation of the Client
and Debugger.

This can be done by implementing an SDK that exposes the relevant functionality
as a library that can be used by a third-party. A simple version of this has
already been tested with Unreal, in the form of a plugin that exposes the SDK
functionality to Unreal Engine, with good success. Unreal was able to connect
to the backend and register an identity successfully.

![](https://img.itch.zone/aW1nLzI4OTM4NTkwLnBuZw==/original/vYQ7wR.png)

This has the added benefit of saving us months of development effort building a
rendering engine, animation system, UI, VFX, etc. in favor of Unreal's
world-class tooling, so we can focus on what matters: **the simulation**; and
let Unreal make it pretty. Though the SDK is engine-agnostic and can be as
easily integrated into other third-party engines.

> Note that the tools and SDK are still experiments and are not included in
> this release.

## What's Next?

Next up, the server's core loop will be refactored into a reactive,
event-driven architecture that ensures deterministic handling of the simulation
state. Work will also continue on tooling and third-party integration.
