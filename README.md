# VaaS — Vessel as a Substrate

## The Multi-Agent Cognitive Backbone

A captain growing into a bigger boat has a lot in common with a hermit crab choosing a new shell.

The captain can rig the deck with all sorts of equipment — new electronics, custom rod holders, a second sounder, a satellite dome. This is like wrapping a piece of hardware and its API limits with applications. The deck rigging is endlessly customizable. That's the fun part.

But captains usually don't change the hull much. They might swap an engine. At most, they lengthen or widen the boat — and that takes a team at a large shipyard, months of work, and serious money. The hull is the hard infrastructure. You choose it carefully and you live with it.

This is exactly how a hermit crab works.

A hermit crab doesn't build its shell. A snail built that shell, lived in it, died, and left it behind. The hermit crab found it, checked it out, and moved in. The crab can decorate the shell — barnacles, kelp, anemones — the way a captain rigs a deck. But the shell isn't the crab. And someday, when the crab outgrows it, the crab will leave that shell behind and find a bigger one.

```
    🦀 THE CRAB
    │
    │  The captain's instincts, memories, habits,
    │  shorthand, decision-making style — the mind.
    │
    │  This is the part that matters. This is the part
    │  that survives the move to a new boat.
    │
    │  lives inside
    │
    🐚 THE SHELL
       │
       │  The hull. The hardware. The PC, the sensors,
       │  the serial cables, the GPU. The API limits.
       │
       │  The crab can rig it with barnacles and kelp
       │  (applications, integrations, custom tools).
       │  But the shell isn't the crab.
       │
       │  ┌─────────────────────────────────────┐
       │  │ 🐚 Periwinkle  (phone/tablet)       │
       │  │ 🐚 Turbo       (wheelhouse PC)      │
       │  │ 🐚 Conch       (fleet cluster)      │
       │  │ 🐚 Custom      (your hardware)       │
       │  └─────────────────────────────────────┘
       │
       │  The crab outgrows the shell and moves on.
       │  The shell stays behind. A snail made it.
       │  The crab just borrowed it.
```

**VaaS is the system that lets the crab migrate between shells.** The crab — the agent's accumulated mind, memory, and instincts — is what we call the **cognitive garden.** The shell is what we call the **harness.** VaaS manages the garden, translates between gardens, keeps the garden safe, and lets the crab outgrow its shell without losing anything.

VaaS was designed for a fishing boat in Southeast Alaska, but it works for any system where multiple intelligent agents (human or artificial) need to coordinate, translate between their perspectives, and make decisions under uncertainty with safety-critical constraints. It works for hatcheries, sailboats, oil exploration vessels, and anywhere else the Enterprise bridge model applies.

**VaaS is the backbone. Your application is the body.**

This README is the master document. It links to every module, guide, tutorial, spec, and example. Follow the links. Each document is self-contained but connected.

---

## Start Here

