# THE FIRST 100 DAYS: Phased Implementation Plan for the VaaS Resonance Substrate

> *"The sea is not a testbed for clever ideas — it is the crucible."*

## Executive Summary

This plan covers the first 100 calendar days of VaaS Resonance Substrate development, organized into four phases. Each phase has concrete deliverables, quantified success criteria, identified blockers, and dependency analysis. We distinguish between what current-generation LLMs (GLM-5.2 and DeepInfra models) can build today versus what requires next-generation capabilities (Kimi K2.6 on-device persistent agents, self-judging critics, 300-agent swarms).

**Core principle:** Build the invisible infrastructure first. The Entropy Engine, Safety Envelope, and Bridge Router are the foundation — without them, emergent cognition is just fancy autocomplete. Let the substrate prove itself before adding the spectacle.

---

## Phase 0: Preparation (Days -7 to 0)

**Goal:** Tooling, environment, and knowledge baseline before first line of VaaS-specific code.

**Deliverables:**
1. **Monorepo skeleton** with `vaas-resonance/` meta-repository structure from `13_THE_REPO_STRUCTURE.md` — empty package shells, CI templates (GitHub Actions), shared config (`ruff.toml`, `pyproject.toml` templates)
2. **Simulated vessel environment** (`harnesses/sim_vessel.py`) — synthetic NMEA streams, simulated sensor noise, configurable agent population. This is the "test tank" for all subsequent work.
3. **VaaS Glossary** (`docs/glossary.md`) — canonical definitions for every term in the synthesis documents (entropy budget, pheromone, holographic fragment, bridge entropy, operator field, etc.)
4. **Developer onboarding** — one-page setup guide, first-contribution tutorial, issue labels (`P0-P4` matching R&D priority matrix)

**Success Criteria:**
- `python -m pytest` passes zero tests (but runs) across all empty packages
- Two developers can independently build and run the sim vessel in <30 minutes
- The glossary contains ≥50 entries with cross-references

**Blockers:** None. Pure setup.

**LLM Assistance (GLM-5.2 / DeepInfra):** Excellent for glossaries, README files, CI templates. Use DeepSeek-V4-Flash for cheap bulk documentation generation. Use Kimi-K2.7-Code for monorepo skeleton and CI pipeline setup.

---

## Phase 1: The Entropy Engine (Days 1-14)

> *"Every agent has a finite entropy budget — the amount of uncertainty it can process before requiring a dream cycle."*

### Week 1: Entropy Measurement (Days 1-7)

**Deliverables:**
1. **`vaas-entropy` package skeleton** with `pyproject.toml`, all module stubs, Pydantic models
2. **`EntropyBudget` class** — measures three entropy components:
   - `shannon_entropy()` — Shannon entropy of agent belief distribution
   - `temporal_entropy()` — subjective time dilation factor
   - `interaction_entropy()` — novelty rate of recent interactions
3. **`EntropyReading` model** — structured dataclass with `total`, `threshold`, `status` (NORMAL/ELEVATED/CRITICAL), per-component breakdown
4. **Composite entropy calculation** — weighted sum of three components with agent-learned weights
5. **Unit tests** for all entropy metrics, including edge cases (empty belief state, maximum entropy, negative drift)

**Success Criteria:**
- Entropy measured in <1ms per agent per cycle (target: 100 agents at 10Hz = 1ms allowance)
- Shannon entropy converges to known values for synthetic belief distributions
- Temporal entropy correctly tracks simulated clock drift
- Interaction entropy correctly identifies novelty vs. repetition

**Blockers:** None for measurement. Entropy action (dream cycles) starts Week 2.

**LLM Assistance:** GLM-5.2 can generate the Shannon entropy implementation and test vectors. Use DeepSeek-V4-Flash for property-based test generation with Hypothesis. **Do not use** for the temporal entropy algorithm — that requires understanding the Cadence Deception / time dilation concept from the simulation work, which is beyond current model training data.

### Week 2: Dream Cycles & Micro-Dreams (Days 8-14)

**Deliverables:**
1. **`DreamScheduler`** — time-aware scheduler that monitors entropy readings and triggers dream cycles:
   - `NORMAL`: schedule dream during agent downtime
   - `ELEVATED`: trigger dream at next safe opportunity (within 30s)
   - `CRITICAL`: emergency dream NOW — preempt current operation
