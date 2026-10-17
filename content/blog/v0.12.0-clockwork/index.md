---
title: "v0.12.0 | Clockwork"
date: 2026-07-11
tags:
  - HalfEngine
type: article
---

HalfEngine 0.12.0 is here, finalizing the core architecture phase. This update
introduces a reactive event-driven backend, robust clock synchronization,
reliable command channels, enhanced debugger, and more!
<!--more-->
## Features

### Reactive Backend

The Host has been refactored into an event-driven architecture. Instead of
dispatching Commands immediately upon receipt, the Host now queues them during
the tick cycle, sorts them deterministically, and dispatches them in order to
their respective handlers. This has a few benefits:

* Handlers are dispatched in a predictable order, ensuring that given the same
  inputs, the end result will always be the same
* Saves processing time. The Host does not need to keep polling each subsystem
  every tick. Instead, a subsystem is only triggered if an event it is
  interested in is received
* Subsystems are fully self-contained and decoupled from the Host's main loop.
  Changing a subsystem will not have any effect on surrounding, unrelated
  subsystems; making the system as a whole easier to maintain and reason about

The work done here also revealed an "emergent feature" that was not planned
initially. Because the Subsystems are designed as stateless logic processors
(they do not maintain internal simulation state), they can be offloaded into
shared objects (DLL/SO binaries) and loaded at runtime; enabling a runtime,
with potential hot-reloading capability, plugin system! However, this feature
is not required now and will not be implemented because there is more important
work that needs to be done.

### Clock Synchronization

A clock synchronization and prediction system has been implemented. The Client
does an NTP-style handshake to estimate the Host's clock offset and RTT. This
enables the Client to map its own local clock to the Host's clock, which
simplifies the implementation of many systems including server-side lag
compensation.

### Command Transport Reliability

Commands are now split into *reliable* and *unreliable* channels. Reliable
commands are critical commands that must be delivered (such as a *Login*
command) and will be retransmitted when they fail to go through (TCP-style
reliability). Unreliable commands are non-critical commands that can be lost
without causing problems, like movement, which is sent frequently and
subsequent commands will correct the state even if a previous command was lost.

This improves network throughput and client responsiveness. A failed Move
command will not block other commands while its being retransmitted
(Head-of-Line blocking).

### Network Monitoring and Simulation Tools

The debugger has been upgraded with additional tools to monitor and simulate
network conditions.

The *Statistics* window displays the network statistics in graphs that show the
current and historical parameters, along with a moving average of the
respective paramter.

![](https://img.itch.zone/aW1nLzI4NDI3MTAzLmpwZw==/original/TWAKUo.jpg)

The *Simulator* window allows the user to apply network parameters server-side
to simulate various network conditions for testing.

![](https://img.itch.zone/aW1nLzI1MjYwMTQxLnBuZw==/original/uXJJZZ.png)

### Fixes and Improvements

* Fix Agent movement not scaling with the timestep. Scaled time now affects
  Agent movement speed
* The RNG is now initialized with a high-entropy seed from a cryptographically
  secure random source, making its output sequence less predictable for a given
  Run.
* The Client and Debugger codepaths have been unified server-side. There is now
  a single pipeline that handles both of them, reducing code duplication
* Due to the refactor above, a potential future vulnerability surfaced and was
  fixed: Commands have flags/attributes, and those flags used to be set by the
  client. This would allow a client to run commands that might require elevated
  privileges, due to both privileged and normal commands being consumed through
  the same pipeline. The flags from incoming commands are no longer considered
  server-side. Instead, the server checks the flags internally, and
  unauthorized commands are dropped
* Various data layout and allocation pattern optimizations
* Improved error handling and logs output

## What's Next?

The next phase of development will move away from engine plumbing and into the
content and production phase. The work will now focus on getting external
content into the simulation, starting with static geometry, and later moving to
dynamic and interactable elements. From now on, the core engine will only
receive updates as required by gameplay systems, not in isolation.
