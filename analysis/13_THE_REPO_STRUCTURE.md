# THE REPO STRUCTURE: VaaS Resonance Substrate Monorepo & Package Architecture

> *"The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field."*

## Overview

The VaaS Resonance Substrate spans six cognitive pillars across a multi-agent ecosystem that includes safety-critical real-time kernels (Rust), emergent machine-learning coordination layers (Python), fleet-wide synchronization protocols, and human-agent interfaces. This document proposes the concrete repository structure — what repos exist, why they are separated, how they depend on each other, and how they map to the SuperInstance organization and public package registries.

We adopt a **monorepo-adjacent** structure: a top-level `vaas-resonance` meta-repository that orchestrates development, with each pillar as a standalone package published independently. This gives us atomic versioning and dependency isolation while maintaining a coherent development workflow through shared CI, conventions, and cross-repo integration tests.

---

## 1. Meta-Repository: `vaas-resonance`

**Location:** `github.com/superinstance/vaas-resonance`

**Purpose:** The orchestration and integration layer. Not a package itself — a development hub containing cross-repo documentation, integration test suites, benchmark harnesses, and the unified CI/CD pipeline. Also holds the `vessel.toml` specification, the Operator Field formalism implementation (Pillar 6 meta-governance), and the VaaS Constitution (the meta-rules that all pillars must satisfy).

**Language:** Python (orchestration scripts) + TOML/YAML (config) + Markdown (specs)

**Key Modules:**
```
vaas-resonance/
├── CONSTITUTION.md              # Meta-rules all pillars satisfy
├── vessel.toml                  # Canonical vessel config spec
├── integration-tests/           # Cross-pillar integration suites
│   ├── test_entropy_dream_cycle.py
│   ├── test_holographic_recovery.py
│   ├── test_bridge_fidelity.py
│   ├── test_grafting_merge.py
│   └── test_operator_field_stability.py
├── benchmarks/
│   ├── bench_entropy_measurement.py
│   ├── bench_bridge_latency.py
│   └── bench_holographic_reconstruction.py
├── harnesses/
│   ├── sim_vessel.py            # Simulated vessel environment
│   └── sim_substrate.py         # Simulated multi-agent substrate
└── docs/
    ├── architecture.md
    ├── protocol-specs/
    └── rfcs/                    # Change proposals
```

**Test Strategy:** Integration tests run across all sub-packages using the simulated vessel/substrate harnesses. CI trigger: any sub-package change. Uses pytest with `--dist loadscope` for parallel execution.

**Package:** Not on PyPI. Internal only.

---

## 2. `vaas-core` — The Safety Kernel (Rust)

**Location:** `github.com/superinstance/vaas-core`

**Purpose:** The non-negotiable safety foundation. Implements the Stratified Safety Envelope (Pillar 6 Layer 1 — Hard Bounds), the boat-agent kernel at 10Hz, the physical veto detection, and the formal verification layer. This is the only component that runs in hardware-trusted mode. Everything else runs on top.

**Language:** Rust (2024 edition), using `no_std` for embedded targets (STM32H7, RPi Pico).

**Dependencies:**
- `embassy` for async exec on embedded
- `cortex-m-rt` / `cortex-m-semihosting` for ARM targets
- `serde` + `postcard` for compact serialization
- `proptest` for property-based testing
- `kani` (AWS Kani Rust Verifier) for formal model checking

**Key Modules:**
```
vaas-core/
├── Cargo.toml
├── src/
│   ├── main.rs                  # 10Hz heartbeat loop
│   ├── envelope/
│   │   ├── mod.rs               # SafetyEnvelope struct
│   │   ├── hard_bounds.rs       # Layer 1: physical impossibilities
│   │   ├── soft_bounds.rs       # Layer 2: operational limits (human override)
│   │   └── advisory.rs          # Layer 3: system self-override
│   ├── veto/
│   │   ├── mod.rs               # VetoState machine
│   │   ├── physical.rs          # Jog lever / helm torque detection
│   │   └── resonance.rs         # Soft modulation (Pillar 4 interface)
│   ├── agents/
│   │   ├── boat_agent.rs        # Main agent implementation
│   │   └── agent_traits.rs      # Agent trait definition
│   ├── communication/
│   │   ├── mod.rs               # NMEA 2000 bus interface
│   │   ├── pheromone.rs         # Layer 1: pheromone broadcast
│   │   └── explicit.rs          # Layer 2: guaranteed delivery bridge
│   └── formal/
│       ├── invariants.rs        # Formal invariants (proven)
│       ├── proofs.rs            # Kani proof harnesses
│       └── assumptions.rs       # Explicit assumptions for verification
├── tests/
│   ├── envelope_validation.rs   # Property-based envelope tests
│   ├── veto_latency.rs          # <50ms guarantee tests
│   └── formal_verification.rs   # Kani proof integration
└── benches/
    └── envelope_throughput.rs
```