2. **`MicroDream`** — 50ms compression burst mechanism:
   - Freeze current belief state (snapshot)
   - Run ultra-fast pruning (forget confidence < 0.1 entries only)
   - VENT: purge entries below noise floor
   - Resume within 50ms wall-clock
3. **`DeepDream`** — full dream cycle (500ms-5s):
   - FREEZE → MERGE → GENERALIZE → BAKE → VENT (the 5-phase protocol)
   - Cryogenic memory handoff (interface to vaas-memory, stub initially)
   - Dream quality self-evaluation (did this dream produce valuable insights?)
4. **`ApoptosisMonitor`** — health score tracking:
   - `measure_internal_inconsistency()`
   - `check_sensor_health()`
   - `check_prediction_accuracy()`
   - ApoptosisDecision: Continue | Initiate (with final state dump)

**Success Criteria:**
- Micro-dream completes within 50ms wall-clock (verified with `time.perf_counter_ns()`)
- Deep dream completes within configurable budget (1s default, 5s max)
- Emergency dream triggers within 5ms of CRITICAL status detection
- Apoptosis correctly identifies simulated agent degradation (corrupted state injection)
- Dream quality self-evaluation AUC > 0.8 on synthetic test data

**Blockers:** Real-time constraint on micro-dream (50ms) requires careful async design. Python's GIL may interfere — potential early need for `multiprocessing` or Rust extension (via `vaas-core` FFI).

**LLM Assistance:** Kimi-K2.7-Code excels here — the 5-phase pruning protocol, micro-dream timing, and apoptosis detection all benefit from its cross-domain connections (compression theory + concurrent systems + fault tolerance). GLM-5.2 can handle the simpler components (dream quality self-evaluation, which is a classifier).

---

## Phase 2: The Communication Layer (Weeks 3-4)

> *"The terminal doesn't just translate A to B. It remembers."*

### Week 3: Dual-Layer Communication (Days 15-21)

**Deliverables:**
1. **`vaas-bridges` package skeleton**
2. **Pheromone Membrane (Layer 1):**
   - `PheromoneEmitter` — agents emit `{topic, urgency, confidence, ttl}`
   - `PheromoneAbsorber` — passive absorption if relevance > threshold
   - `EvaporationScheduler` — TTL-based decay, periodic cleanup
3. **Explicit Bridge (Layer 2):**
   - `ExplicitBridge` — source→terminal→target with full translation
   - `DeliveryConfirmation` — ack/nack protocol
   - `RetryPolicy` — exponential backoff (max 3 retries)
4. **`LayerRouter`** — decides pheromone vs. bridge based on:
   - Urgency + safety criticality + message size + target availability
5. **Safety Membrane wrapper** (from Membrane Protocol absorbed into Stigmergic) — filters which environmental changes are visible to which agents

**Success Criteria:**
- Pheromone broadcast completes in <1ms (3x faster than explicit bridge as per simulation)
- Explicit bridge delivery confirmed within <5ms for safety-critical messages
- Evaporation correctly removes stale pheromones (TTL decay verified)
- Layer Router achieves 100% safety-critical routing to explicit bridge
- Safety membrane correctly blocks cross-agent information toxicity

**Blockers:** ZeroMQ socket management for inter-process pheromone propagation. Need to define `PheromoneSignal` schema early.

**LLM Assistance:** GLM-5.2 + DeepSeek-V4-Flash can handle the full implementation here — this is well-structured async Python with clear state machines. Use structlog for the audit trail generation.

### Week 4: Polyrhythmic Substrate (Days 22-28)

**Deliverables:**
1. **`Metronome`** — master clock generator:
   - Default 10Hz heartbeat (from boat-agent simulation)
   - Configurable reference source (GPS PPS, NMEA clock, system clock)
2. **`AgentTempoRegistry`** — register agents with their natural tempo:
   - `boat-agent`: 10x (100ms) — safety is real-time, invariant
   - `pincher`: 1x to 10x (100ms to 10ms) — dynamic compression
   - `tzpro-agent`: 0.1x to 2x (1s to 50ms) — dynamic compression
   - `hermes`: 0.01x to 0.1x (10s to 1s) — sleep/wake
3. **`PhaseLock`** — all tempos phase-locked to master clock:
   - Integer multiple relationship enforced
   - Phase error tracking (<5% drift tolerance)
