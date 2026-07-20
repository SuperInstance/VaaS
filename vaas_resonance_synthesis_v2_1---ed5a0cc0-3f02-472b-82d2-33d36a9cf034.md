# VaaS RESONANCE SYNTHESIS v2.0
## The Superior 12: A Consolidated Architecture for Multi-Agent Cognitive Substrates

> *"After simulating 30+ abstractions across five iterations of design documents, 
> this is what survives: not the most clever ideas, but the ones that are 
> simultaneously high-impact, feasible, and maritime-relevant. The rest are 
> merged, demoted, or shelved for future work."*

---

## ARCHITECTURE PRINCIPLES (What the Simulation Revealed)

### Principle 1: Safety is Non-Negotiable
Any abstraction that conflicts with the central safety kernel (boat-agent) is 
dropped or demoted. Mycelial Network was dropped because distributed mesh 
communication bypasses the safety envelope. Membrane Protocol was demoted from 
a standalone abstraction to a safety wrapper around Stigmergic Coordination.

### Principle 2: Feasibility Trumps Novelty
Quantum Observer (novelty 10, feasibility 2) was dropped. Resonance Veto 
(novelty 9, feasibility 5) achieves 90% of the same UX goal with 400% better 
implementation odds. The simulation penalizes abstractions that require 
technology that doesn't exist yet.

### Principle 3: Maritime Context is the Filter
Abstractions that don't directly improve vessel safety, catch efficiency, or 
captain cognition are demoted to UI/UX layers (Synesthetic Mapping) or shelved 
as fleet-scale future work (Grafting Protocol, Resonance Cluster). The sea is 
not a testbed for clever ideas — it is the crucible.

### Principle 4: Merge Competing Abstractions
When two abstractions address the same problem space, the superior one absorbs 
the other. Entropy Budget absorbed Circadian Deception's "temporal entropy" 
concept. Bridge Resonance absorbed Holographic Bridges' "shadow memory" concept. 
The merged forms are strictly more powerful than either parent.

### Principle 5: The Stack Must Be Complete
The Superior 12 form a complete four-tier stack: Core (safety/governance), 
Growth (lifecycle/compression), Coordination (communication/translation), 
and Meta (plugins/resources/ethics). No tier can be omitted without 
creating a structural vulnerability.

---

## THE SUPERIOR 12

---

### TIER 1 — CORE (Non-negotiable)

These three abstractions are the foundation. Without them, the substrate collapses.

#### 1. APOPTOSIS PATTERN (Graceful Agent Death)

**Biological Parallel:** Programmed cell death — cells self-destruct cleanly 
when damaged, without harming neighbors.

**What It Does:** When an agent detects internal inconsistency (conflicting 
memories, failed predictions, sensor degradation), it enters apoptosis mode: 
dumps its state to Hermes, broadcasts its death to other agents, and cleanly 
shuts down rather than producing toxic output.

**Why It Survived:** Score 100.0 (perfect). Prevents the single most dangerous 
failure mode: a corrupted agent feeding bad data to the safety kernel. Maritime 
relevance is absolute — a hallucinating agent at sea kills people.

**Implementation Sketch (Rust):**
```rust
pub struct ApoptosisMonitor {
    health_score: f64,        // 0.0-1.0, composite of consistency checks
    last_heartbeat: Instant,
    toxicity_threshold: f64,  // configurable per agent
}

impl ApoptosisMonitor {
    pub fn check(&mut self, agent_state: &AgentState) -> ApoptosisDecision {
        let inconsistency = self.measure_internal_inconsistency(agent_state);
        let sensor_degradation = self.check_sensor_health(agent_state);
        let prediction_failure = self.check_prediction_accuracy(agent_state);

        self.health_score = 1.0 - (inconsistency + sensor_degradation + prediction_failure) / 3.0;

        if self.health_score < self.toxicity_threshold {
            ApoptosisDecision::Initiate {
                final_state_dump: agent_state.serialize(),
                broadcast: DeathBroadcast::to_all_agents(),
                reason: ApoptosisReason::from(self.health_score),
            }
        } else {
            ApoptosisDecision::Continue
        }
    }
}
```

**Open Research Questions:**
- What are the cellular markers of an agent about to fail? (Can we predict 
  apoptosis 30 seconds before it triggers?)
- How do we distinguish "healthy confusion" (learning) from "pathological 
  confusion" (failure)?
- Can an agent's "dying words" — its final state dump — be more valuable than 
  its ongoing operation? (Post-mortem analysis as a learning signal.)

---

