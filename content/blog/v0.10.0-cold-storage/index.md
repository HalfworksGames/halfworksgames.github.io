---
title: "v0.10.0 | Cold Storage"
date: 2026-03-09
tags:
  - HalfEngine
type: article
---

HalfEngine v0.10.0 is online, bringing one more core pillar to the engine:
**identity persistence**. For the first time, players can register accounts and
authenticate with the server, transitioning from ephemeral simulation observers
to persistent actors within the world.
<!--more-->
This release introduces a complete authentication system backed by SQLite for
persistent storage, secure password hashing via OpenSSL, and session management
bound to durable user identities. The foundation laid here will enable
long-running simulations where players retain ownership of their Agents across
server restarts.

## New Features and Changes

### Persistence & Security

* **SQLite Integration**: HalfEngine now integrates SQLite as its persistent
  storage backend for all deployment-wide data. This includes identities, user
  credentials and auth tokens, along with other miscellaneous metadata.

  > Token-based authentication is currently not supported. The schema is
  > implemented to make it easy to add support for it in a future release.
* **OpenSSL Integration**: OpenSSL has been integrated into the engine as the
  cryptographic backend. This provides industry-standard password hashing and
  verification, cryptographically secure RNG for salts and tokens, and
  cross-platform consistency.
* **Argon2id Password Hashing**: Following security best practices, passwords
  are hashed using Argon2id, the current state-of-the-art password hashing and
  key-derivation function.
* **Persistent Identities**: Sessions are now bound to persistent user IDs upon
  successful authentication. This is a fundamental shift from v0.9.0's
  ephemeral sessions, which were tied only to connection handles without
  long-term identity.

### Authentication

* **Authentication System**: The complete authentication pipeline has been
  implemented in the Server. The Client can no longer interact with the Server
  without a valid, authenticated Session. Unauthenticated interaction may be
  reintroduced in the future for non-critical functions (e.g. spectators).
* **RegisterUser Command**: The command allows clients to request account
  creation from the server. The Client is automatically authenticated and bound
  to their new ID upon successful registration.
* **AuthenticateUser Command**: The command allows clients to authenticate as
  an existing user. Only one client may be tied to any given ID at once.
  Multiple clients logging into the same account at the same time are not
  allowed.

## Breaking Changes!

This version of the engine cannot interact with older versions because they
lack the authentication system. The older clients are incapable of
authenticating against the new server, and the older servers are incapable of
processing registration and authentication requests.

## What's Next?

The next milestone will focus on integrating the persistent identities with the
world snapshots — bridging the gap between *identity* and *world state*.

The state snapshots are currently monolithic, meaning the entire world-state is
written to a single file. This is problematic for a few reasons; the snapshot
cannot be partially updated, meaning if a single value changes, the whole
snapshot must be rewritten. This also means the entire state must be kept in
memory at all times. When a client disconnects and their Agent goes offline, it
cannot be persisted to disk and unloaded from memory.

Per-identity state persistence cannot be reliably implemented in the current
snapshot format, so the next step that bridges the gap between the runtime
state and the persistent storage is identity-specific snapshots. Each
*identity* gets its own snapshot physically separate from the rest. This
enables a few features, including:

* On-demand loading and unloading of state. When a client disconnects, its
  state can be persisted to disk and unloaded from memory.
* World state deserialization can be parallelized. This means multiple state
  files can be loaded in parallel. This is impossible in the current format.
* Better corruption resilience. A single broken state will not corrupt the
  entire snapshot.

This will be the last major system which completes the engine infrastructure
phase. What work remains will focus on polishing and hardening the existing
systems and preparing the engine for real-world use.