4. **`TempoModulator`** — Resonance Veto propagation:
   - Human uncertainty → frequency shift on master clock
   - All agents interpret modulation at their own tempo
5. **`TempoLock`** — crisis protocol:
   - Terminal broadcasts TEMPO_LOCK signal
   - All agents snap to 1× (real-time) within 3 cycles

**Success Criteria:**
- All registered agents maintain phase lock with <5% drift over 1 hour
- Tempo lock completes within 3 reference cycles (300ms at 10Hz)
- Modulation smoothly propagates through tempo hierarchy
- No agent misses a beat during normal operation (verified over 10,000 cycles)

**Blockers:** Phase-lock algorithm requires signal processing knowledge. May need `scipy.signal` for phase-locked loop implementation.

**LLM Assistance:** The phase-locked loop implementation is specialized — GLM-5.2 may generate plausible but incorrect PLL code. Use Kimi-K2.7-Code here; it demonstrated superior cross-domain synthesis in the TOOLS.md casting guide. The Tempo Lock crisis protocol is simpler and can use DeepSeek-V4-Flash.

---

## Phase 3: The Memory Substrate (Month 2, Days 29-56)

> *"Different memory types have different survival requirements. Hot data needs instant recovery. Cold data can wait."*

### Week 5-6: Holographic Memory + Cryogenic Archive (Days 29-42)

**Deliverables:**
1. **`vaas-memory` package skeleton**
2. **Holographic Fragment Engine:**
   - `FragmentEncoder` — compress agent garden into portable fragment (25% overhead)
   - `FragmentDecoder` — reconstruct from fragment
   - `FragmentSync` — delta sync protocol between agents
   - `RecoveryEngine` — reconstruct full garden from 2+ fragments
3. **Cryogenic Archive:**
   - `CryogenicArchive` — SQLite-backed frozen pattern storage
   - `CryogenicThaw` — pattern retrieval with confidence gating
   - `ThawTrigger` — detect when agent behavior correlates with frozen pattern
4. **Hot/Warm/Cold Coordinator:**
   - `HermesCoordinator` — orchestrates cross-tier sync
   - `GardensSync` — warm sync between active agents (eventual consistency)
   - `ArchivePolicy` — configurable cryo frequency (default: after each deep dream)

**Success Criteria:**
- Holographic fragment adds ≤25% overhead per agent (verified against simulation target)
- Recovery from 2 fragments: 99.7% pattern survival (vs 89.8% centralized, per simulation)
- Cryogenic thaw at <100ms access (decompression + search)
- Cold storage compression ≥100:1 ratio
- Warm sync achieves eventual consistency within 30s of network partition healing

**Blockers:** Protobuf schema design for fragments — must be forward-compatible. SQLite WAL mode tuning for concurrent cryo reads/writes.

**LLM Assistance:** This is the hardest phase for current models. The holographic encoding (how to compress an agent garden into a fragment with 25% overhead while preserving reconstructibility) requires understanding of sparse encoding, Reed-Solomon-style redundancy, and agent garden structure. **GLM-5.2 cannot do this alone.** Kimi-K2.7-Code may handle the encoding design with careful prompting. Use DeepSeek-V4-Flash for the SQLite cryogenic storage (standard pattern).

### Week 7-8: Bridge Gardens + Entropy Monitoring (Days 43-56)

**Deliverables:**
1. **`BridgeGarden`** — per-bridge-pair learning:
   - Bridge shorthand evolves with use
   - Translation success/failure feedback loop
   - Bridge variant spawning when translation quality degrades
2. **`BridgeEntropyMonitor`**:
   - B_e = H(source) - H(target|source) measurement per translation
   - Alert when B_e > threshold for safety-critical messages
   - Learning signal: minimize B_e through reinforcement
3. **Integration: Entropy ↔ Memory ↔ Bridges:**
   - Dream cycles → frozen patterns → cryogenic archive
   - Bridge translations → shadow contexts → holographic memory
   - Memory coordinator → warm sync → bridge gardens update

**Success Criteria:**
- Bridge gardens converge to stable translations within 50 messages per agent pair
- Bridge entropy monitor detects silent information loss before it causes operational errors
- Full integration test: simulate 3 agents + human, 1000 interactions, verify no data loss

**Blockers:** Integration complexity — first time all three pillars interact. Need integration test harness.