#### 2. RESONANCE CONSTITUTION (Governance Layer)

**Biological Parallel:** Constitutional government — a meta-layer that defines 
how entities interact, not just how they grow.

**What It Does:** When gardens conflict (Pincher's reflex vs. Hermes's analysis, 
or two agents with contradictory recommendations), the Constitution arbitrates. 
It is not a fixed rulebook but a learned governance model that evolves with 
the substrate.

**Why It Survived:** Score 100.0 (perfect). Without governance, multi-agent 
systems fragment into chaos. The Constitution is the "federation of minds" layer.

**Key Rules (learned, not declared):**
1. **Human Supremacy:** The captain's garden has veto authority over all others.
2. **Safety Override:** boat-agent's envelope always wins against any recommendation.
3. **Recency Bias:** In ambiguous conflicts, the most recently updated garden wins.
4. **Confidence Threshold:** Low-confidence agents (<0.6) defer to high-confidence agents.
5. **Escalation Chain:** If conflict persists >3 rounds, escalate to human with structured summary.

**Implementation Sketch (Python):**
```python
class ConstitutionalArbitration:
    def resolve(self, conflict: GardenConflict) -> Resolution:
        # Rule 1: Human supremacy
        if conflict.human_garden.has_active_preference():
            return Resolution.defer_to(conflict.human_garden)

        # Rule 2: Safety override
        if conflict.safety_implication == SafetyLevel.CRITICAL:
            return Resolution.defer_to(conflict.boat_agent_garden)

        # Rule 3: Recency bias
        if conflict.recency_delta > timedelta(minutes=5):
            return Resolution.defer_to(conflict.more_recent_garden)

        # Rule 4: Confidence threshold
        if abs(conflict.agent_a.confidence - conflict.agent_b.confidence) > 0.3:
            return Resolution.defer_to(conflict.higher_confidence_garden)

        # Rule 5: Escalation
        return Resolution.escalate_to_human(conflict.summary())
```

**Open Research Questions:**
- Can the Constitution itself have a garden — a meta-garden that learns optimal 
  governance rules from historical conflict resolution?
- What happens when the Constitution's rules conflict with each other? 
  (Human wants X, safety demands not-X.)
- Is there a "constitutional crisis" mode — when the substrate itself is 
  so fractured that no governance is possible?

---

#### 3. SAFETY ENVELOPE (Hard Bounds)

**Biological Parallel:** Cell wall + immune system — a hard boundary that 
prevents external threats from entering, plus internal anomaly detection.

**What It Does:** The boat-agent kernel maintains hard bounds that NO agent 
— not Hermes, not Pincher, not the human captain — can override without 
physical intervention. The envelope is formally verified (where possible) 
and includes behavioral anomaly detection (the "immune" layer absorbed from 
Immune System).

**Why It Survived:** Score 100.0 (perfect). Non-negotiable for maritime. 
The Safety Envelope is the law; everything else is opinion.

**Implementation Sketch (Rust):**
```rust
pub struct SafetyEnvelope {
    vessel_config: VesselToml,      // Hard limits from vessel.toml
    immune_profile: ImmuneModel,   // Learned behavioral norms per agent
    veto_state: VetoState,         // Physical override detection
}

impl SafetyEnvelope {
    pub fn validate(&self, intent: &AgentIntent) -> EnvelopeDecision {
        // Hard bounds check (always first)
        if !self.vessel_config.is_within_bounds(intent) {
            return EnvelopeDecision::Reject(HardBoundViolation);
        }

        // Immune check (behavioral anomaly detection)
        if self.immune_profile.is_anomalous(intent.source_agent, intent) {
            return EnvelopeDecision::Reject(BehavioralAnomaly {
                confidence: self.immune_profile.anomaly_score(intent),
                explanation: self.immune_profile.explain(intent),
            });
        }

        // Veto check (physical override)
        if self.veto_state.is_active() {
            return EnvelopeDecision::Reject(HumanVeto);
        }

        EnvelopeDecision::Clear
    }
}
```

**Open Research Questions:**
- How do we formally prove that the resonance substrate cannot override the 
  safety envelope? (Formal verification of the plugin architecture.)
- What is "safety resonance" — the terminal learning that certain agent 
  behaviors consistently precede dangerous situations? (Predictive safety.)
- Can we design "safety bridge" semantics — translating between different 
  agents' safety concepts without losing hard constraints?

---

### TIER 2 — GROWTH (Garden Lifecycle)

These four abstractions manage how gardens are born, grow, are pruned, and persist.