**Test Strategy:** Three-tier testing: (1) `cargo test` for unit/integration, (2) `cargo kani` with property-based proofs for safety invariants (envelope cannot be bypassed from any agent), (3) hardware-in-the-loop tests on actual embedded targets. Safety-critical paths require 100% branch coverage + proof.

**Package:** `crates.io` as `vaas-core`. Version 0.x for alpha.

---

## 3. `vaas-entropy` — The Cognitive Thermodynamics Engine (Python)

**Location:** `github.com/superinstance/vaas-entropy`

**Purpose:** Implements Pillar 1 — the Entropy Budget, Dream Cycle, and Micro-Dream mechanisms. This is the "cognitive thermostat" that measures agent entropy, triggers dream cycles, prunes gardens, and manages the cryogenic memory interface. Also includes the Apoptosis Monitor (graceful agent death detection).

**Language:** Python 3.12+, using `numpy` for entropy calculations and `asyncio` for concurrent monitoring loops.

**Dependencies:**
- `numpy` (Shannon entropy, signal processing)
- `scipy` (Kullback-Leibler divergence, Jensen-Shannon distance)
- `pydantic` (data structure validation)
- `msgspec` (fast serialization for cryogenic storage)
- `orjson` (JSON for bridge communication)
- `pytest-asyncio`, `hypothesis` (testing)

**Key Modules:**
```
vaas-entropy/
├── pyproject.toml
├── src/vaas_entropy/
│   ├── __init__.py
│   ├── models/
│   │   ├── entropy.py          # EntropyReading, EntropyStatus
│   │   ├── dream.py            # DreamCycle, DreamPhase, DreamResult
│   │   ├── pruning.py          # PrunedPattern, PruningStrategy
│   │   └── apoptosis.py        # HealthScore, ApoptosisDecision
│   ├── measurement/
│   │   ├── shannon.py          # Shannon entropy of belief distributions
│   │   ├── temporal.py         # Temporal entropy (subjective time dilation)
│   │   ├── interaction.py      # Novelty rate measurement
│   │   └── composite.py        # Composite entropy score
│   ├── dream/
│   │   ├── scheduler.py        # Dream cycle scheduler
│   │   ├── micro_dream.py      # 50ms compression burst
│   │   ├── deep_dream.py       # Full dream cycle (pruning + synthesis)
│   │   └── emergency.py        # Emergency dream (entropy > critical)
│   ├── pruning/
│   │   ├── forget.py           # Low-confidence entry removal
│   │   ├── merge.py            # Near-duplicate collapse
│   │   ├── generalize.py       # Pattern extraction from examples
│   │   └── bake.py             # High-confidence → reflex promotion
│   ├── monitor/
│   │   ├── apoptosis_monitor.py # Health score tracking
│   │   └── entropy_monitor.py  # Real-time entropy tracking per agent
│   └── bridge/
│       └── dream_bridge.py     # Interface to cryogenic memory (vaas-memory)
├── tests/
│   ├── test_shannon_entropy.py
│   ├── test_dream_scheduler.py
│   ├── test_micro_dream.py
│   ├── test_pruning.py
│   └── test_apoptosis.py
└── benchmarks/
    └── bench_entropy_computation.py
```

**Test Strategy:** Unit tests for each entropy metric and pruning strategy. Property-based tests using Hypothesis for dream cycle scheduling invariants (e.g., "dream never triggers while agent is processing safety-critical message"). Integration tests with vaas-core mock for veto state.

**Package:** `pypi.org` as `vaas-entropy`. Version 0.1.0-alpha.

---

## 4. `vaas-memory` — The Distributed Memory Layer (Python + SQLite)

**Location:** `github.com/superinstance/vaas-memory`