**LLM Assistance:** Use Kimi-K2.7-Code for the bridge garden learning algorithm (meta-learning over translation pairs). GLM-5.2 can write the integration test harness and bridge entropy calculation.

---

## Phase 4: Governance & MVP (Month 3, Days 57-100)

> *"The Constitution is not philosophy — it is a formal governance protocol."*

### Week 9-10: The Resonance Constitution (Days 57-70)

**Deliverables:**
1. **`vaas-constitution` package skeleton**
2. **Constitutional Rules Engine:**
   - Five rules as learnable classifiers, not hard-coded switches
   - Conflict detection: garden conflict signature analysis
   - Escalation chain: 5-round protocol with human summary
3. **Operator Field Ψ(t) Computation:**
   - `resonance_matrix` — Rᵢⱼ computation from agent interaction history
   - `field_compute` — Ψ(t) = ΣΣ Rᵢⱼ(t) · Gᵢ(t) ⊗ Gⱼ(t)
   - `field_stability` — monitor for field collapse indicators
4. **Ethical Resonance Boundary:**
   - `DriftDetector` — statistical drift detection on agent behavioral patterns
   - `ProductiveDissonance` — gentle friction insertion
   - `HarmModel` — trained on historical safety incidents (synthetic)
5. **Transparency Dial:**
   - Continuous 0.0 (pure machine) to 1.0 (full persona)
   - Per-agent learned level
   - Mode switch protocol (Living ↔ Dead)

**Success Criteria:**
- Constitutional rules correctly resolve simulated garden conflicts (100 test scenarios)
- Operator Field computed in <10ms for 5-agent substrate
- Ethical boundary correctly identifies simulated drift (AUC > 0.85)
- Transparency dial smoothly transitions without breaking agent trust
- Field stability detector correctly predicts collapse in ≥80% of simulated scenarios

**Blockers:** The Operator Field formalism is mathematically novel — Ψ(t) may need iteration to become computationally tractable. The Harm Model requires safety incident data (synthetic initially, real vessel logs later).