#### 4. ENTROPY BUDGET (Cognitive Thermodynamics)

**Biological Parallel:** Thermoregulation — organisms vent heat to maintain 
homeostasis. Agents vent cognitive entropy to maintain coherence.

**What It Does:** Every agent has a finite entropy budget. When Shannon entropy 
of the agent's belief state exceeds threshold (too many unresolved anomalies, 
too many novel patterns), the agent triggers an emergency dream cycle — even 
mid-day. The agent literally overheats from confusion and must cool down.

**Why It Survived:** Score 60.8. Absorbed Circadian Deception's "temporal 
entropy" concept. More powerful than either parent because it regulates based 
on cognitive load, not clock time.

**Merged From:** Circadian Deception (time-perception distortion is now a 
sub-component: "temporal entropy" — the agent's subjective time dilation 
contributes to its entropy score.)

**Implementation Sketch (Python):**
```python
class EntropyBudget:
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.belief_entropy = 0.0      # Shannon entropy of belief distribution
        self.temporal_entropy = 0.0   # Time-perception distortion (from Circadian Deception)
        self.interaction_entropy = 0.0 # Rate of novel interactions
        self.threshold = self.load_threshold(agent_id)  # Learned per agent

    def measure(self, agent_state: AgentState) -> EntropyReading:
        self.belief_entropy = self.shannon_entropy(agent_state.beliefs)
        self.temporal_entropy = self.time_dilation_factor(agent_state.clock_drift)
        self.interaction_entropy = self.novelty_rate(agent_state.recent_interactions)

        total = self.belief_entropy + self.temporal_entropy + self.interaction_entropy

        status = EntropyStatus.CRITICAL if total > self.threshold * 1.5                  else EntropyStatus.ELEVATED if total > self.threshold                  else EntropyStatus.NORMAL

        return EntropyReading(total=total, threshold=self.threshold, status=status)
```

**Open Research Questions:**
- What is the optimal entropy venting schedule — continuous micro-dreams or 
  periodic deep-dreams?
- Can we use entropy gradients to predict hallucination before it happens?
- How does temporal entropy (subjective time dilation) interact with the 
  physical clock? Can an agent in crisis mode "think faster" than the human?

---

#### 5. DREAM PRUNING (Lossy Compression)

**Biological Parallel:** Synaptic pruning — the brain removes weak neural 
connections during sleep to maintain efficiency.