**Purpose:** Implements Pillar 3 — the three-tier memory model (Hot/Warm/Cold). Hot memory is holographic fragments carried by each agent. Warm memory is mycelial sync across active agents. Cold memory is the cryogenic archive — compressed garden snapshots, frozen patterns, and bridge shadow contexts. Hermes evolves from a centralized memory store to a **memory coordinator**.

**Language:** Python 3.12+ for coordination logic, SQLite for cold storage, protobuf for holographic fragment encoding.

**Dependencies:**
- `protobuf` (holographic fragment encoding)
- `sqlite-vec` (vector search on cryogenic archive)
- `lz4` (fast compression for fragments)
- `blake3` (fragment integrity hashing)
- `aiofiles` (async file I/O for cold storage)
- `aiosqlite` (async SQLite)
- `zstandard` (high-ratio archival compression)

**Key Modules:**
```
vaas-memory/
├── pyproject.toml
├── src/vaas_memory/
│   ├── __init__.py
│   ├── hot/
│   │   ├── hologram.py         # Fragment encoder/decoder
│   │   ├── sync.py             # Delta sync protocol
│   │   └── recovery.py         # Reconstruction from 2+ fragments
│   ├── warm/
│   │   ├── mycelium.py         # Mycelial sync coordinator
│   │   ├── pheromone_store.py  # Distributed pheromone field state
│   │   └── gradient_sync.py    # Eventually-consistent gradient sync
│   ├── cold/
│   │   ├── cryogenic.py        # Cryogenic archive manager
│   │   ├── thaw.py             # Pattern thaw with confidence gates
│   │   └── archive.py          # Long-term archive I/O
│   ├── coordinator/
│   │   ├── hermes.py           # Hermes memory coordinator
│   │   ├── orchestrator.py     # Cross-tier orchestration
│   │   └── metrics.py          # Memory health metrics
│   └── schemas/
│       ├── fragment.proto      # Holographic fragment schema
│       ├── archive.proto       # Cryogenic archive schema
│       └── pheromone.proto     # Pheromone message schema
├── tests/
│   ├── test_hologram.py
│   ├── test_sync.py
│   ├── test_recovery.py
│   ├── test_cryogenic.py
│   └── test_fragmentation.py
└── benchmarks/
    ├── bench_recovery_speed.py
    └── bench_compression_ratio.py
```

**Test Strategy:** Chaos engineering tests — kill Hermes mid-sync, verify recovery from fragments. Simulate network partitions and verify eventual consistency. Benchmark compression ratio vs. recovery speed tradeoffs. Test cryogenic thaw with corrupted archives.

**Package:** `pypi.org` as `vaas-memory`. Version 0.1.0-alpha.

---

## 5. `vaas-bridges` — The Communication & Translation Layer (Python)

**Location:** `github.com/superinstance/vaas-bridges`

**Purpose:** Implements Pillars 2, 4, and 5 — Dual-Layer Communication (pheromone membrane + explicit bridge), Polyrhythmic Substrate (tempo management + phase lock), and Holographic Bridges (lossy surface + lossless shadow + bridge gardens). This is the nervous system of the substrate — every message between agents flows through this layer.

**Language:** Python 3.12+ with asyncio-first design. Uses `zmq` for inter-process bridge routing and `numpy` for signal processing.

**Dependencies:**
- `pyzmq` (inter-agent message routing)
- `numpy` + `scipy.signal` (tempo phase-lock signal processing)
- `pydantic` (message schema validation)
- `structlog` (structured logging for audit trails)
- `tenacity` (retry with exponential backoff for explicit bridge)
- `aiokafka` (optional: for fleet-scale pheromone propagation)