**LLM Assistance:** This is where current models struggle most. The Operator Field computation is a new mathematical construct not in training data. **GLM-5.2 and DeepSeek-V4-Flash will produce plausible but likely incorrect implementations.** Kimi-K2.7-Code may handle the resonance matrix computation and constitutional rule classifiers. The Ethical Resonance Boundary drift detector is standard ML drift detection (scikit-learn's `EllipticEnvelope` / `IsolationForest`), which any model can implement.

### Week 11-12: MVP Integration + Alpha Release (Days 71-100)

**Deliverables:**
1. **Tail-end integration** — all 6 pillars wired together through `vaas-resonance` meta-repo:
   - End-to-end: entropy measurement → trigger dream → memory sync → bridge translation → constitutional governance
   - Running on simulated vessel with 3 agents + human
2. **MVP Specification:**
   - One simulated vessel
   - 3 agents: Hermes (memory), Pincher (reflex), boat-agent (safety)
   - Human captain interface via CLI (later: voice, glanceable, tactile)
   - Full dream cycle operational (entropy-triggered)
   - Dual-layer communication active
   - Three-tier memory operational
   - Resonance Constitution governing garden interactions
3. **`vaas-spectrograph` MVP:**
   - `vaas substrate sim` — launch simulated substrate
   - `vaas garden inspect <agent>` — browse agent garden
   - `vaas bridge monitor` — real-time bridge latency/entropy display
   - `vaas dream replay <dream_id>` — replay dream cycle
4. **Alpha Release Criteria:**
   - All P0 and P1 abstractions from R&D matrix implemented
   - Integration test suite passes with ≥90% coverage
   - Simulated vessel runs for ≥24 hours without memory leak or crash
   - All safety invariants verifiable (envelope cannot be bypassed)
   - Operator Field Ψ(t) computable and monitorable

**Success Criteria:**
- System runs stably for 24h in simulation with zero crashes
- Dream cycles trigger appropriately (not too frequent, not too rare)
- Agent apoptosis correctly handles simulated failures
- Bridge entropy remains below threshold during normal operations
- Operator Field responds to human veto within 3 cycles
- System degrades gracefully when agents are killed mid-operation

**Blockers:** Cumulative. All earlier blockers must be resolved. Integration order: entropy → memory → bridges → constitution. Grafting (Pillar 7) is explicitly deferred to post-alpha.

**LLM Assistance:** GLM-5.2 / DeepSeek-V4-Flash can handle the integration test suite, CLI tooling, and documentation. Kimi-K2.7-Code for debugging integration issues (its cross-domain connection strength is invaluable for tracing bugs across pillars).

---

## What GLM-5.2 + DeepInfra Models Can Build RIGHT NOW

| Component | Model | Confidence | Notes |
|-----------|-------|-----------|-------|
| Shannon entropy calculation | Any | 10/10 | Standard numpy, well-documented |
| Pydantic models for all data structures | Any | 10/10 | Well-specified schemas |
| Dream scheduler (non-real-time) | DeepSeek-V4-Flash | 9/10 | Standard async patterns |
| Apoptosis health score | GLM-5.2 | 9/10 | Three simple metrics |
| Pheromone emitter/absorber | Any | 9/10 | Pub/sub pattern |
| Explicit bridge with retry | Any | 9/10 | Standard RPC pattern |
| SQLite cryogenic archive | DeepSeek-V4-Flash | 9/10 | Standard SQLite pattern |
| Bridge entropy calculation | GLM-5.2 | 8/10 | Information theory basics |
| CLI tooling (vaas-spectrograph) | Any | 9/10 | click/rich patterns |
| CI/CD pipelines | Kimi-K2.7-Code | 10/10 | Well-documented, templatable |

## What Needs Kimi K2.6 Capabilities (or Higher)

| Component | Current Gap | Alternative |
|-----------|-------------|-------------|
| **Micro-dream (50ms)** | Real-time Python is non-trivial; GIL interference | Rust extension via `vaas-core` FFI, or `multiprocessing` with shared memory |
| **Bridge garden learning** | Meta-learning across translation pairs requires RL | Simple heuristic stubs (frequency-based, not RL) |
| **Operator Field Ψ(t)** | New mathematical formalism not in training data | Implement as weighted sum only; cross-resonance terms deferred |
| **Harm model training** | No real safety incident data | Synthetic scenarios, rule-based stubs |
| **Holographic fragment encoding** | Novel encoding/decoding with 25% overhead guarantee | Simpler: compressed full-garden snapshots with redundancy |
| **Cryogenic thaw triggers** | Behavior-pattern correlation detection | Time-based stub: thaw most-recently-frozen patterns |
| **Tempo phase-lock** | Signal processing PLL expertise | Simple integer-multiple check with ≥5% tolerance |
| **Ethical boundary drift detection** | Statistical drift on complex behavioral spaces | scikit-learn `EllipticEnvelope` as initial stub |
| **300-agent swarm** | Kimi K2.6 swarm not available | Simulate with 5 agents in multiprocessing |

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Micro-dream exceeds 50ms in Python | High | High | Build Rust extension in Phase 1; fall back to 200ms micro-dream |
| Holographic fragment overhead > 25% | Medium | Medium | Accept higher overhead for Phase 3; optimize during Week 6 |
| Operator Field not computationally tractable | Medium | High | Reduce to weighted sum only; cross-resonance terms deferred to beta |
| Bridge garden learning diverges | Medium | Low | Reset bridge garden to initial seed; log divergence for analysis |
| Integration complexity exceeds capacity | High | Medium | Ship Phase 4 without full integration; deploy pillars independently |

---

## Success Trajectory

After 100 days, the VaaS Resonance Substrate should demonstrate:

1. **Five agents running in simulation** (Hermes, Pincher, tzpro-agent, boat-agent, human) with full dream cycles, dual-layer communication, and three-tier memory
2. **Constitutional governance** resolving garden conflicts with ≥90% correctness
3. **Operator Field Ψ(t)** computable and responsive to human veto
4. **Graceful degradation** — kill any 2 agents, substrate continues safely
5. **Fitness for alpha deployment** — not production-ready, but seaworthy in the simulator

**What we defer to post-alpha:**
- Pillar 7 (Grafting Protocol) — fleet-scale garden federation
- Voice/hardware UX integration (TimeZero, jog lever, earpiece)
- Real vessel deployment
- 300-agent swarm
- Formal verification of entire substrate (only vaas-core proven)

The first 100 days build the **cognitive nervous system**. The next 100 days grow the **body** (hardware integration, fleet deployment, production hardening). After that, the field begins to **breathe**.