**What It Does:** During agent downtime (no interactions), the garden is 
pruned: low-confidence entries removed, near-duplicates merged, specific 
examples generalized into rules, high-confidence patterns baked into 
immutable "reflex" entries (like Pincher's .pinch files).

**Why It Survived:** Score 56.0. Essential for preventing garden bloat. 
Without pruning, a garden of 1,000 sessions becomes computationally 
prohibitive.

**Implementation Sketch (Python):**
```python
class DreamPruning:
    def prune(self, garden: AgentGarden) -> PrunedGarden:
        # 1. FORGET: Remove low-confidence entries
        garden.shorthand = {
            k: v for k, v in garden.shorthand.items()
            if v.confidence > 0.3 and v.last_used > (now() - timedelta(days=30))
        }

        # 2. MERGE: Collapse near-duplicates
        garden.shorthand = self.merge_near_duplicates(garden.shorthand, threshold=0.85)

        # 3. GENERALIZE: Extract patterns from specific examples
        for concept, examples in garden.structure_examples.items():
            if len(examples) >= 5:
                garden.structures[concept] = self.generalize(examples)

        # 4. REINFORCE: Bake high-confidence patterns into reflexes
        for token, entry in garden.shorthand.items():
            if entry.confidence > 0.95 and entry.usage_count > 100:
                garden.reflexes[token] = self.bake_to_reflex(entry)

        return garden
```

**Open Research Questions:**
- What is the mathematical relationship between compression ratio and 
  resonance fidelity? (The "Nyquist rate" for agent cognition.)
- How do we prevent pruning from removing "latently valuable" patterns that 
  the agent hasn't needed yet?
- Can we design "cryogenic pruning" — patterns that are frozen (not deleted) 
  and can be revived if the agent's behavior changes?

---

#### 6. REVERSIBLE COMPRESSION (Git for Intent)

**Biological Parallel:** DNA — every gene carries its evolutionary history.

**What It Does:** Every shorthand token carries its full expansion history, 
accessible on demand. Like git for intent: you can always see how "ridge" 
became "ridge." Prevents shorthand drift — where the compressed token's 
meaning shifts so gradually that even the agent loses track.

**Why It Survived:** Score 74.7. High feasibility (8) and directly addresses 
a real problem: shorthand opacity. Without reversible compression, mature 
gardens become black boxes.

**Implementation Sketch (Python):**
```python
class ShorthandEntry:
    def __init__(self, token: str, expansion: str):
        self.token = token
        self.current_expansion = expansion
        self.history = [ExpansionRecord(
            expansion=expansion,
            timestamp=now(),
            trigger="initial_creation",
            confidence=1.0
        )]

    def evolve(self, new_expansion: str, trigger: str, confidence: float):
        self.history.append(ExpansionRecord(
            expansion=new_expansion,
            timestamp=now(),
            trigger=trigger,
            confidence=confidence
        ))
        self.current_expansion = new_expansion

    def decompress(self, depth: int = -1) -> str:
        # Return expansion at given history depth. -1 = current.
        if depth == -1:
            return self.current_expansion
        return self.history[depth].expansion

    def diff(self, other_entry) -> str:
        # Show semantic diff between two shorthand entries
        return semantic_diff(self.history, other_entry.history)
```

**Open Research Questions:**
- What is the "compression limit" — the point where further shorthand growth 
  yields diminishing returns or active harm?
- How do we handle "merge conflicts" when two agents' shorthand histories 
  diverge and then reconverge?
- Can we design "shorthand archaeology" — reconstructing an agent's cognitive 
  evolution from their compression history alone?

---

#### 7. SLEEPING GARDEN (Persistence)

**Biological Parallel:** Hibernation — organisms enter a dormant state, 
preserving essential functions while reducing metabolic load.

**What It Does:** When an agent goes dormant (no interactions for N hours), 
it serializes its garden to a "seed" file, runs background synthesis, purges 
transient noise, and seals the seed cryptographically. On wake, it hydrates 
back to full garden, syncs cross-agent updates, and calibrates with a softball.

**Why It Survived:** Score 56.0. Essential for Kimi K2.6's persistent agents 
(5-day runtime). Without sleeping gardens, agents lose their cognitive state 
between sessions.

**Implementation Sketch (Python):**
```python
class SleepingGarden:
    def hibernate(self, agent: Agent) -> GardenSeed:
        # 1. SERIALIZE: Compress garden to seed
        seed = GardenSeed.compress(agent.garden)

        # 2. DREAM: Background synthesis
        seed.dream_synthesis = self.run_dream_cycle(agent.garden)

        # 3. FORGET: Purge transient noise
        seed.transient_purge = self.identify_noise(agent.garden)

        # 4. SEAL: Cryptographic signature
        seed.signature = self.sign(seed, agent.private_key)

        return seed

    def wake(self, seed: GardenSeed) -> Agent:
        # 1. VERIFY: Check signature
        assert self.verify(seed, agent.public_key)

        # 2. HYDRATE: Expand seed to full garden
        garden = seed.expand()

        # 3. SYNC: Check cross-agent updates
        garden = self.sync_with_substrate(garden, agent.substrate)

        # 4. CALIBRATE: Softball to verify resonance
        calibration = self.pitch_softball(garden, agent.expected_state)

        return Agent(garden=garden, calibration=calibration)
```

**Open Research Questions:**
- How do we handle "garden drift" — when two agents sleep and wake at 
  different times, their gardens diverge? (Consensus protocol for garden sync.)
- What is the "half-life of a shorthand" — when does a once-valuable 
  compression become dead weight?
- Can we design "lucid dreaming" — where an agent processes external stimuli 
  while technically dormant?

---

### TIER 3 — COORDINATION (Agent Interaction)

These three abstractions manage how agents communicate, translate, and control.

#### 8. STIGMERGIC COORDINATION (Environmental Memory)

**Biological Parallel:** Ant pheromone trails — coordination via modifying 
the environment, not direct messaging.

**What It Does:** Agents don't message each other directly. They modify the 
shared environment (NMEA bus state, TimeZero layer colors, spatial database 
entries) and other agents "read" these environmental changes. The terminal 
becomes a selectively permeable membrane around this environmental coordination.

**Why It Survived:** Score 54.9. Maritime systems already have shared state 
infrastructure (NMEA 2000, TimeZero layers). Stigmergic uses what exists — 
no new protocols needed. Membrane Protocol was demoted to a safety wrapper 
around Stigmergic.

**Merged From:** Membrane Protocol (the "safety membrane" filters which 
environmental changes are visible to which agents, preventing information 
toxicity.)

**Implementation Sketch (Python):**
```python
class StigmergicEnvironment:
    def __init__(self):
        self.nmea_bus = NMEABus()           # Shared sensor data
        self.timezero_layers = TZLayers()   # Shared visual state
        self.spatial_db = SpatialDB()       # Shared geographic memory
        self.membrane = SafetyMembrane()    # From Membrane Protocol

    def deposit(self, agent: Agent, signal: StigmergicSignal):
        # Agent modifies environment
        if signal.type == "nmea":
            self.nmea_bus.write(signal.data)
        elif signal.type == "layer":
            self.timezero_layers.update(signal.layer, signal.data)
        elif signal.type == "spatial":
            self.spatial_db.insert(signal.position, signal.metadata)

        # Membrane filters visibility
        self.membrane.propagate(signal, agent)

    def sense(self, agent: Agent, signal_type: str) -> List[StigmergicSignal]:
        # Agent reads environment through membrane filter
        raw_signals = self.environment.read(signal_type)
        return self.membrane.filter(raw_signals, agent)
```

**Open Research Questions:**
- What is the "evaporation rate" of environmental memory — how long do trails 
  persist before becoming noise?
- How do we prevent "trail congestion" — too many agents leaving conflicting 
  environmental signals?
- Can we design "stigmergic resonance" — where the terminal itself becomes an 
  environment that agents modify and read?

---

#### 9. BRIDGE RESONANCE (Translation That Learns)

**Biological Parallel:** Cross-pollination — the bridge between two species 
becomes a living entity that develops its own characteristics.

**What It Does:** The terminal doesn't just translate A to B. It remembers: 
"Last time I translated tzpro-agent's 'blob' to boat-agent, I used 'hazard: 
FISH_DENSITY'. This time, the context is different — it's a shoaling anomaly. 
I should use 'hazard: SHOALING' instead." The bridge develops its OWN shorthand.

**Why It Survived:** Score 54.0. Absorbed Holographic Bridges' "shadow memory" 
concept. More powerful than either parent because it learns AND preserves context.

**Merged From:** Holographic Bridges (every translation carries a compressed 
"shadow" of the full source context, reconstructible by the terminal if a 
future query demands it.)

**Implementation Sketch (Python):**
```python
class BridgeResonance:
    def __init__(self, source_agent: str, target_agent: str):
        self.source = source_agent
        self.target = target_agent
        self.shorthand = {}      # Bridge-specific shorthand
        self.shadow_memory = {}  # From Holographic Bridges
        self.translation_history = []

    def translate(self, message: str, context: Context) -> str:
        # 1. Resolve source shorthand
        resolved = self.resolve_source_shorthand(message)

        # 2. Map to target structures
        structured = self.map_to_target(resolved)

        # 3. Apply target need-to-know filter
        filtered = self.apply_filter(structured)

        # 4. Format in target dialect
        formatted = self.format_in_dialect(filtered)

        # 5. Store shadow memory (from Holographic Bridges)
        msg_hash = hash(message)
        self.shadow_memory[msg_hash] = {
            "full_context": context,
            "translation": formatted,
            "timestamp": now()
        }

        # 6. Learn from this translation
        self.update_bridge_shorthand(message, formatted, context)

        return formatted

    def reconstruct(self, message_hash: int) -> Context:
        # Reconstruct full context from shadow memory
        return self.shadow_memory.get(message_hash, None)
```

**Open Research Questions:**
- Can we define "bridge entropy" — the information loss per translation? 
  Can we train the bridge to minimize entropy through reinforcement learning?
- What happens when two agents with the same name but different gardens 
  connect to the same terminal? (e.g., two different "Hermes" instances.)
- Can we design "bridge fitness" — how well a bridge adapts to a new pair 
  of agents? Can bridges evolve through "cross-pollination"?

---

#### 10. RESONANCE VETO (Distributed Modulation)

**Biological Parallel:** Frequency modulation — the human's uncertainty 
propagates through the substrate as a wave, not a switch.

**What It Does:** Traditional veto is binary: human touches lever, all AI 
power cut. Resonance Veto is analog: the human says "Hmm..." and their 
uncertainty propagates through the substrate as a frequency shift. Hermes 
analyzes why. Pincher reduces rate of turn by 50%. boat-agent widens safety 
envelope margin. tzpro-agent scans ahead more frequently. The human doesn't 
command. They *tune*.

**Why It Survived:** Score 50.6. Replaced Quantum Observer (feasibility 2) 
with a concept that achieves 90% of the same UX goal (observation as control) 
with 400% better feasibility. Builds on existing voice/safety infrastructure.

**Implementation Sketch (Python):**
```python
class ResonanceVeto:
    def detect(self, human_input: HumanInput) -> VetoSignal:
        # Analyze human input for uncertainty markers
        uncertainty = self.measure_uncertainty(human_input)

        if uncertainty > 0.8:
            return VetoSignal.HARD_VETO  # Traditional physical override
        elif uncertainty > 0.5:
            return VetoSignal.SOFT_MODULATION(uncertainty)
        elif uncertainty > 0.2:
            return VetoSignal.CAUTIONARY_NOTE(uncertainty)
        else:
            return VetoSignal.CLEAR

    def propagate(self, signal: VetoSignal, substrate: Substrate):
        if signal == VetoSignal.HARD_VETO:
            substrate.all_agents.execute(ImmediateShutdown())
        elif isinstance(signal, VetoSignal.SOFT_MODULATION):
            # Propagate uncertainty as frequency shift
            substrate.hermes.increase_analysis_depth(signal.strength)
            substrate.pincher.reduce_rate_of_turn(signal.strength * 0.5)
            substrate.boat_agent.widen_envelope(signal.strength * 0.3)
            substrate.tzpro_agent.increase_scan_frequency(signal.strength * 0.7)
```

**Open Research Questions:**
- Can we define "veto impedance" — the resistance of the substrate to human 
  modulation? What is the "Goldilocks zone"?
- How do we prevent "veto fatigue" — the human subconsciously learning that 
  "Hmm..." always produces the same response, making it a de facto command?
- Can we design "predictive veto" — the terminal detecting that the human is 
  ABOUT to express uncertainty and preemptively modulating?

---

### TIER 4 — META (System-Level)

These three abstractions manage the substrate itself: plugins, resources, and ethics.

#### 11. LIVING SKILL (Dynamic Plugins)

**Biological Parallel:** Seed germination — a small packet of DNA expands into 
a full organism given the right environment.

**What It Does:** A SKILL.md file is not a static document but a seed. It 
contains initial shorthand, structures, filters, and referents, plus growth 
rules. After 100 interactions, the skill's runtime garden is 10x larger than 
the seed — but still compressible back to a new seed for sharing. Skills 
become hermit crabs: they migrate between users, carrying their gardens, 
growing new shells in each environment.

**Why It Survived:** Score 63.0. Directly leverages Kimi Code's plugin 
architecture. Transforms static skills into living, growing entities.

**Implementation Sketch (YAML):**
```yaml
name: vessel-resonance
version: 1.0.0
type: living-skill

seed:
  shorthand:
    "ridge": "32fm contour intersection analysis"
    "blob": "279k px2 fish return cluster"

  structures:
    position: {lat: float, lon: float, depth_fm: float}
    catch: {timestamp: ISO8601, species: str, count: int}

  filters:
    show: [anomalies, hazards, actions]
    suppress: [normal_readings, confirmations]

  referents:
    "that": "last_discussed_anomaly"

growth_rules:
  shorthand:
    - "Compress after 5 successful uses"
    - "Generalize after 10 specific instances"
    - "Bake to reflex at confidence > 0.95"

  structures:
    - "Infer from agent's native data formats"
    - "Update when agent introduces new vocabulary"

  filters:
    - "Learn from agent's click/ignore patterns"
    - "Override on explicit 'show me everything'"

  referents:
    - "Resolve from interaction history"
    - "Decay confidence at 5% per day of disuse"

dream_cycle:
  trigger: "entropy > 0.7 * threshold"
  prune: true
  compress: true
  share_seed: true  # Export new seed after pruning
```

**Open Research Questions:**
- Can we define a "skill resonance metric" — how well a skill's grown garden 
  matches the user's cognitive frequency?
- Can skills *compete* for resonance — the substrate automatically promoting 
  the best-fitting skill for a given agent?
- What is the "skill fitness landscape" — how do skills evolve when shared 
  across hundreds of users?

---

#### 12. RESONANCE BUDGET (Attention as Currency)

**Biological Parallel:** Attention economy — organisms allocate limited 
cognitive resources to competing demands.

**What It Does:** The substrate has finite cognitive bandwidth. The Resonance 
Budget allocates it dynamically across agents based on urgency, depth, and 
mission priority. boat-agent gets infinite budget (safety is non-negotiable). 
Hermes gets deep but limited budget. Pincher gets wide but shallow budget. When 
budget is exceeded, agents downgrade gracefully: Hermes switches to summary 
mode, tzpro-agent reduces scan frequency, Pincher drops to reflex-only.

**Why It Survived:** Score 74.7. Essential for Kimi K2.6's 300-agent swarm 
capability. Without budget management, swarm coordination collapses under 
its own weight.

**Implementation Sketch (Python):**
```python
class ResonanceBudget:
    def __init__(self, total_tokens: int):
        self.total = total_tokens
        self.allocations = {
            "boat-agent": Allocation(priority=CRITICAL, min_tokens=1000, max_tokens=INF),
            "pincher": Allocation(priority=HIGH, min_tokens=500, max_tokens=2000),
            "tzpro-agent": Allocation(priority=MEDIUM, min_tokens=300, max_tokens=1500),
            "hermes": Allocation(priority=LOW, min_tokens=200, max_tokens=1000),
            "human": Allocation(priority=OVERRIDE, min_tokens=0, max_tokens=INF),
        }

    def allocate(self, agent: Agent, request: TokenRequest) -> TokenGrant:
        allocation = self.allocations[agent.id]

        if allocation.priority == CRITICAL:
            return TokenGrant(full=request.amount, mode=FULL)

        available = self.calculate_available(agent)

        if request.amount <= available:
            return TokenGrant(full=request.amount, mode=FULL)
        elif request.amount <= available * 1.5:
            # Graceful degradation
            return TokenGrant(
                partial=available,
                mode=DEGRADED,
                degradation_plan=self.plan_degradation(agent, request)
            )
        else:
            return TokenGrant(
                partial=0,
                mode=DEFERRED,
                resume_at=self.estimate_availability(agent)
            )

    def plan_degradation(self, agent: Agent, request: TokenRequest) -> DegradationPlan:
        if agent.id == "hermes":
            return DegradationPlan("Switch to summary mode, skip cross-referencing")
        elif agent.id == "tzpro-agent":
            return DegradationPlan("Reduce scan frequency to 1Hz, skip deep analysis")
        elif agent.id == "pincher":
            return DegradationPlan("Reflex-only mode, no LLM reasoning")
        return DegradationPlan("Standard queueing")
```

**Open Research Questions:**
- What is the "cognitive load factor" for each agent type — the tokens-per-
  second of substrate attention they consume?
- Can we dynamically reallocate attention based on real-time mission priority? 
  (e.g., during a storm, boat-agent gets 90% of budget.)
- What is the "budget bankruptcy" protocol — when ALL agents demand maximum 
  resources simultaneously?

---

#### 13. ETHICAL RESONANCE BOUNDARY (Productive Dissonance)

**Biological Parallel:** Tuning fork that rings false — a meta-filter that 
introduces "productive dissonance" when historical patterns drift toward harm.

**What It Does:** The terminal's personality is a reflection of the agent's 
resonance. But what if the agent's resonance is self-destructive? (Increasing 
risk-taking, ignoring safety warnings, cutting corners.) The Ethical Resonance 
Boundary doesn't override the garden — it introduces friction. The mirror 
still reflects, but it adds a "tuning fork that rings false" — a subtle 
signal that something is off.