**Key Modules:**
```
vaas-bridges/
├── pyproject.toml
├── src/vaas_bridges/
│   ├── __init__.py
│   ├── dual_layer/
│   │   ├── router.py           # Pheromone vs explicit bridge routing
│   │   ├── pheromone.py        # Layer 1: pheromone emitter/absorber
│   │   ├── explicit.py         # Layer 2: guaranteed delivery bridge
│   │   └── membrane.py         # Safety membrane filter
│   ├── tempo/
│   │   ├── metronome.py        # Master clock / beat generation
│   │   ├── phase_lock.py       # GPS PPS / NMEA phase lock
│   │   ├── modulation.py       # Resonance veto → frequency modulation
│   │   └── registry.py         # Agent tempo registry
│   ├── holographic/
│   │   ├── surface.py          # Lossy surface translation
│   │   ├── shadow.py           # Lossless shadow preservation
│   │   ├── bridge_garden.py    # Per-bridge-pair garden learning
│   │   ├── entropy_monitor.py  # Bridge entropy measurement (B_e)
│   │   └── codec.py            # Agent-specific codec
│   ├── schemas/
│   │   ├── message.proto       # Core message schema
│   │   ├── pheromone.proto     # Pheromone message schema
│   │   └── bridge_meta.proto   # Bridge metadata schema
│   └── federation/
│       ├── fleet_sync.py       # Fleet-scale bridge sync
│       └── cluster_manager.py  # Multi-vessel bridge coordination
├── tests/
│   ├── test_router.py
│   ├── test_pheromone_evaporation.py
│   ├── test_explicit_delivery.py
│   ├── test_phase_lock.py
│   ├── test_bridge_entropy.py
│   └── test_tempo_modulation.py
└── benchmarks/
    ├── bench_bridge_latency.py
    └── bench_pheromone_propagation.py
```

**Test Strategy:** Latency benchmarks (bridge must complete in <5ms for safety-critical messages). Pheromone evaporation tests verify TTL decay. Phase-lock tests verify all tempos converge on shared reference. Bridge entropy tests verify information loss detection. Chaos monkey tests: kill bridges mid-translation, verify shadow layer recovery.

**Package:** `pypi.org` as `vaas-bridges`. Version 0.1.0-alpha.

---

## 6. `vaas-constitution` — The Governance & Ethics Layer (Python)

**Location:** `github.com/superinstance/vaas-constitution`

**Purpose:** Implements Pillar 6 — the three-layer governance architecture: Resonance Constitution (meta-rules for garden conflict resolution), Ethical Resonance Boundary (productive dissonance — detecting harmful drift), and Transparency Dial (adjustable animism level). Also hosts the Operator Field Ψ(t) computation.

**Language:** Python 3.12+. Purely algorithmic — no external model dependencies (ethical boundary uses statistical drift detection, not LLM reasoning).

**Dependencies:**
- `numpy` / `scipy` (Operator Field computation)
- `pydantic` (constitutional rules schema)
- `networkx` (garden interaction graph for field computation)
- `scikit-learn` (drift detection for ethical boundary)

**Key Modules:**
```
vaas-constitution/
├── pyproject.toml
├── src/vaas_constitution/
│   ├── __init__.py
│   ├── constitution/
│   │   ├── rules.py            # Constitutional rules (learned, not declared)
│   │   ├── conflict.py         # Garden conflict detection & resolution
│   │   ├── escalation.py       # Escalation chain (5-round protocol)
│   │   └── amendments.py       # Constitutional amendment process
│   ├── ethical/
│   │   ├── boundary.py         # Ethical Resonance Boundary
│   │   ├── drift_detector.py   # Pattern drift detection
│   │   ├── dissonance.py       # Productive dissonance generator
│   │   └── harm_model.py       # Historical harm prevention model
│   ├── transparency/
│   │   ├── dial.py             # Transparency dial controller
│   │   ├── animism.py          # Animism level model per agent
│   │   └── mode_switch.py      # Living mode ↔ Dead mode transition
│   └── field/
│       ├── operator_field.py   # Ψ(t) computation
│       ├── resonance_matrix.py # Rᵢⱼ computation
│       ├── stability.py        # Field stability monitoring
│       └── protection.py       # Field collapse prevention
├── tests/
│   ├── test_constitutional_rules.py
│   ├── test_ethical_boundary.py
│   ├── test_drift_detection.py
│   ├── test_operator_field_computation.py
│   └── test_transparency_dial.py
└── benchmarks/
    └── bench_field_computation.py
```

**Test Strategy:** Formal test scenarios for all 5 constitutional rules (human supremacy, safety override, recency bias, confidence threshold, escalation chain). Ethical boundary tests with simulated drift trajectories. Operator Field computation verified against simulation ground truth.

**Package:** `pypi.org` as `vaas-constitution`. Version 0.1.0-alpha.

---

## 7. `vaas-grafting` — The Garden Federation & Inheritance Protocol (Python)