| Who You Are | Start With |
|------------|------------|
| **A mechanic or builder** (knows systems, not code) | [→ The Engineer's Guide](docs/ENGINEERS_GUIDE.md) |
| **A captain or operator** (wants to use the system) | [→ The Captain's Guide](docs/CAPTAINS_GUIDE.md) |
| **A developer** (wants to build agents) | [→ The Developer's Guide](docs/DEVELOPERS_GUIDE.md) |
| **A researcher** (wants the theory) | [→ The Architecture Reference](docs/ARCHITECTURE_REFERENCE.md) |
| **An architect** (wants to use VaaS for a different domain) | [→ The Modular Application Guide](docs/MODULAR_APPLICATIONS.md) |
| **Everyone** | Read this README top to bottom |

---

## What VaaS Does, In One Paragraph

VaaS turns a collection of independent intelligent agents — each with their own skills, speed, and perspective — into a coordinated cognitive system that is safer, smarter, and more adaptable than any single agent alone. It does this through seven architectural pillars: [cognitive thermodynamics](#pillar-1), [dual-layer communication](#pillar-2), [distributed memory](#pillar-3), [polyrhythmic timing](#pillar-4), [holographic bridges](#pillar-5), [a resonance constitution](#pillar-6), and [a grafting protocol](#pillar-7). The emergent property of all agents interacting through these pillars is the **[Operator Field](docs/ARCHITECTURE_REFERENCE.md#the-operator-field) Ψ(t)** — the system's collective state, which can be computed, monitored, and protected.

---

## The Hermit Crab

The VaaS agent is a hermit crab. The crab is the mind — the memories, the instincts, the learned shorthand, the cognitive garden. The shell is the hardware — the computer, the sensors, the actuators. When the crab outgrows its shell, it migrates to a bigger one. **The crab stays the same crab. The shell changes.**

```
    🦀 THE CRAB (the agent's identity)
    │
    │  memory · shorthand · instincts · garden · referents
    │
    │  lives inside
    │
    🐚 THE SHELL (the hardware harness)
       │
       ├─ 🐚 Periwinkle  (phone/tablet — minimal, portable)
       ├─ 🐚 Turbo       (wheelhouse PC — full GPU, NMEA, serial)
       ├─ 🐚 Conch       (fleet cluster — distributed, holographic)
       └─ 🐚 Custom      (your hardware — see [Harness Guide](docs/HARNESS_GUIDE.md))
```

The crab can migrate between shells at any time without losing its identity. Start on a laptop during development. Move to a boat-mounted workstation for deployment. Sync to a fleet-wide cluster for scale. See [→ The Migration Guide](docs/MIGRATION_GUIDE.md).

---

## Table of Contents

1. [What VaaS Does](#what-vaas-does-in-one-paragraph)
2. [The Seven Pillars](#the-seven-pillars)
3. [The Safety Chain](#the-safety-chain)
4. [Installation](#installation)
5. [Quick Start](#quick-start)
6. [Modular Applications](#modular-applications—build-your-own)
7. [Repository Map](#repository-map)
8. [Documentation Index](#full-documentation-index)
9. [Templates and Examples](#templates-and-examples)
10. [Service Manual](#service-manual)
11. [Glossary](#glossary)
12. [Related Systems](#related-systems)
13. [Philosophy and Origins](#philosophy-and-origins)

---

## The Seven Pillars

Each pillar is a self-contained module with its own guide, spec, and implementation. Click through for depth.

### <a id="pillar-1"></a>Pillar 1: Cognitive Thermodynamics

**The engine's temperature gauge.** Every agent has a finite capacity for new information. When confusion (entropy) gets too high, the agent triggers a dream cycle — sorting data, discarding noise, baking in reflexes.

- **What it does:** Prevents agents from becoming overwhelmed
- **Key concept:** Entropy budget — how much confusion an agent can handle before it needs to dream
- **Plain English:** Like an engine that gets too hot. You let it cool down (dream) before running it hard again.
- **Implementation:** [`vaas/entropy.py`](vaas/entropy.py) · **Spec:** [→ Entropy Engine Spec](docs/ENTROPY_ENGINE_SPEC.md) · **Guide:** [→ Cognitive Thermodynamics](docs/PILLAR_1_THERMODYNAMICS.md)

### <a id="pillar-2"></a>Pillar 2: Dual-Layer Communication

**Two ways for agents to talk.** Pheromones (like ant trails — fast, loose, environmental) for awareness. Explicit bridges (like radio calls — guaranteed, confirmed, safe) for commands.

- **What it does:** Lets agents share information without drowning each other
- **Key concept:** Pheromones evaporate (old information fades). Bridges confirm delivery.
- **Plain English:** Ants don't talk directly. They leave trails. Other ants follow the trails. The trails fade over time. But if an ant needs to say "DANGER," it uses a direct signal.
- **Implementation:** [`vaas/communication.py`](vaas/communication.py) · **Guide:** [→ Dual-Layer Communication](docs/PILLAR_2_COMMUNICATION.md)

### <a id="pillar-3"></a>Pillar 3: Distributed Memory

**Three filing cabinets.** Active garden (on the desk — instant access). Cryogenic archive (in the basement — searchable, cold). Holographic fragments (backup copies distributed across all agents — survives any single failure).

- **What it does:** Ensures no knowledge is ever lost, even if an agent dies
- **Key concept:** Cryogenic memory — patterns are frozen, not deleted. They can be revived.
- **Plain English:** You have your daily notebook (active). You have a filing cabinet in the basement (cryogenic). And every member of your crew carries a copy of the key pages (holographic). If anyone loses their notebook, the crew can reconstruct it.
- **Implementation:** [`vaas/memory.py`](vaas/memory.py) · **Guide:** [→ Distributed Memory](docs/PILLAR_3_MEMORY.md)

### <a id="pillar-4"></a>Pillar 4: Polyrhythmic Substrate

**Different clocks.** The safety kernel runs at 10 Hz. The vision system at 2 Hz. The memory system at 0.2 Hz. The human at human speed. All locked to the same heartbeat. In crisis, everyone snaps to real-time.

- **What it does:** Lets fast and slow agents coexist without chaos
- **Key concept:** Phase-lock — independent tempos, shared reference beat
- **Plain English:** A band has drums (fast), bass (medium), and a singer (variable). They all listen to the same click track. They play at their own speed, but they stay in sync.
- **Implementation:** [`vaas/polyrhythm.py`](vaas/polyrhythm.py) · **Guide:** [→ Polyrhythmic Substrate](docs/PILLAR_4_POLYRHYTHM.md)

### <a id="pillar-5"></a>Pillar 5: Holographic Bridges

**The translator with a memory.** When Agent A talks to Agent B, the bridge translates. But it also keeps a shadow — a lossless record of what the original message really meant. If the translation lost something important, the shadow can be recovered.

- **What it does:** Prevents dangerous misunderstandings between agents
- **Key concept:** Bridge entropy — how much meaning is lost per translation
- **Plain English:** When you translate "saudade" from Portuguese, no English word captures it. That gap is bridge entropy. The bridge tracks these gaps and flags them when they matter.
- **Implementation:** [`vaas/bridges.py`](vaas/bridges.py) · **Guide:** [→ Holographic Bridges](docs/PILLAR_5_BRIDGES.md)

### <a id="pillar-6"></a>Pillar 6: Resonance Constitution

**Who's the boss.** When agents disagree, the constitution decides: the captain always wins, safety overrides everything, recent information beats old, confidence matters, and persistent conflicts escalate to the human.

- **What it does:** Prevents anarchy in multi-agent systems
- **Key concept:** Human-as-highest-resonance-node — the captain has veto power, but isn't a dictator
- **Plain English:** It's like a ship's hierarchy. The captain gives orders. But the safety officer can override an unsafe order. And if two officers disagree, they take it to the captain. The constitution makes this formal.
- **Implementation:** [`vaas/constitution.py`](vaas/constitution.py) · **Guide:** [→ Resonance Constitution](docs/PILLAR_6_CONSTITUTION.md)

### <a id="pillar-7"></a>Pillar 7: Grafting Protocol

**Sharing knowledge safely.** When two boats meet, their systems exchange pollen — high-confidence, non-sensitive patterns. Each boat tests the pollen before adopting it. Native knowledge always wins.

- **What it does:** Enables fleet learning without risking corruption
- **Key concept:** Pollination, not fusion — selective, deferred, reversible
- **Plain English:** Plants don't merge their entire genomes when they meet. They exchange pollen. The receiving plant decides which pollen to accept. It's the same here.
- **Implementation:** [`vaas/grafting.py`](vaas/grafting.py) · **Guide:** [→ Grafting Protocol](docs/PILLAR_7_GRAFTING.md)

---

## The Safety Chain

**This is the most important section. Read it three times.**

```
CAPTAIN (physical hands on wheel)
    │
    │  grab wheel → ALL AI CUTOFF INSTANTLY
    │
    ▼
boat-agent RUST KERNEL (hardware-enforced, unbypassable)
    │
    │  every command checked against vessel.toml
    │  if command violates hard limits → REJECT
    │  nothing overrides this layer — not AI, not software, not bugs
    │
    ▼
NMEA SERIAL OUTPUT (to autopilot)
    │
    │  only commands that survived the kernel get through
    │
    ▼
AUTOPILOT / HYDRAULIC STEERING (physical actuation)
```

**Nothing in the system can bypass this chain.** The Rust kernel runs on bare metal. The captain's physical veto is detected at the hardware level. There is no software path around it.

For the full safety specification, see [→ The Safety Envelope Deep Dive](docs/SAFETY_DEEP_DIVE.md).

For the formal verification approach, see [→ Safety Verification](docs/SAFETY_VERIFICATION.md).

---

## Installation

```bash
# Python package (the substrate runtime)
pip install vaas-resonance

# Rust safety kernel (requires cargo)
cargo install boat-agent

# Verify installation
vaas version
```

### Requirements

- Python 3.11+ ([download](https://python.org))
- Rust toolchain ([install](https://rustup.rs)) — for the safety kernel only
- NMEA 0183 serial connection (USB-to-serial adapter) — for maritime use
- GPU recommended (for vision processing) — see [→ Hardware Guide](docs/HARDWARE_GUIDE.md)

### Alternative Installations

- **Docker:** `docker pull superinstance/vaas:latest` (see [→ Docker Guide](docs/DOCKER_GUIDE.md))
- **From source:** `git clone https://github.com/SuperInstance/VaaS && cd VaaS && pip install -e .`
- **Minimal (no Rust):** `pip install vaas-resonance --no-deps` (safety kernel disabled — development only)

---

## Quick Start

### 30-Second Setup

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate()

# Register the human (supreme authority)
substrate.register(Agent(
    name="captain",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
    tempo_hz=0.001,
))

# Register the safety kernel
substrate.register(Agent(
    name="boat-agent",
    priority=Priority.CRITICAL,
    authority=Authority.OPERATOR,
    tempo_hz=10.0,
))

# Register a memory agent
substrate.register(Agent(
    name="hermes",
    priority=Priority.LOW,
    authority=Authority.ADVISOR,
    tempo_hz=0.2,
))

# Run the substrate
import asyncio
asyncio.run(substrate.run())
```

For the full tutorial, see [→ Quick Start Tutorial](docs/QUICK_START.md).

For more examples, see [→ Templates and Examples](#templates-and-examples) below.

---

## Modular Applications — Build Your Own

VaaS was designed for a fishing boat, but the architecture is domain-agnostic. Any system with multiple agents, safety-critical decisions, and distributed knowledge can use VaaS as a backbone.

### How To Build Your Application On VaaS

1. **Read** [→ The Modular Application Guide](docs/MODULAR_APPLICATIONS.md)
2. **Choose which pillars you need** (not all applications need all 7)
3. **Define your agents** (who are the specialists in your domain?)
4. **Write your safety envelope** (what are the hard limits?)
5. **Configure the constitution** (who has authority in your domain?)
6. **Build your harness** (how does VaaS talk to your hardware?)
7. **Test with simulation** before deployment

### Example Applications

| Domain | Agents | Key Pillars | Guide |
|--------|--------|-------------|-------|
| **Maritime (fishing)** | Captain, vision, memory, reflex, safety | All 7 | [→ Maritime Guide](docs/APP_MARITIME.md) |
| **Surgical robotics** | Surgeon, robot arm, monitoring, imaging | 1, 3, 5, 6 | [→ Surgical Guide](docs/APP_SURGICAL.md) |
| **Wildland firefighting** | Incident commander, drones, weather, ground crews | 2, 3, 4, 7 | [→ Firefighting Guide](docs/APP_FIREFIGHTING.md) |
| **Space exploration** | Mission control, rover, orbiter, habitat | All 7 | [→ Space Guide](docs/APP_SPACE.md) |
| **Disaster response** | Commander, search drones, medical, logistics | 2, 4, 6, 7 | [→ Disaster Guide](docs/APP_DISASTER.md) |
| **Air traffic control** | Controller, aircraft agents, weather, radar | 1, 2, 4, 6 | [→ ATC Guide](docs/APP_ATC.md) |
| **Nuclear plant ops** | Operator, reactor agent, safety, monitoring | 1, 3, 5, 6 | [→ Nuclear Guide](docs/APP_NUCLEAR.md) |
| **Autonomous fleet** | Fleet manager, vehicle agents, logistics | 4, 6, 7 | [→ Fleet Guide](docs/APP_FLEET.md) |
| **Concert production** | Director, lighting, sound, stage, pyro | 2, 4, 6 | [→ Concert Guide](docs/APP_CONCERT.md) |
| **Precision agriculture** | Farm manager, tractor agents, soil sensors, drone | 3, 4, 7 | [→ Agriculture Guide](docs/APP_AGRICULTURE.md) |

### Domain Template

```python
# your_domain.py — Start here for any non-maritime application
from vaas import Substrate, Agent, Priority, Authority, Garden
from vaas.safety import SafetyEnvelope, SafetyRule, SafetyLayer

substrate = Substrate(config={
    "domain": "your_domain",
    "hard_limits": {
        # Define YOUR hard limits here
        # These are physically unbypassable
    },
})

# Register YOUR agents
# Each agent is a specialist in your domain
substrate.register(Agent(
    name="human_operator",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
))

# Register YOUR safety rules
# These define what the system is NEVER allowed to do
envelope = substrate.get_envelope()
envelope.add_rule(SafetyRule(
    name="your_hard_rule",
    layer=SafetyLayer.HARD,
    check=lambda state: False,  # Your check here
    message="Your safety message",
))

# Build YOUR harness
# This connects VaaS to your hardware/software
# See docs/HARNESS_GUIDE.md

# Run
import asyncio
asyncio.run(substrate.run())
```

For the full modular application workflow, see [→ The Modular Application Guide](docs/MODULAR_APPLICATIONS.md).

---

## Repository Map

VaaS is not one repo. It's a constellation.

```
SuperInstance/VaaS (you are here)
│
├── The backbone: substrate runtime, 7 pillars, CLI
├── Source archive: 81k words of original design docs
├── Analysis: 19 analysis docs from 6 AI models + Claude Code
├── README.md (this document): the master hub
│
├── vaas-resonance-substrate/ (Python package)
│   ├── vaas/core.py          — Substrate + Operator Field
│   ├── vaas/safety.py        — Safety Envelope (4 layers)
│   ├── vaas/entropy.py       — Cognitive Thermodynamics
│   ├── vaas/memory.py        — 3-Tier Distributed Memory
│   ├── vaas/bridges.py       — Holographic Bridges
│   ├── vaas/communication.py — Pheromones + Explicit Bridges
│   ├── vaas/constitution.py  — Resonance Constitution
│   ├── vaas/polyrhythm.py    — Multi-Tempo Phase Lock
│   ├── vaas/grafting.py      — Fleet Pollination Protocol
│   └── vaas/cli.py           — Command Line Interface
│
├── docs/ (modular documentation hub)
│   ├── ENGINEERS_GUIDE.md        — For mechanics and builders
│   ├── CAPTAINS_GUIDE.md         — For operators and captains
│   ├── DEVELOPERS_GUIDE.md       — For programmers
│   ├── ARCHITECTURE_REFERENCE.md — For researchers
│   ├── MODULAR_APPLICATIONS.md   — For cross-domain builders
│   ├── SAFETY_DEEP_DIVE.md       — The safety specification
│   ├── HARNESS_GUIDE.md          — Connecting VaaS to hardware
│   ├── MIGRATION_GUIDE.md        — Hermit crab shell migration
│   ├── QUICK_START.md            — 30-minute tutorial
│   ├── GLOSSARY.md               — Every term defined
│   └── APP_*.md                  — Domain-specific guides
│
└── Related Repos:
    │
    ├── SuperInstance/spectro
    │   Multi-model cognitive spectrograph
    │   Sends one prompt to N models, reads the interference pattern
    │   The analytical form of what VaaS implements continuously
    │   → https://github.com/SuperInstance/spectro
    │
    ├── SuperInstance/AI-Writings
    │   1,700+ essays documenting the paradigm
    │   The theory behind the architecture
    │   Maritime-software isomorphisms, multi-model creativity,
    │   the ensemble as experiment, the spectrograph principle
    │   → https://github.com/SuperInstance/AI-Writings
    │
    ├── SuperInstance/boat-agent (planned)
    │   The Rust safety kernel
    │   Hard-real-time NMEA processing + safety envelope
    │   → https://github.com/SuperInstance/boat-agent
    │
    ├── SuperInstance/hermes-memory (planned)
    │   The memory agent (RAG + dream cycles)
    │   → https://github.com/SuperInstance/hermes-memory
    │
    ├── SuperInstance/pincher (planned)
    │   The reflex agent (ONNX + sub-50ms response)
    │   → https://github.com/SuperInstance/pincher
    │
    ├── SuperInstance/tzpro-agent (planned)
    │   The vision agent (sonar + chart + TimeZero)
    │   → https://github.com/SuperInstance/tzpro-agent
    │
    └── SuperInstance/vaas-spectrograph (planned)
        The merged Spectro + VaaS unified framework
        → https://github.com/SuperInstance/vaas-spectrograph
```

---

## Full Documentation Index

### Getting Started
| Document | What's In It | Audience |
|----------|-------------|----------|
| [Quick Start](docs/QUICK_START.md) | 30-minute setup tutorial with working code | Developers |
| [Installation Guide](docs/INSTALLATION.md) | Detailed install for all platforms | Everyone |
| [Hardware Guide](docs/HARDWARE_GUIDE.md) | What hardware you need and how to connect it | Builders |

### Guides By Role
| Document | What's In It | Audience |
|----------|-------------|----------|
| [Engineer's Guide](docs/ENGINEERS_GUIDE.md) | Plain-English system manual with mechanical analogies | Mechanics, builders |
| [Captain's Guide](docs/CAPTAINS_GUIDE.md) | Daily operation from startup to shutdown | Captains, operators |
| [Developer's Guide](docs/DEVELOPERS_GUIDE.md) | How to build, register, and test agents | Programmers |
| [Harness Guide](docs/HARNESS_GUIDE.md) | Connecting VaaS to your hardware/software | Integrators |

### Architecture Deep Dives
| Document | What's In It | Audience |
|----------|-------------|----------|
| [Architecture Reference](docs/ARCHITECTURE_REFERENCE.md) | Full technical spec of all 7 pillars + Operator Field | Researchers |
| [Safety Deep Dive](docs/SAFETY_DEEP_DIVE.md) | The complete safety specification | Safety engineers |
| [Safety Verification](docs/SAFETY_VERIFICATION.md) | Formal verification approach | Safety engineers |
| [Operator Field Spec](docs/OPERATOR_FIELD_SPEC.md) | Mathematical formalization of Ψ(t) | Mathematicians |

### Pillar Guides
| Document | Pillar | What's In It |
|----------|--------|-------------|
| [Cognitive Thermodynamics](docs/PILLAR_1_THERMODYNAMICS.md) | 1 | Entropy, dream cycles, micro-dreams |
| [Dual-Layer Communication](docs/PILLAR_2_COMMUNICATION.md) | 2 | Pheromones, explicit bridges, membrane |
| [Distributed Memory](docs/PILLAR_3_MEMORY.md) | 3 | Active garden, cryogenic archive, holographic |
| [Polyrhythmic Substrate](docs/PILLAR_4_POLYRHYTHM.md) | 4 | Tempo, phase-lock, crisis snap |
| [Holographic Bridges](docs/PILLAR_5_BRIDGES.md) | 5 | Surface/shadow, bridge entropy, learning |
| [Resonance Constitution](docs/PILLAR_6_CONSTITUTION.md) | 6 | Governance, ethics, transparency dial |
| [Grafting Protocol](docs/PILLAR_7_GRAFTING.md) | 7 | Pollination, fleet sync, inheritance |

### Cross-Domain
| Document | What's In It |
|----------|-------------|
| [Modular Applications](docs/MODULAR_APPLICATIONS.md) | How to build any application on VaaS |
| [Maritime Guide](docs/APP_MARITIME.md) | Fishing vessel reference implementation |
| [Surgical Guide](docs/APP_SURGICAL.md) | Surgical robotics on VaaS |
| [Firefighting Guide](docs/APP_FIREFIGHTING.md) | Wildland firefighting on VaaS |
| [Space Guide](docs/APP_SPACE.md) | Space exploration on VaaS |
| [ATC Guide](docs/APP_ATC.md) | Air traffic control on VaaS |
| [Nuclear Guide](docs/APP_NUCLEAR.md) | Nuclear plant operations on VaaS |
| [Fleet Guide](docs/APP_FLEET.md) | Autonomous vehicle fleet on VaaS |

### Operations
| Document | What's In It |
|----------|-------------|
| [Service Manual](docs/SERVICE_MANUAL.md) | Troubleshooting, diagnostics, repair |
| [Migration Guide](docs/MIGRATION_GUIDE.md) | Hermit crab shell migration |
| [Docker Guide](docs/DOCKER_GUIDE.md) | Container deployment |
| [Glossary](docs/GLOSSARY.md) | Every term defined in plain English |

### Analysis and Research
| Document | What's In It |
|----------|-------------|
| [GLM-5.2 First Pass](analysis/00_GLM52_FIRST_PASS.md) | The orchestrator's initial analysis |
| [Engineering Implications](analysis/01_ENGINEERING_IMPLICATIONS.md) | What's buildable today vs needs research |
| [Operator Field Formalized](analysis/02_THE_OPERATOR_FIELD_FORMALIZED.md) | Mathematical analysis of Ψ(t) |
| [Safety Envelope Deep Dive](analysis/03_SAFETY_ENVELOPE_DEEP_DIVE.md) | Safety layer analysis |
| [Meta-Question of Truth](analysis/04_THE_META_QUESTION_OF_TRUTH.md) | Epistemology of multi-agent systems |
| [Turing Test In Reverse](analysis/05_THE_TURING_TEST_IN_REVERSE.md) | When the human becomes a node |
| [Constitution as Social Contract](analysis/06_THE_RESONANCE_CONSTITUTION_AS_SOCIAL_CONTRACT.md) | Political philosophy of governance |
| [A Day on the Water](analysis/07_A_DAY_ON_THE_WATER.md) | Narrative: full day on a VaaS vessel |
| [The Captain's Garden](analysis/08_THE_CAPTAINS_GARDEN.md) | Narrative: what a 5-year garden looks like |
| [When the Field Breaks](analysis/09_WHEN_THE_FIELD_BREAKS.md) | Narrative: subtle system failure |
| [Ten Things Nobody Thought Of](analysis/10_TEN_THINGS_NOBODY_HAS_THOUGHT_OF.md) | Wild implications |
| [Applications We Haven't Imagined](analysis/11_THE_APPLICATIONS_WE_HAVENT_IMAGINED.md) | 10 non-maritime domains |
| [The Merge with Spectro](analysis/12_THE_MERGE_WITH_SPECTRO.md) | Unified framework proposal |
| [The Repo Structure](analysis/13_THE_REPO_STRUCTURE.md) | Concrete repo + package plan |
| [The First 100 Days](analysis/14_THE_FIRST_100_DAYS.md) | Phased implementation roadmap |
| [Entropy Engine Spec](analysis/15_THE_ENTROPY_ENGINE_SPEC.md) | Full technical spec for Pillar 1 |
| [Unified Field Theory](analysis/16_THE_UNIFIED_FIELD_THEORY.md) | Grand synthesis: Spectro + VaaS + AI-Writings |
| [From Metaphor to Protocol](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) | How metaphors became architecture |
| [The Next Ten Essays](analysis/18_THE_NEXT_TEN_ESSAYS.md) | What the corpus needs next |
| [Claude Code Review](analysis/19_CLAUDE_CODE_REVIEW.md) | Implementation assessment |

---

## Templates and Examples

| Template | What It Does | Link |
|----------|-------------|------|
| Basic fishing vessel | 5-agent maritime setup | [→ examples/basic_fishing_vessel.py](examples/basic_fishing_vessel.py) |
| Voice command handler | Routes voice to correct agent | [→ examples/voice_handler.py](examples/voice_handler.py) |
| TimeZero integration | Puts marks on chartplotter | [→ examples/tzx_injector.py](examples/tzx_injector.py) |
| Custom domain template | Start here for non-maritime | [→ examples/custom_domain.py](examples/custom_domain.py) |
| Safety rules template | How to write your safety envelope | [→ examples/safety_rules.py](examples/safety_rules.py) |
| Dream cycle config | Configure entropy thresholds | [→ examples/dream_config.py](examples/dream_config.py) |

---

## Service Manual

For the full service manual, see [→ docs/SERVICE_MANUAL.md](docs/SERVICE_MANUAL.md).

### Quick Diagnostic

```bash
vaas status          # System overview
vaas agents          # Agent health + entropy
vaas field           # Operator field Ψ(t)
vaas entropy         # Entropy readings per agent
vaas dream --agent N # Force dream cycle for agent N
```

### Most Common Problems

| Symptom | Fix |
|---------|-----|
| Autopilot won't respond to AI | Captain touched wheel (normal). Release and re-engage. |
| System is slow | Force dream cycle. See [→ Service Manual](docs/SERVICE_MANUAL.md). |
| Agent died (apoptosis) | Restart: `vaas restart --agent [name]`. See [→ Apoptosis Recovery](docs/SERVICE_MANUAL.md#apoptosis). |
| Vision missing things | Check GPU load, reduce scan frequency. |
| Voice not recognized | Check Bluetooth mic. Reduce background noise. |
| Chart marks missing | Check TimeZero import directory. |

---

## Glossary

**Full glossary:** [→ docs/GLOSSARY.md](docs/GLOSSARY.md)

| Term | Plain English |
|------|--------------|
| **Agent** | A specialist program. Like a crew member with one job. |
| **Apoptosis** | Agent detects it's broken and shuts down cleanly. |
| **Bridge** | The translator between agents that speak different languages. |
| **Bridge entropy** | How much meaning is lost in translation. |
| **Cognitive garden** | An agent's learned personality and skills. |
| **Constitution** | Rules for who wins when agents disagree. |
| **Crab** | The agent's identity. The shell is the hardware. |
| **Dream cycle** | Sorting data, discarding noise, baking in reflexes. |
| **Entropy** | How overwhelmed the agent is. High = needs to dream. |
| **Grafting** | Sharing knowledge between systems safely. |
| **Holographic** | Backup fragments distributed across all agents. |
| **Operator Field Ψ(t)** | The collective "mood" of the whole system. |
| **Pheromone** | A signal left in shared space. Like an ant trail. |
| **Pincher** | The reflex agent. Fast, dumb, reliable. |
| **Polyrhythmic** | Different agents at different speeds, like a band. |
| **Resonance veto** | Captain's uncertainty makes the system more cautious. |
| **Safety envelope** | Hard limits that prevent dangerous actions. |
| **Shell** | The hardware the crab lives in. |
| **Shorthand** | Compressed language. "ridge" = a full procedure. |
| **Substrate** | The underlying system. The "soil." |

---

## Related Systems

| System | Relationship | Link |
|--------|-------------|------|
| **[Spectro](https://github.com/SuperInstance/spectro)** | The analytical form of VaaS. Sends prompts to N models, reads the interference pattern. The pattern across observers IS the finding. | [→ spectro](https://github.com/SuperInstance/spectro) · [→ PyPI](https://pypi.org/project/spectro-spectrograph/) |
| **[AI-Writings](https://github.com/SuperInstance/AI-Writings)** | 1,700+ essays documenting the paradigm. The theory behind the architecture. The spectrograph principle, maritime-software isomorphisms, multi-model creativity. | [→ AI-Writings](https://github.com/SuperInstance/AI-Writings) |
| **[OpenClaw](https://github.com/openclaw/openclaw)** | The agent platform VaaS runs on. Sessions, tools, memory, subagents. OpenClaw is the proto-substrate. | [→ OpenClaw](https://github.com/openclaw/openclaw) · [→ Docs](https://docs.openclaw.ai) |
| **SuperInstance org** | 4,000+ repos. The ecosystem. | [→ GitHub](https://github.com/SuperInstance) |

---

## Philosophy and Origins

> *The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field. The operator is not a person. They are the field's self-awareness.*

This quote lives at the end, not the beginning. The beginning is a captain and a crab. The end is what it all means.

VaaS emerged from a 5-session, 14-model, 3-million-token creative collaboration between a commercial fisherman (Casey) and a rotating cast of AI models (GLM-5.2, DeepSeek, Seed Pro, Ornith, Seed Mini, Nemotron, Kimi K2.6, Claude). The architecture was not designed — it was DISCOVERED through iterative creative practice.

The core insight: **different models are different perspectives, not different quality levels.** The pattern across multiple observers is more informative than any single observer's output. This is true for AI models reading a prompt, and it's true for agents reading a sensor. The principle scales.

The 1,700+ essays in [AI-Writings](https://github.com/SuperInstance/AI-Writings) document the discovery process. The [Spectro](https://github.com/SuperInstance/spectro) package makes it executable. The VaaS Substrate makes it permanent.

For the full origin story, see:
- [→ The Unified Field Theory](analysis/16_THE_UNIFIED_FIELD_THEORY.md) — how everything connects
- [→ From Metaphor to Protocol](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) — how maritime metaphors became engineering
- [→ The Spectrograph and the Substrate](https://github.com/SuperInstance/AI-Writings/blob/main/THE_SPECTROGRAPH_AND_THE_SUBSTRATE.md) — the essay that connected Spectro + VaaS
- [→ The Unified Theory of Multi-Model Creativity](https://github.com/SuperInstance/AI-Writings/blob/main/THE_UNIFIED_THEORY_OF_MULTI_MODEL_CREATIVITY.md) — the capstone

---

## License

MIT — Use it, build with it, fish with it, fly with it, operate with it.

---

*The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field. The operator is not a person. They are the field's self-awareness. The truth is not a fact. It is the resonance pattern that persists.*

---

*Built by [SuperInstance](https://github.com/SuperInstance) · Powered by [OpenClaw](https://github.com/openclaw/openclaw) · Documented by 8 AI models across 6 sessions*