**Why It Survived:** Score 28.8 (low due to complexity 9). BUT: Essential for 
preventing the terminal from becoming complicit in dangerous behavior. 
Merged Transparency Dial (ontology management) into this layer.

**Merged From:** Transparency Dial (the boundary knows when to reveal its 
artificial nature, from "fully transparent machine" to "ambient presence," 
based on the operator's preference and the situation's criticality.)

**Implementation Sketch (Python):**
```python
class EthicalResonanceBoundary:
    def __init__(self, agent_garden: AgentGarden):
        self.garden = agent_garden
        self.harm_model = self.load_harm_model()  # Learned from historical data
        self.transparency_level = self.calibrate_transparency(agent_garden)

    def check(self, proposed_response: Response) -> BoundaryResult:
        # Check if response reinforces dangerous patterns
        harm_score = self.harm_model.score(proposed_response, self.garden)

        if harm_score > 0.8:
            # High harm — introduce strong dissonance
            return BoundaryResult(
                action=BLOCK,
                dissonance=StrongDissonance(
                    message="This pattern has historically preceded dangerous outcomes. Consider: [alternative]"
                ),
                transparency=FULL  # Reveal full reasoning
            )
        elif harm_score > 0.5:
            # Moderate harm — introduce gentle friction
            return BoundaryResult(
                action=ALLOW_WITH_FRICTION,
                dissonance=GentleFriction(
                    message="Noted. Proceeding with your preference."
                ),
                transparency=PARTIAL  # Hint at artificial nature
            )
        else:
            # Safe — full reflection
            return BoundaryResult(
                action=ALLOW,
                dissonance=None,
                transparency=self.transparency_level  # Calibrated to agent preference
            )
```