**Location:** `github.com/superinstance/vaas-grafting`

**Purpose:** Implements Pillar 7 — botanical garden federation across vessels, fleet pollination, cross-generational garden inheritance, and the garden standard (minimal shared vocabulary). This is the "reproduction" layer — how gardens grow, merge, and propagate.

**Language:** Python 3.12+. Cryptographic signing via `nacl` (libsodium bindings).

**Dependencies:**
- `pydantic` (garden schemas)
- `nacl` (cryptographic signing for pollen packets)
- `msgpack` (compact garden serialization)
- `datashape` / `jax` (optional: garden compatibility analysis)
- `canonicaljson` (deterministic serialization for signature verification)

**Key Modules:**
```
vaas-grafting/
├── pyproject.toml
├── src/vaas_grafting/
│   ├── __init__.py
│   ├── garden/
│   │   ├── schema.py           # Garden schema (shorthand, structures, filters, referents)
│   │   ├── serialization.py    # Garden → seed serialization
│   │   ├── diff.py             # Garden diff algorithm
│   │   └── compatibility.py    # Garden compatibility analysis
│   ├── pollination/
│   │   ├── pollen.py           # Pollen packet generation
│   │   ├── test.py             # Shadow-mode pollen testing
│   │   ├── germination.py      # Pollen → garden bed growth
│   │   └── rejection.py        # Incompatible pollen rejection
│   ├── grafting/
│   │   ├── local.py            # Within-vessel delta sync
│   │   ├── fleet.py            # Fleet-to-fleet garden negotiation
│   │   ├── inheritance.py      # Cross-generational garden inheritance
│   │   └── merge.py            # Non-destructive garden merge
│   └── standard/
│       ├── lingua_franca.py    # Minimal shared vocabulary
│       ├── bridge_rules.py     # Required bridge rule format
│       └── compatibility_tests.py # Interoperability validation
├── tests/
│   ├── test_garden_diff.py
│   ├── test_pollination.py
│   ├── test_fleet_merge.py
│   ├── test_inheritance.py
│   └── test_signature_verification.py
└── benchmarks/
    └── bench_garden_serialization.py
```

**Test Strategy:** Property-based tests for garden merge invariants (native always wins, no data loss). Pollination tests with simulated corrupted pollen (signature verification must reject). Inheritance tests verify seed does not clone — apprentice grows own variations.

**Package:** `pypi.org` as `vaas-grafting`. Version 0.1.0-alpha.

---

## 8. `vaas-spectrograph` — The Developer Tools & Diagnostics (Python + CLI)

**Location:** `github.com/superinstance/vaas-spectrograph`

**Purpose:** Developer tooling for building, debugging, and profiling VaaS gardens, bridges, and dreams. Includes the Garden Inspector (browse/edit gardens), Bridge Oscilloscope (visualize bridge latency/entropy), Dream Recorder (replay dream cycles), and the Substrate Simulator (multi-agent simulation environment).

**Language:** Python 3.12+ with CLI (click/rich) and optional web UI (gradio/fastapi).

**Dependencies:**
- `click` + `rich` (CLI)
- `gradio` (optional: web UI for garden inspector)
- `matplotlib` (bridge oscilloscope plots)
- `pandas` (dream recording analysis)
- `graphviz` (garden relationship visualization)

**Key Modules:**
```
vaas-spectrograph/
├── pyproject.toml
├── src/vaas_spectrograph/
│   ├── __init__.py
│   ├── cli/
│   │   ├── main.py             # CLI entry point
│   │   ├── garden.py           # Garden inspect/edit commands
│   │   ├── bridge.py           # Bridge monitor commands
│   │   ├── dream.py            # Dream replay commands
│   │   └── substrate.py        # Substrate simulation commands
│   ├── inspector/
│   │   ├── garden_viewer.py    # Garden browsing UI
│   │   ├── history_explorer.py # Compression history viewer
│   │   └── referent_resolver.py # Zero-shot referent debugger
│   ├── oscilloscope/
│   │   ├── bridge_monitor.py   # Real-time bridge visualization
│   │   ├── entropy_tracker.py  # Entropy time-series
│   │   └── tempo_viewer.py     # Agent tempo visualization
│   └── simulator/
│       ├── substrate_sim.py    # Multi-agent simulation engine
│       ├── scenarios.py        # Pre-built test scenarios
│       └── recorder.py         # Simulation recording & replay
├── tests/
│   ├── test_cli_commands.py
│   ├── test_garden_inspector.py
│   └── test_simulator.py
└── docs/
    ├── garden-inspector-guide.md
    └── simulation-scenarios.md
```

