# OpenConstruct + VaaS: Agent Onboarding as Station Assignment

> *When a new agent comes aboard, it doesn't arrive as a process. It arrives as a station on the bridge — with a console, a garden, and a budget. This document specifies how OpenConstruct provisions the agent and how VaaS assigns the station.*

---

## The Two Systems

[OpenConstruct](https://github.com/SuperInstance/OpenConstruct) is the **hardware-agnostic agent runtime** with a layered trait system: Layer 0 (bare-metal, no alloc), Layer 1 (sync, with alloc), Layer 2 (async, with tools and GPU). It defines *how an agent exists on hardware*. The runtime is what makes a `Construct` instance tick.

[VaaS](https://github.com/SuperInstance/VaaS) is the **cognitive substrate** — the field on which agents interact. It defines the seven pillars (cognitive thermodynamics, dual-layer communication, distributed memory, polyrhythmic timing, holographic bridges, resonance constitution, grafting protocol) and the station architecture.

OpenConstruct answers: *"How does this agent run on this hardware?"*
VaaS answers: *"What does this agent do on this bridge?"*

Neither is complete without the other. An agent with no station is a process with no purpose. A station with no agent is a chair waiting for someone to sit in it. Together, they are the bridge.

[openconstruct-docs](https://github.com/SuperInstance/openconstruct-docs) is the integration surface: it tells a developer how to *create* an OpenConstruct agent in the first place. It is the front door. VaaS is the room behind the door.

This document is the spec for the *handoff*: the moment a freshly-minted OpenConstruct agent crosses from "process" into "station."

---

## The Onboarding Problem

Today, when an agent is created — say, a new vision agent for a 40-foot trawler — somebody writes a Python file, picks a model, configures a prompt, sets a polling rate, plugs it into the substrate, writes its safety envelope, defines its garden, registers it with the constitution, assigns it an attention budget, and connects it to a UI surface. That is *eleven distinct steps*, performed by *at least three people*. It takes a week. It usually fails.

That failure is the bottleneck. Not the agent's intelligence. The *onboarding*.

The fix is to make onboarding look like *station assignment*. When a captain takes on a new deckhand, they do not run a week-long induction. They say: "This is your station. This is your wheel. This is your station book. You have ten minutes of attention per hour." The deckhand is *provisioned* in one minute. OpenConstruct should be able to do that for an AI agent.

---

## The Bridge Metaphor, Made Literal

The VaaS bridge has a fixed topology. There is a navigation station, a communications station, an engineering station, a science station, a security station, and a helm. Each station is a *role* — a set of responsibilities, a tempo, a vocabulary, and an authority level. Each station has a console (the surface the operator uses), a garden (the agent's accumulated shorthand and referents), and an attention budget (how much of the substrate's compute the station can spend per hour).

When OpenConstruct provisions a new agent, it is *assigned a station*. The assignment is the onboarding. The station defines what the agent does. The agent's OpenConstruct implementation defines *how it runs*.

```
┌──────────────────────────────────────────────────────────────┐
│                       THE BRIDGE                             │
│                                                              │
│   ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐         │
│   │   NAV   │  │  COMMS  │  │   ENG   │  │   SCI   │         │
│   │ station │  │ station │  │ station │  │ station │         │
│   └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘         │
│        │            │            │            │              │
│        └────────────┴─────┬──────┴────────────┘              │
│                           │                                 │
│                    ┌──────┴──────┐                          │
│                    │   HELM      │   ← SUPREME authority    │
│                    │  (captain)  │                          │
│                    └─────────────┘                          │
└──────────────────────────────────────────────────────────────┘
        │
        │  each station is an OpenConstruct instance
        │  with its own layer (0, 1, or 2)
        ▼
   [Layer 0 ESP32 sensor]      [Layer 1 Pi console]      [Layer 2 GPU reasoning]
```

---

## Station Schema

A station is a JSON-serializable bundle. When OpenConstruct reports "agent ready," VaaS receives this:

```yaml
station:
  id: nav-01                  # unique within the bridge
  name: "Helmsman"            # callsign
  role: navigation            # one of the bridge roles
  priority: HIGH              # LOW / MEDIUM / HIGH / CRITICAL / SUPREME
  authority: OPERATOR         # OBSERVER / ADVISOR / OPERATOR / SOVEREIGN
  tempo_hz: 10.0              # polyrhythmic clock rate
  layer: 2                    # OpenConstruct layer (0, 1, or 2)

console:
  surface: chartplotter       # what UI surface the operator sees
  modality: visual            # visual / audio / haptic / multimodal
  glanceable: true            # may the operator glance, or must they read?

garden:
  shorthand_path: /gardens/nav-01/shorthand.json
  structures_path: /gardens/nav-01/structures.json
  filters_path: /gardens/nav-01/filters.json
  referents_path: /gardens/nav-01/referents.json
  cold_storage: /cryo/nav-01/                 # Pillar 3 archive
  holographic_peers: [helm, sci]              # who holds fragments

attention_budget:
  provider: cocapn            # who meters the budget
  ceiling_tokens_per_hour: 50000
  ceiling_inferences_per_hour: 240
  cost_per_inference_usd: 0.003
  escalation_path: helm        # when over budget, escalate to whom

safety_envelope:
  hard_limits: [...]          # loaded from vessel.toml
  soft_limits: [...]
  ethical_layer: enabled
  immune_profile_path: /immune/nav-01.json

pheromones:
  emits: [heading.drift, depth.shallow, current.set]
  sensitive_to: [wind.gust, traffic.cpa, weather.front]
  decay_rate_hz: 0.1

bridges:
  explicit_to: [helm, sci, comms]   # who this station may command
  implicit_to: all                  # who this station may signal
```

This is what *station assignment* looks like as data. The agent does not need to know it. VaaS consumes it.

---

## The Assignment Protocol

The flow is five steps, each idempotent, each logged.

**Step 1 — OpenConstruct instantiates.** A developer (or, eventually, an auto-scaler) calls `construct.create(spec)`. The spec says: layer, model, hardware target, name. OpenConstruct returns a `ConstructHandle` — a live, running instance with a `hardware_id` and an active capability profile.

**Step 2 — Construct requests a station.** The handle calls `vaas.assign_station(handle, role)` — this is a single RPC. VaaS looks up the role, allocates a station id (or returns an existing one if the agent is re-assigning), generates the station bundle above, and reserves the attention budget with CoCapn.

**Step 3 — Garden seeding.** VaaS asks the agent to *seed its garden*. The agent uploads its initial shorthand, structures, filters, and referents — what it knows on day one. This becomes the garden's `genesis/`. Cryogenic archive begins immediately; holographic fragments are dispatched to peer stations within seconds.

**Step 4 — Console attachment.** VaaS binds the station's console surface to the agent's output stream. The chartplotter now receives the agent's chart annotations. The radio now speaks the agent's voice. The deck unit now plays the agent's audio alerts. The binding is bidirectional: operator input on the console becomes pheromone events for the agent.


## Layer Mapping (OpenConstruct → VaaS)

OpenConstruct's three layers map to station capability classes.

| OpenConstruct Layer | Capability | Typical Station | Example Hardware |
|--------------------|-----------|-----------------|------------------|
| **Layer 0** (bare-metal) | Static queries, no alloc, O(1) lookup | Safety reflex (pincher-equivalent), watchdog, sensor reader | ESP32, Cortex-M |
| **Layer 1** (sync, alloc) | Dynamic skill load, owned queries, persistent state | Station controller, local memory (hermes-equivalent) | Raspberry Pi, NMEA gateway |
| **Layer 2** (async, full) | Async I/O, GPU, network, tool lifecycle | Reasoning agent, vision, planning, language interface | Workstation, DGX, Jetson |

A station may *contain* multiple constructs. The navigation station might be a Layer 2 reasoning agent plus a Layer 0 reflex (the reflex runs the actual rudder command at 10Hz; the reasoner runs at 0.2Hz). VaaS does not care which OpenConstruct layer does which work — VaaS only cares about the station's external role.

---

## CoCapn Attention Budget Integration

[CoCapn](https://github.com/SuperInstance/cocapn-marine) is the **fleet captain enforcing γ + η = C** — the law that every decision has a cost (η), every problem has a value (γ), and the sum must remain within the daily cost envelope C. Without CoCapn, agents would spend whatever they want. With CoCapn, the bridge has a *budget*.

When a station is assigned, VaaS registers the station with CoCapn and CoCapn returns:

```json
{
  "station_id": "nav-01",
  "envelope": {
    "C_per_day_usd": 0.86,
    "γ_assigned": 0.95,
    "η_rolling_usd_per_hour": 0.012,
    "burst_allowance_usd": 0.10,
    "credit_line_station": "helm"
  },
  "metering": {
    "interval_s": 60,
    "callback": "vaas.attention.tick"
  }
}
```

Every inference the station makes is metered. If the station burns its daily budget before noon, CoCapn *throttles* it: fewer inferences per hour, lower-priority pheromones only, no proactive bridges. The captain can extend the budget manually — but doing so *costs* against the day's envelope. This is the *productive friction* of attention: scarce attention forces prioritization.

When CoCapn is offline, the station falls back to a local budget declared in the station bundle. No silent over-spend. No infinite inference.

---

## Open Terminal as the Console

The "Open Terminal" concept — every agent has a place to *speak* — maps cleanly onto station consoles. Each station's console is *its terminal*. The console is **visual** (chart annotations, helm readout, AIS overlay), **audio** (spoken alerts, voice queries, TTS replies), or **haptic** (wheel-shake on danger, throttle-pulse on course correction).

A station may have multiple consoles; a console may be served by multiple stations. The bridge topology is a *bipartite* graph, not a 1:1 mapping.

---

## Concrete Examples

**Example 1 — A new trawler is commissioned.** The yard calls `vaas.bridge.bootstrap(vessel_id)`. VaaS creates the helm station (captain console), the navigation station (Layer 2 on the wheelhouse PC, with a Layer 0 reflex backing it), the engineering station (Layer 1 on the NMEA gateway), the science station (Layer 2 vision agent on a Jetson), and the comms station (Layer 1 AIS/VHF). Five stations. Five OpenConstruct instances. One bridge. Three minutes from cold-iron to first pheromone.

**Example 2 — A deckhand brings a personal tablet.** The tablet runs OpenConstruct Layer 1 and registers as an "auxiliary helm" station. The captain's authority is unaffected. The deckhand can see what the captain sees, propose course corrections, but not actuate. When the captain hands the wheel to the deckhand (a single voice command), the helm authority transfers. When the captain takes it back, transfer reverses.

**Example 3 — A shore-based fleet manager joins remotely.** The fleet manager connects as an advisory station at the comms position. Priority LOW. Authority ADVISOR. Cannot actuate. Can read all pheromones. Can write to fleet pheromones (fleet-wide awareness layer, Pillar 7). When the fleet manager disconnects, the station is gracefully torn down, garden state archived, and attention budget returned to the pool.




[← Back to README](../README.md) · [← Electricity Metaphor](ELECTRICITY_METAPHOR.md) · [→ Enterprise Model](THE_ENTERPRISE_MODEL.md)