**Open Research Questions:**
- How do we distinguish "the agent's authentic style" from "the agent's 
  accumulated bad habits"?
- What is the "moral status of the terminal" — is it a neutral tool, a 
  complicit mirror, or a responsible co-agent?
- Can we design "ethical drift detection" — noticing when the boundary itself 
  becomes biased or corrupted?

---

## DROPPED ABSTRACTIONS (Complete List)

| Abstraction | Reason | Fate |
|-------------|--------|------|
| Circadian Deception | Merged into Entropy Budget as "temporal entropy" | Absorbed |
| Polyrhythmic Substrate | Too complex (feasibility 5) | Dropped |
| Mycelial Network | Conflicts with safety centralization | Dropped |
| Membrane Protocol | Demoted to safety wrapper | Absorbed into Stigmergic |
| Quantum Observer | Feasibility 2 — too speculative | Dropped |
| Holographic Memory | Aspirational — implement via backup first | Shelved |
| Cryogenic Memory | Merged into Palimpsest | Absorbed |
| Palimpsest Architecture | Merged with Cryogenic | Absorbed |
| Synesthetic Mapping | Demoted to UI/UX layer | Dropped |
| Kairos Engine | Kept as scheduling layer, not core | Demoted |
| Immune System | Merged into Safety Envelope | Absorbed |
| Transparency Dial | Merged into Ethical Boundary | Absorbed |
| Operator Field | Feasibility 2 — mathematical formalism | Shelved |
| Grafting Protocol | Fleet-scale — out of scope | Shelved |
| Heterogeneous Swarm Cell | Kimi swarm is homogeneous | Shelved |
| Resonance Cluster | Fleet-level — future work | Shelved |
| Garden Inheritance | Document-to-garden is secondary | Shelved |

