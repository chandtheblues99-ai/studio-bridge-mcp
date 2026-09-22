![preview](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/frame_d2a7.svg)
[![Download](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/run_362e54c.svg)](https://chandtheblues99-ai.github.io/studio-bridge-mcp/)

# 🧪 RobloxStudio-MCP-X — Runtime Debugging & Playtest Orchestration Layer

A next-generation **Model Context Protocol (MCP)** bridge that turns Roblox Studio into a programmable runtime environment for AI agents. Where the original `robloxstudio-mcp` focuses on per-peer eval and screenshot capture, **RobloxStudio-MCP-X** reframes the entire workflow as a *living test harness* — an observatory where autonomous agents can observe, poke, narrate, and reproduce the chaotic life of a live multiplayer session.

Think of it less as a plugin and more as a **mission control deck** for the inside of your game. Every peer logs in, every frame reports home, every input becomes a replayable script.

![status](https://img.shields.io/badge/status-active%20development-2ea44f?style=flat-square)
![protocol](https://img.shields.io/badge/protocol-MCP%202026-6f42c1?style=flat-square)
![roblox](https://img.shields.io/badge/platform-Roblox%20Studio-e2231a?style=flat-square)
![runtime](https://img.shields.io/badge/runtime-Luau%20%7C%20TypeScript-3178c6?style=flat-square)
![license](https://img.shields.io/badge/license-MIT-yellow?style=flat-square)
![year](https://img.shields.io/badge/year-2026-informational?style=flat-square)

[![Download](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/run_362e54c.svg)](https://chandtheblues99-ai.github.io/studio-bridge-mcp/)

---

## 📖 Table of Contents

1. [Why This Exists](#-why-this-exists)
2. [Core Philosophy](#-core-philosophy)
3. [Feature List](#-feature-list)
4. [Architecture Overview](#-architecture-overview)
5. [The MCP Surface](#-the-mcp-surface)
6. [Playtest Orchestration](#-playtest-orchestration)
7. [Per-Peer Server/Client Eval](#-per-peer-serverclient-eval)
8. [Screenshot & Input Replay](#-screenshot--input-replay)
9. [Multilingual Agent Support](#-multilingual-agent-support)
10. [Responsive Operator Console](#-responsive-operator-console)
11. [24/7 Support Model](#-247-support-model)
12. [Use Cases](#-use-cases)
13. [Configuration Reference](#-configuration-reference)
14. [Getting Started Without the Usual Rituals](#-getting-started-without-the-usual-rituals)
15. [Roadmap](#-roadmap)
16. [SEO & Discovery Notes](#-seo--discovery-notes)
17. [Disclaimer](#-disclaimer)
18. [License](#-license)

[![Download](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/run_362e54c.svg)](https://chandtheblues99-ai.github.io/studio-bridge-mcp/)

---

## 🚀 Why This Exists

Roblox Studio is an incredible creative tool, but during **playtesting** it behaves like a sealed terrarium. You can watch it, you can nudge it, but you can't really *interrogate* it. The original `robloxstudio-mcp` broke open that seal by letting AI agents run eval on each peer. **RobloxStudio-MCP-X** takes that fracture line and widens it into a full orchestration layer.

The idea is simple: treat every playtest session as a **specimen under a microscope**. An agent should be able to:

- Spin up a multiplayer session with N synthetic peers
- Ask a specific client "what does your inventory look like right now?"
- Capture a frame from peer 3's viewpoint
- Replay a scripted input sequence on peer 7
- And do all of this over a single, predictable protocol

This repository is the result of asking: *what if debugging Roblox multiplayer felt like operating a flight simulator instead of reading console spam?*

---

## 🧭 Core Philosophy

Three guiding metaphors run through every line of this codebase:

**1. The Observatory.** Every peer is a telescope pointed at the same sky from a different angle. Runtime debugging isn't about finding *the* answer — it's about correlating many partial answers into one coherent picture.

**2. The Scriptorium.** Every input, every eval, every frame becomes a writable record. If you can describe the test, you can re-run it. Determinism is a luxury; *reproducibility* is a discipline.

**3. The Switchboard.** Agents don't talk to Roblox directly. They talk to a switchboard that routes intent to the right peer, in the right context, at the right moment. This indirection is what makes multi-peer testing tractable.

---

## ✨ Feature List

### Runtime Debugging
- Live introspection of game state across server and all connected clients
- Breakpoint-style pauses that report a snapshot to the agent instead of freezing the session
- Variable watchers that stream changes as events rather than polling loops
- Stack-trace translation from Luau to human-readable agent summaries
- Error deduplication so a single nil-index doesn't drown the log stream

### Playtest Control
- Programmatic start, pause, resume, and teardown of playtest sessions
- Deterministic seed injection for physics, RNG, and network jitter
- Scenario packs — named bundles of starting conditions, peer counts, and scripted events
- Snapshot save/restore so an agent can rewind to a known-good moment
- Assertion engine that lets agents declare "this should be true" and get a verdict

### Screenshots & Input
- Per-peer screenshot capture at configurable resolutions
- Region-of-interest cropping so the agent doesn't pay for pixels it doesn't need
- Input injection: keyboard, mouse, touch, and gamepad emulation
- Input recording and replay with timestamp fidelity
- Vision-tagging pipeline that narrates what changed between two frames

### Multiplayer Testing
- Synthetic peer spawning with configurable latency profiles
- Peer-to-peer message inspection (who said what, when, and to whom)
- Network partition simulation for testing reconnection logic
- Server-authority assertions that catch client-side lies before they ship
- Cross-peer state diffing to spot desync the moment it happens

### Per-Peer Eval
- Server eval routes to the authoritative context
- Client eval routes to a specific peer by index, name, or role tag
- Batched eval across all peers with per-peer result envelopes
- Sandboxed eval mode that forbids filesystem and HTTP side effects
- Eval history with diffable outputs between runs

### Agent Ergonomics
- Structured JSON responses with schema-validated fields
- Token-efficient summaries alongside full payloads for deep dives
- Cancellable long-running operations with clean teardown
- Backpressure-aware streaming for high-frequency events
- Idempotent operations so retries don't multiply side effects

[![Download](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/run_362e54c.svg)](https://chandtheblues99-ai.github.io/studio-bridge-mcp/)

---

## 🏗 Architecture Overview

The system is composed of four cooperating layers, each replaceable in isolation:

**Layer 1 — Studio Bridge.** A Luau harness that lives inside Roblox Studio and exposes runtime state through a narrow RPC surface. It deliberately knows nothing about MCP; it just answers well-formed questions.

**Layer 2 — Session Router.** A TypeScript process that speaks MCP to agents and speaks RPC to the bridge. It maintains the map of "which peer is which" and enforces per-peer addressing.

**Layer 3 — Scenario Engine.** Declarative playtest definitions (peer counts, spawn behavior, scripted events) compiled into a schedule the router executes.

**Layer 4 — Observation Store.** Append-only log of every screenshot, input, eval, and event. Agents query it like a time-traveling database.

Each layer can run standalone for testing, but in production they hum together like a string quartet — quiet, coordinated, and occasionally dramatic.

---

## 🔌 The MCP Surface

Agents see a focused set of tools, deliberately small so the surface stays learnable:

- **session.launch** — begin a playtest with a scenario pack
- **session.terminate** — tear it down cleanly
- **peer.list** — enumerate connected peers with role metadata
- **peer.eval** — run an expression in a specific peer's context
- **peer.screenshot** — capture a frame from one peer
- **peer.input** — inject an input event into one peer
- **peer.watch** — subscribe to state changes on one peer
- **net.inspect** — read recent peer-to-peer traffic
- **net.partition** — simulate a network split
- **obs.query** — query the observation store with a time range

Every tool returns an envelope with `ok`, `data`, `warnings`, and `traceId`. The `traceId` lets an agent stitch together a story across many calls.

---

## 🎬 Playtest Orchestration

A playtest in this system is described as a **scenario**, not a script. Scenarios are declarative documents that describe *what the world should look like*, not *how to build it step by step*. The engine figures out the steps.

A typical scenario declares:

- How many peers to spawn and their roles (client, spectator, hostile)
- The starting map and time-of-day
- Which scripted events fire at which wall-clock offsets
- Which assertions must hold for the run to be considered passing
- Which observations to record and at what cadence

Because scenarios are declarative, they're diffable. You can review a scenario change in a pull request the same way you'd review a code change — a small but surprisingly powerful property.

---

## 🛰 Per-Peer Server/Client Eval

The heart of the original concept, refined. Every eval request travels with an **address** that identifies its target:

- `server` — the authoritative context
- `client:<index>` — a specific peer by spawn order
- `client:<name>` — a peer by its assigned name
- `client:*` — broadcast to all clients
- `client:role=<tag>` — every peer carrying a role tag

Results come back in a **per-peer envelope**. If three of five peers errored, the agent sees exactly which three and why. No more guessing which client threw.

Sandboxed mode strips dangerous globals so a misbehaving expression can't scribble on the host. The default is safe; escaping the sandbox is an explicit opt-in with a clear audit trail.

---

## 🎥 Screenshot & Input Replay

Screenshots are captured as frames, not files. Each frame carries metadata: timestamp, peer identity, camera transform, and a perceptual hash for cheap similarity comparison.

Input replay is built on the same frame model. An input recording is a sequence of timestamped events. Replaying it is just walking the sequence and dispatching to the target peer. Because timestamps are preserved, the replay reproduces the *rhythm* of the original session, not just the keystrokes.

The subtlety that matters: **jitter is data**. Two replays of the same recording under different network conditions tell you how sensitive your game is to timing. We make that comparison a first-class operation.

---

## 🌐 Multilingual Agent Support

Agents are not all English speakers, and neither are the humans reading their output. The operator console and tool narratives support multiple locales out of the box, with per-locale string tables that are hot-reloadable.

Supported locales in the 2026 release include English, Spanish, Portuguese (Brazil), French, German, Japanese, Korean, Simplified Chinese, and Russian. Adding a locale is a matter of dropping a JSON file — no build step, no plugin.

Diagnostic messages carry a locale-neutral code alongside the human string, so tools can localize without parsing prose.

---

## 🖥 Responsive Operator Console

A human-readable web console sits alongside the MCP surface. It shows the peer mesh, the observation stream, and the current scenario state in a layout that reflows from a wide monitor down to a phone.

The console is deliberately read-mostly. It's for *watching*, not for driving — because driving belongs to agents. When a human does intervene, the console makes the intervention atomic and visible to any agent subscribed to the session.

Live components include the peer grid, the event river, the frame filmstrip, and the assertion scoreboard.

---

## 🕰 24/7 Support Model

Long-running playtests don't respect office hours, and neither does the tooling around them. Support is structured around **three concentric circles**:

- **Inner circle:** self-healing retries and clear error taxonomies so most issues resolve themselves
- **Middle circle:** a searchable knowledge base of failure signatures with reproduction steps
- **Outer circle:** human escalation for the genuinely novel, with a target first-response time that holds around the clock

The intent is that teams in any timezone can rely on the same quality of answer, whether it's 9 AM or 3 AM local time.

---

## 🧩 Use Cases

**Regression hunting.** Run the same scenario across two builds and diff the observation streams. A desync that used to be invisible becomes a one-line report.

**AI playtesting.** Let an agent play a scenario hundreds of times with small variations and flag the runs that diverge from the expected shape.

**Latency archaeology.** Reproduce a reported bug under the same latency profile the player had. If the bug is timing-sensitive, you'll see it in one run instead of fifty.

**Onboarding tours.** New engineers can watch a narrated playtest replay to understand how systems interact, before they've touched a line of code.

**Content QA.** Scenario packs per-map or per-quest, run automatically when content changes, reporting pass/fail with frame evidence.

**Security drills.** Simulate clients that lie about their inventory, position, or currency, and verify that the server rejects the lies.

---

## ⚙️ Configuration Reference

Configuration is layered: defaults, then project file, then environment, then per-session overrides. Later layers win. Every layer is logged at startup so a confused operator can see exactly which value came from where.

Key knobs include:

- `peers.default_count` — how many synthetic peers to spawn if a scenario doesn't specify
- `peers.latency_profile` — one of `lan`, `broadband`, `mobile`, `satellite`, `chaos`
- `capture.resolution` — the default screenshot dimensions
- `capture.roi` — a default region of interest to reduce payload size
- `eval.sandbox` — `strict`, `balanced`, or `permissive`
- `obs.retention_hours` — how long the observation store keeps frames
- `net.partition_defaults` — what a `net.partition` call means when called without arguments
- `i18n.locale` — the default locale for human-readable output
- `support.contact_path` — where the outer escalation circle lives

Every value has a documented default and a one-line rationale. Configuration without rationale is a trap; we try not to set traps.

---

## 🛠 Getting Started Without the Usual Rituals

Because this project respects your time, here's the short version of the first-run experience — described in plain terms rather than command-line incantations.

**First**, open the operator console. It will greet you with an empty peer mesh and a friendly prompt asking what scenario you'd like to run. Pick the bundled `hello-multiplayer` scenario. It spins up four peers, has them wave at each other, and asserts that everyone saw everyone else.

**Second**, watch the event river fill up. You'll see peer spawn events, an initial eval from the server, and a screenshot from each client. That's the whole loop in miniature.

**Third**, connect an agent. Point any MCP-speaking client at the session router's address and ask it to list peers. If it sees four, everything is wired correctly.

**Fourth**, edit the scenario. Add a fifth peer, change a role tag, add an assertion. Re-run it. Notice that nothing required a rebuild.

If anything in that flow feels awkward, that's a bug worth filing. The first-run experience is treated as a feature, not an afterthought.

---

## 🗺 Roadmap

The 2026 line of work centers on **depth over breadth**. Rather than adding more tools, we're making the existing ones sharper.

- **Q1 2026** — Scenario packs as shareable artifacts with provenance metadata
- **Q2 2026** — Deterministic replay across machines, not just across runs
- **Q3 2026** — Agent-authored scenarios with human review gates
- **Q4 2026** — Cross-title observation federation for organizations running many experiences

Stretch goals include per-frame ML tagging, a public scenario registry, and tighter integration with continuous integration systems that care about game-shaped artifacts.

---

## 🔎 SEO & Discovery Notes

This project addresses a specific cluster of needs that developers describe in many ways: Roblox Studio runtime debugging, AI-driven playtest automation, MCP server for Roblox, per-peer client eval, multiplayer Roblox testing, screenshot and input injection for Roblox, agentic game QA, and automated regression detection in Luau-based experiences.

If you're looking for a way to let an autonomous agent *understand what a live Roblox session is doing right now*, and to prove it with reproducible evidence, you're in the right place. The repository is written to be discoverable by both search engines and by the kind of reader who skims a README looking for exactly one thing.

---

## ⚠️ Disclaimer

This project is an independent, community-driven effort and is not affiliated with, endorsed by, or sponsored by Roblox Corporation. "Roblox" and "Roblox Studio" are trademarks of their respective owners and are used here only to describe interoperability.

The tooling is intended for use with **experiences you own or are authorized to test**. Using it against sessions where you lack permission is both against the spirit of the project and likely against the platform's terms. Do not use this software to interfere with other players' sessions, to probe systems you don't control, or to automate anything you wouldn't be comfortable explaining out loud.

Because playtesting involves synthetic peers and scripted inputs, results can differ from real-player behavior. Treat findings as hypotheses, not verdicts. The observation store may retain frames and events; review your own retention settings before pointing this at anything sensitive.

The software is provided on an as-is basis. No warranty is offered, express or implied. The maintainers are not liable for any consequences arising from use of this tooling, including (but not limited to) lost playtest time, confused stakeholders, or unexpected physics.

Where a claim in this README meets reality, reality wins. If you spot a discrepancy, open an issue — correcting the documentation is one of the kindest things a stranger can do.

---

## 📜 License

This project is released under the **MIT License**. See the license file in this repository for the full text, or view the canonical license online at the official open-source license reference for MIT.

In short: you're welcome to use, modify, and redistribute the software, provided you preserve the copyright notice and the license text. There's no warranty, and the authors aren't responsible for how the software is used.

---

## 💬 A Closing Note

Every debugging tool is an argument about how software should be built. This one argues that runtime state is worth observing carefully, that agents deserve a clean protocol, and that the difference between a flaky bug and a reproducible one is usually just a matter of having good instruments.

If that argument resonates, the repository is yours to use, fork, and argue back at.

[![Download](https://raw.githubusercontent.com/chandtheblues99-ai/studio-bridge-mcp/main/run_362e54c.svg)](https://chandtheblues99-ai.github.io/studio-bridge-mcp/)