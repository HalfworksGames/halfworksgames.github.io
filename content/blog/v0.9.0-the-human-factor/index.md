---
title: "v0.9.0 | The Human Factor"
date: 2026-02-16
tags:
  - HalfEngine
type: article
---

Up until now, HalfEngine has been about infrastructure. Simulation loops.
Serialization and persistence. Structured logging. Protocol negotiation.
Commands, replication, and validation.

The world existed. Agents moved. Clients could observe the state of the
simulation. But there was still something missing...
<!--more-->
Update v0.9.0 introduces the first iteration of real-time, embodied interaction
with the simulation. It is intentionally minimal and focused on correctness
over polish, but it represents a major architectural milestone: HalfEngine is
no longer just observable. It is inhabitable… temporarily.

## New features and changes:

* **Server→Client Events**: The commands system now supports events flowing in
  both directions. This change lays the groundwork for richer interaction
  without introducing special-case networking paths.
* **Session Tracking**: A new session system tracks active connections and
  their associated agents. Each incoming connection is assigned a session.
  Sessions are runtime constructs responsible for:
  * Mapping connections to agents
  * Validating command authority
  * Managing possession state

  Sessions are ephemeral by design. Identity persistence will arrive in a
  future milestone. For now, they ensure clean separation between connection
  handles and simulation entities. This means that network transport, identity,
  and simulation entities remain decoupled systems rather than collapsing into
  a single implicit "player object".
* **Agent Possession**: Clients can now possess agents within the simulation.
  Possession is handled entirely server-side:
  * The client requests possession.
  * The server validates the session.
  * An agent is spawned (if necessary).
  * The session binds to the agent.
  * The server notifies the client of the assigned AgentID.

  All authority remains on the server. The client never directly mutates agent
  state. Movement and orientation are applied through validated commands,
  preserving architectural consistency.
* **Placeholder Agents Removed**: The simulation no longer auto-spawns agents
  at startup.

  Previously, agents were spawned automatically to populate the world. This
  behavior has been removed in favor of explicit spawning through possession
  requests. Agents now exist because something (a client, in this case)
  requested them.

  This keeps the simulation semantically cleaner and avoids artificially
  inflating the world state during testing.
* **First-person Character Controller**: A minimal first-person controller has
  been implemented for testing. The controller is intentionally bare-bones. Its
  purpose is not polish, but validation of the full input → command → server
  mutation → replication pipeline. Despite its simplicity, this is the first
  time the simulation has felt alive from within rather than observed from
  outside.
* **View Cones**: Agents now render simple view cones to visualize orientation.

  This is purely a visualization aid. It is not currently tied to AI perception
  or gameplay logic. However, it provides immediate visual confirmation that
  orientation replication and server-side mutation are functioning correctly.

## Fixes and Improvements

* **Serializer Forward Compatibility Fix**: A bug in the serializer prevented
  proper field skipping when encountering unknown fields. The size calculation
  was incorrect, which could cause stream desynchronization. This has been
  fixed.

  Unknown fields are now correctly skipped, making the serializer forward- and
  backward-compatible in trivial scenarios. While the unified serializer is
  still limited to snapshot usage for now, this fix strengthens long-term
  compatibility guarantees.
* **Improved Command Validation**: Command validation paths have been hardened
  to handle exceptional cases such as:
  * Sessions without bound agents are rejected cleanly
  * Invalid possession attempts are logged
  * Missing agents are handled safely
  * Handler constraints are enforced more strictly

  This ensures that even malformed or unexpected input cannot corrupt
  simulation state.
* **Replication and Signal Handling Fixes**:
  * Fixed minor replication edge cases involving agent removal
  * General cleanup and stability improvements

## Controls

* **Spacebar**: possess / unpossess an agent
* **Mouse**: Look around
* **WASD** or **arrow keys**: for movement
* **Left Alt+C**: Toggle cursor visibility. Useful to constrain the cursor to
  the window when looking around

## Breaking Changes!

* The replicated snapshot has more data in it now, and since the snapshot is
  still deserialized through the old pipeline, old versions (up to v0.8) will
  not be able to deserialize it. The protocol version has been bumped to
  prevent incompatible versions from interacting.

## What's Next?

The next phase will focus on identity persistence, authentication, and durable
ownership — transitioning from temporary embodiment to persistent actors within
a long-running world.