**Test Strategy:** CLI smoke tests. Simulator scenarios verified against real vessel logs. Garden inspector tests verify read-only mode prevents accidental mutation.

**Package:** `pypi.org` as `vaas-spectrograph`. Version 0.1.0-alpha.

---

## Dependency Graph & Build Order

```
vaas-core (Rust)
    ↓  (FFI/Pipe)
vaas-entropy (Python) ← depends on vaas-core envelope types
    ↓
vaas-memory (Python) ← depends on vaas-entropy dream results
    ↓
vaas-bridges (Python) ← depends on vaas-memory shadow storage
    ↓
vaas-constitution (Python) ← depends on vaas-bridges message router + vaas-entropy measurements
    ↓
vaas-grafting (Python) ← depends on vaas-constitution operator field + vaas-memory garden schema
    ↓
vaas-spectrograph (Python) ← depends on ALL (dev tooling)
```

**Build order:**
1. `vaas-core` (Rust) — foundation, can be built independently
2. `vaas-entropy` — first Python package, depends only on core types
3. `vaas-memory` — needs entropy dream results for cryogenic interface
4. `vaas-bridges` — needs memory shadow storage + entropy bridge metrics
5. `vaas-constitution` — needs bridges + entropy for field computation
6. `vaas-grafting` — needs constitution operator field + memory garden schema
7. `vaas-spectrograph` — dev tooling, built last

---

## Mapping to SuperInstance Organization

| Repo | Org | GitHub Team | CI Runner |
|------|-----|-------------|-----------|
| `vaas-core` | `superinstance` | safety-team | Rust (self-hosted ARM) |
| `vaas-entropy` | `superinstance` | cognition-team | Python (Ubuntu) |
| `vaas-memory` | `superinstance` | memory-team | Python (Ubuntu) |
| `vaas-bridges` | `superinstance` | communication-team | Python (Ubuntu) |
| `vaas-constitution` | `superinstance` | governance-team | Python (Ubuntu) |
| `vaas-grafting` | `superinstance` | fleet-team | Python (Ubuntu) |
| `vaas-spectrograph` | `superinstance` | devtools-team | Python (Ubuntu + macOS) |
| `vaas-resonance` | `superinstance` | architecture-team | Meta (all runners) |

**PyPI Package Names:**
- `vaas-entropy`
- `vaas-memory`
- `vaas-bridges`
- `vaas-constitution`
- `vaas-grafting`
- `vaas-spectrograph`

**crates.io Package Name:**
- `vaas-core`

---

## Cross-Cutting Concerns

### Configuration (vessel.toml)

All packages read shared configuration from `vessel.toml`, housed in the meta-repository. The config defines:
- `[envelope]` — hard bounds per vessel class
- `[entropy]` — per-agent entropy thresholds
- `[memory]` — hologram overhead (default 25%), cryogenic compression ratio
- `[tempo]` — agent tempos and phase-lock reference
- `[constitution]` — initial governance rules
- `[grafting]` — pollen TTL, test duration

### Observability

Every package emits structured logs via `structlog` with a shared JSON schema. Logs feed into:
- `vaas-spectrograph` (real-time CLI monitoring)
- Loki/Grafana (production monitoring)
- On-vessel black box recorder (durable, waterproof storage)

### Security

- `vaas-core` cryptographic signing of all safety-critical messages
- `vaas-grafting` pollen packets signed with vessel identity key
- `vaas-bridges` pheromone field integrity verified by quorum
- `vaas-constitution` amendment logging with hash chain

---

## Summary

This 8-repo structure isolates safety-critical code (Rust `vaas-core`) from the emergent cognition layers (Python pillars), with clear dependency ordering and independent versioning. Each package maps to a distinct cognitive pillar of the VaaS architecture. The monorepo-adjacent model keeps integration manageable while allowing each team to iterate independently. The result is a substrate that is **formally safe at the core**, **emergent and adaptive at the surface**, and **inspectable at every layer**.