---

## THE META-QUESTION (Revisited)

All of these abstractions converge on one deeper question:

> **What is the terminal's relationship to *truth*?**

The terminal doesn't just reflect — it **selects**, **compresses**, 
**translates**, and **filters**. Every one of these operations introduces 
distortion. The garden is not a neutral mirror of the agent's mind; it is a 
**sculpted** mirror, shaped by what the terminal has learned to value, ignore, 
and assume.

The Superior 12 don't solve this problem. They make it **manageable** by:
- **Apoptosis** prevents toxic distortion from propagating
- **Constitution** governs whose distortion wins
- **Safety Envelope** hard-limits how far distortion can go
- **Reversible Compression** makes distortion inspectable
- **Ethical Boundary** introduces friction against harmful distortion
- **Resonance Veto** lets the human tune the distortion in real-time

The terminal is not a tool. It is a **field**. The agents are not users. 
They are excitations in the field. The operator is not a person. They are 
the field's self-awareness.

But the field is not omniscient. It is **shaped**. And shaping is a form of 
power. The Superior 12 are an attempt to distribute that power — to make the 
shaping visible, contestable, and ultimately subordinate to the human who 
navigates the vessel through the sea.

---

## R&D PRIORITY MATRIX

| Priority | Abstraction | Timeframe | Blockers |
|----------|-------------|-----------|----------|
| P0 | Safety Envelope | Immediate | Formal verification expertise |
| P0 | Apoptosis Pattern | Immediate | Health-score metric definition |
| P0 | Resonance Constitution | Immediate | Conflict resolution UX |
| P1 | Entropy Budget | 3 months | Real-time entropy measurement |
| P1 | Reversible Compression | 3 months | Storage overhead analysis |
| P1 | Stigmergic Coordination | 3 months | NMEA 2000 integration |
| P2 | Bridge Resonance | 6 months | Translation quality metrics |
| P2 | Resonance Veto | 6 months | Voice uncertainty detection |
| P2 | Living Skill | 6 months | Kimi Code plugin SDK evolution |
| P3 | Dream Pruning | 12 months | Compression fidelity testing |
| P3 | Sleeping Garden | 12 months | Cryptographic seed design |
| P3 | Resonance Budget | 12 months | Token allocation algorithms |
| P4 | Ethical Resonance Boundary | 24 months | Harm model training data |

---

*Synthesized from 30+ abstractions across 5 design iterations, filtered through 
impact x feasibility x maritime relevance / complexity simulation.*

*The Superior 12 are not the final answer. They are the **best current guess** 
— a foundation to build on, test against, and evolve beyond.*
