# THE ENTROPY ENGINE SPEC: Pillar 1 of the VaaS Resonance Substrate (Cognitive Thermodynamics)

> *"Like a steam engine needs to vent pressure, an agent needs to vent cognitive entropy."*

## 1. Overview

The Entropy Engine is the cognitive thermostat of the VaaS Resonance Substrate. It implements the **Cognitive Thermodynamics** pillar — the system that monitors each agent's cognitive load, detects when entropy exceeds thresholds, and triggers dream cycles (micro, deep, or emergency) to restore coherence. Without this engine, agents accumulate unresolved anomalies, novel patterns, and contradictory memories until they hallucinate, confabulate, or collapse entirely.

The Engine is implemented in `vaas-entropy` (Python 3.12+) with performance-critical paths delegated to `vaas-core` (Rust) via FFI. This document specifies the complete data structures, algorithms, protocols, and test plan.

---

## 2. Core Data Structures

### 2.1 EntropyReading

```python
from __future__ import annotations
import numpy as np
from dataclasses import dataclass, field
from datetime import datetime, timedelta
from enum import Enum, auto
from typing import Optional

class EntropyStatus(Enum):
    """Cognitive temperature classification."""
    NORMAL = auto()        # H < 0.5 * threshold — cool, efficient
    ELEVATED = auto()      # 0.5 * threshold < H < threshold — warm, monitor
    CRITICAL = auto()      # threshold < H < 1.5 * threshold — hot, urgent
    DANGER = auto()        # H > 1.5 * threshold — overheating, emergency dream required

@dataclass(frozen=True)
class EntropyComponents:
    """
    The three orthogonal entropy dimensions measured per agent.
    
    Shannon Entropy (H_belief):
        Uncertainty in the agent's belief distribution.
        High when agent has many contradictory or poorly-correlated beliefs.
        Measured via Shannon entropy of the belief vector.
    
    Temporal Entropy (H_temporal):
        Subjective time distortion — drift between agent's internal clock
        and the shared reference. High during crisis compression (agent thinks
        faster than wall-clock) or boredom dilation (agent thinks slower).
    
    Interaction Entropy (H_interaction):
        Novelty rate of recent interactions. High when agent encounters
        many new patterns. Low during routine operations.
    """
    shannon: float = 0.0       # H_belief ∈ [0.0, 1.0]
    temporal: float = 0.0      # H_temporal ∈ [0.0, 1.0]
    interaction: float = 0.0   # H_interaction ∈ [0.0, 1.0]

    @property
    def total(self) -> float:
        return self.shannon + self.temporal + self.interaction

@dataclass(frozen=True)
class EntropyReading:
    """
    A point-in-time entropy measurement for a single agent.
    
    The reading captures the agent's current cognitive temperature
    along with contextual metadata for dream scheduling decisions.
    """
    agent_id: str
    timestamp: datetime
    components: EntropyComponents
    threshold: float               # Agent-specific threshold (learned)
    status: EntropyStatus
    cycle_count: int               # Total interactions since last dream
    pending_count: int             # Unresolved pending decisions
    
    @property
    def total(self) -> float:
        return self.components.total
    
    @property
    def margin(self) -> float:
        """How close to CRITICAL? Negative = already critical."""
        return self.threshold - self.total
    
    @property
    def is_emergency(self) -> bool:
        return self.status in (EntropyStatus.CRITICAL, EntropyStatus.DANGER)
```

### 2.2 DreamCycle

```python
class DreamPhase(Enum):
    """The five phases of a full dream cycle."""
    FREEZE = auto()        # Snapshot current belief state
    MERGE = auto()         # Collapse near-duplicate shorthand
    GENERALIZE = auto()    # Extract rules from specific examples
    BAKE = auto()          # Promote high-confidence → immutable reflexes
    VENT = auto()          # Purge genuine noise below noise floor

@dataclass
class DreamConfig:
    """
    Configuration for a single dream cycle execution.
    
    The config determines which phases run, for how long,
    and what thresholds apply. It varies by dream type.
    """
    max_duration_ms: int = 5000        # Hard timeout for this dream
    freeze_all: bool = True             # Always freeze
    merge_threshold: float = 0.85       # Similarity threshold for merge
    generalize_min_examples: int = 5    # Min examples to generalize a concept
    bake_confidence: float = 0.95       # Confidence threshold for baking
    bake_usage_count: int = 100         # Usage threshold for baking
    forget_confidence: float = 0.1      # Below this → forget
    forget_age_days: int = 30           # Unused this long → forget
    vent_noise_floor: float = 0.01      # Below this → purge

@dataclass
class DreamResult:
    """
    The outcome of a completed dream cycle.
    
    Contains before/after metrics, what was pruned,
    and what new insights were synthesized.
    """
    dream_id: str
    agent_id: str
    started_at: datetime
    completed_at: datetime
    duration_ms: float
    
    # Before/after state
    entries_before: int
    entries_after: int
    shorthand_before: int
    shorthand_after: int
    
    # What happened
    frozen_entries: list[str] = field(default_factory=list)         # → cryogenic
    merged_entries: list[tuple[str, str]] = field(default_factory=list)  # (kept, merged)
    generalized_rules: list[str] = field(default_factory=list)      # New rules created
    baked_reflexes: list[str] = field(default_factory=list)         # → .pinch
    vented_entries: list[str] = field(default_factory=list)         # Purged
    
    # Dream quality (self-evaluation)
    quality_score: float = 0.0       # 0.0-1.0, estimated value of this dream
    hallucination_risk: float = 0.0  # 0.0-1.0, did this dream synthesize garbage?
    anomalous_findings: list[str] = field(default_factory=list)
    
    @property
    def compression_ratio(self) -> float:
        if self.entries_before == 0:
            return 1.0
        return self.entries_after / self.entries_before
    
    @property
    def effectiveness(self) -> float:
        """Combined metric: quality * compression."""
        return self.quality_score * self.compression_ratio
```

### 2.3 PrunedPattern

```python
@dataclass(frozen=True)
class PrunedPattern:
    """
    A pattern removed from the active garden during a dream cycle.
    
    These are not deleted — they are moved to cryogenic memory
    (via vaas-memory bridge). They can be thawed if the agent's
    behavior later correlates with this pattern.
    """
    pattern_id: str
    agent_id: str
    source_dream_id: str
    
    # What kind of pattern
    pattern_type: str           # "shorthand" | "structure" | "filter" | "rule"
    pattern_content: str        # The actual pattern (compressed)
    
    # Why it was pruned
    reason: str                 # "forgotten" | "merged" | "generalized" | "vented"
    confidence_at_prune: float  # Agent's confidence in this pattern when pruned
    usage_count_at_prune: int
    age_at_prune_days: float
    
    # Cryogenic metadata
    frozen_at: datetime
    thawed_at: Optional[datetime] = None   # Set when thawed back
    thaw_trigger: Optional[str] = None     # Why it was thawed
    
    @property
    def is_in_cryo(self) -> bool:
        return self.thawed_at is None

@dataclass
class PruningSummary:
    """Summary of all pruning activity across a dream cycle."""
    total_pruned: int
    total_frozen: int
    total_vented: int
    total_merged_into: int
    total_generalized: int
    patterns: list[PrunedPattern] = field(default_factory=list)
```

### 2.4 HealthScore / Apoptosis

```python
@dataclass
class HealthScore:
    """Composite health of an agent. Drives Apoptosis decisions."""
    consistency: float         # 0.0-1.0: internal consistency
    sensor_health: float       # 0.0-1.0: sensor integrity
    prediction_accuracy: float # 0.0-1.0: prediction failure rate
    last_heartbeat_ms: float   # ms since last heartbeat
    
    @property
    def composite(self) -> float:
        return (self.consistency + self.sensor_health + self.prediction_accuracy) / 3.0
    
    @property
    def is_failing(self) -> bool:
        return self.composite < 0.3

class ApoptosisDecision(Enum):
    CONTINUE = auto()
    INITIATE = auto()        # Graceful shutdown with state dump

@dataclass
class ApoptosisSignal:
    """Signal emitted when an agent enters programmed self-destruction."""
    agent_id: str
    decision: ApoptosisDecision
    reason: str
    health_score: HealthScore
    final_state_dump: bytes          # Serialized garden snapshot
    death_broadcast: list[str]       # Agents to notify
```

---

## 3. The Entropy Measurement Algorithm

### 3.1 Shannon Entropy of Belief Distribution

The agent's belief distribution is represented as a vector of probabilities over its known concepts. Let `B = [p_1, p_2, ..., p_n]` be the belief vector, where each `p_i` represents the agent's confidence in concept `i`.

```python
import numpy as np
from scipy.special import entr  # x * log(x) for small x stability

def measure_shannon_entropy(beliefs: np.ndarray) -> float:
    """
    Measure Shannon entropy of the agent's belief distribution.
    
    H(B) = -Σ p_i * log₂(p_i)
    
    Normalized to [0.0, 1.0] where:
    - 0.0 = delta distribution (single belief at p=1.0, rest at 0.0)
    - 1.0 = uniform distribution (all beliefs equally uncertain)
    
    Uses scipy.special.entr for numerical stability with small p.
    """
    if beliefs.size == 0:
        return 0.0
    
    # Normalize to probability distribution
    beliefs = np.asarray(beliefs, dtype=np.float64)
    beliefs = beliefs / beliefs.sum()
    
    # Compute entropy with numerical stability
    # entr(x) = -x * log(x) for x > 0, 0 for x = 0
    h = np.sum(entr(beliefs)) / np.log(2.0)  # Convert from nats to bits
    
    # Normalize: max entropy = log₂(n) for uniform distribution
    max_h = np.log2(beliefs.size)
    if max_h == 0:
        return 0.0
    
    return float(min(1.0, h / max_h))


def measure_temporal_entropy(
    clock_drift_ms: float,
    reference_interval_ms: float = 100.0,
    max_drift_tolerance: float = 0.5,
) -> float:
    """
    Measure temporal entropy — subjective time dilation.
    
    Temporal entropy captures the distortion between the agent's
    internal clock and the shared reference (vessel heartbeat at 10Hz).
    
    High drift = high temporal entropy:
    - Agent running at 2x speed → clock_drift ≈ -50ms per cycle
    - Agent running at 0.5x → clock_drift ≈ +50ms per cycle
    
    Args:
        clock_drift_ms: Drift between agent clock and reference (ms).
                        Negative = agent faster than reference.
        reference_interval_ms: Reference cycle interval (default 100ms = 10Hz).
        max_drift_tolerance: Fraction of reference interval considered "critical drift".
    
    Returns:
        Temporal entropy score ∈ [0.0, 1.0]
    """
    drift_fraction = abs(clock_drift_ms) / reference_interval_ms
    return float(min(1.0, drift_fraction / max_drift_tolerance))


def measure_interaction_entropy(
    recent_interactions: list[str],
    known_patterns: set[str],
    window_size: int = 100,
) -> float:
    """
    Measure interaction entropy — novelty rate of recent exchanges.
    
    High when the agent encounters many new patterns it hasn't seen before.
    Low during routine operations where most patterns are familiar.
    
    Args:
        recent_interactions: List of interaction signatures (most recent first).
        known_patterns: Set of patterns the agent has already learned.
        window_size: How many recent interactions to consider.
    
    Returns:
        Interaction entropy score ∈ [0.0, 1.0]
    """
    window = recent_interactions[:window_size]
    if not window:
        return 0.0
    
    novel = sum(1 for sig in window if sig not in known_patterns)
    return min(1.0, novel / len(window))


class CompositeEntropyCalculator:
    """
    Combines all three entropy components into a single reading.
    
    Uses per-agent learned weights to adjust component importance.
    Weights are updated after each dream cycle based on dream effectiveness.
    """
    
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        # Learned weights: initialized uniformly, updated after dreams
        self.weights = np.array([1/3, 1/3, 1/3], dtype=np.float64)
        self.weight_history: list[tuple[datetime, np.ndarray]] = []
    
    def measure(
        self,
        beliefs: np.ndarray,
        clock_drift_ms: float,
        recent_interactions: list[str],
        known_patterns: set[str],
        threshold: float,
        cycle_count: int,
        pending_count: int,
    ) -> EntropyReading:
        components = EntropyComponents(
            shannon=measure_shannon_entropy(beliefs),
            temporal=measure_temporal_entropy(clock_drift_ms),
            interaction=measure_interaction_entropy(recent_interactions, known_patterns),
        )
        
        total = components.total
        
        if total > threshold * 1.5:
            status = EntropyStatus.DANGER
        elif total > threshold:
            status = EntropyStatus.CRITICAL
        elif total > threshold * 0.5:
            status = EntropyStatus.ELEVATED
        else:
            status = EntropyStatus.NORMAL
        
        return EntropyReading(
            agent_id=self.agent_id,
            timestamp=datetime.utcnow(),
            components=components,
            threshold=threshold,
            status=status,
            cycle_count=cycle_count,
            pending_count=pending_count,
        )
    
    def update_weights(self, dream_result: DreamResult):
        """
        Update component weights based on dream effectiveness.
        
        If a dream cycle was effective (high quality_score) after a period
        of high [component] entropy, that component's weight increases —
        it was a useful signal. If the dream was ineffective despite high
        [component] entropy, that component's weight decreases — it was noise.
        
        Simple heuristic: for each component, compute correlation between
        pre-dream component level and dream effectiveness. Use moving average.
        """
        # Simplified: adjust toward uniform, preventing weight collapse
        self.weights = 0.9 * self.weights + 0.1 * (1/3 * np.ones(3))
        self.weight_history.append((datetime.utcnow(), self.weights.copy()))
```

### 3.2 Measuring Internal Inconsistency (Apoptosis)

```python
def measure_internal_inconsistency(agent_state: dict) -> float:
    """
    Measure how internally consistent an agent's state is.
    
    Checks for:
    1. Conflicting memories: two memories that describe the same event
       with contradictory details.
    2. Prediction failures: recent predictions that didn't match reality.
    3. Self-contradictory beliefs: belief A implies not-belief B, but
       both exist with high confidence.
    4. Temporal anomalies: state claims events in impossible order.
    
    Returns:
        Inconsistency score ∈ [0.0, 1.0], where 0 = perfectly consistent
        and 1 = maximal inconsistency (hallucinating).
    """
    scores = []
    
    # 1. Check memory conflicts
    memory_conflicts = _find_memory_conflicts(
        agent_state.get('memories', [])
    )
    if memory_conflicts:
        scores.append(min(1.0, len(memory_conflicts) / 10.0))
    
    # 2. Check prediction accuracy
    predictions = agent_state.get('recent_predictions', [])
    if predictions:
        failures = sum(1 for p in predictions if not _prediction_matched(p))
        scores.append(failures / len(predictions))
    
    # 3. Check belief consistency
    beliefs = agent_state.get('beliefs', {})
    contradictions = _find_belief_contradictions(beliefs)
    if contradictions:
        scores.append(min(1.0, len(contradictions) / 5.0))
    
    # 4. Check temporal consistency
    temporal_anomalies = _find_temporal_anomalies(
        agent_state.get('timeline', [])
    )
    if temporal_anomalies:
        scores.append(min(1.0, len(temporal_anomalies) / 5.0))
    
    if not scores:
        return 0.0
    
    return sum(scores) / len(scores)


def _find_memory_conflicts(memories: list[dict]) -> list[tuple]:
    """Find pairs of memories that describe the same event with contradictions."""
    # Group by event_id
    events: dict[str, list[dict]] = {}
    for mem in memories:
        eid = mem.get('event_id')
        if eid:
            events.setdefault(eid, []).append(mem)
    
    conflicts = []
    for eid, mems in events.items():
        if len(mems) < 2:
            continue
        # Compare each pair
        for i in range(len(mems)):
            for j in range(i + 1, len(mems)):
                if _memories_contradict(mems[i], mems[j]):
                    conflicts.append((mems[i].get('memory_id'), mems[j].get('memory_id')))
    
    return conflicts


def _find_belief_contradictions(beliefs: dict[str, float]) -> list[tuple[str, str]]:
    """
    Find pairs of beliefs that logically contradict.
    
    If belief "water_depth > 20fm" has confidence 0.9 and
    belief "water_depth < 15fm" also has confidence 0.9,
    this is a contradiction — both can't be true.
    """
    from itertools import combinations
    
    contradictions = []
    # Group beliefs by domain (e.g., "water_depth", "wind_speed")
    by_domain: dict[str, list[tuple[str, float]]] = {}
    for belief_name, confidence in beliefs.items():
        domain = belief_name.split('_')[0] if '_' in belief_name else belief_name
        by_domain.setdefault(domain, []).append((belief_name, confidence))
    
    for domain, domain_beliefs in by_domain.items():
        for (b1, c1), (b2, c2) in combinations(domain_beliefs, 2):
            if c1 > 0.7 and c2 > 0.7 and _implicitly_contradictory(b1, b2):
                contradictions.append((b1, b2))
    
    return contradictions
```

---

## 4. The Dream Cycle Protocol

### 4.1 Dream Scheduler

```python
import asyncio
import time
from collections import defaultdict
from heapq import heappush, heappop

class DreamScheduler:
    """
    Schedules dream cycles based on entropy readings.
    
    The scheduler maintains a priority queue of pending dreams,
    ordered by urgency. It respects agent cooldowns and system
    load to prevent dream thrashing.
    """
    
    def __init__(self, min_interval_seconds: float = 30.0):
        self.min_interval = min_interval_seconds  # Minimum gap between dreams per agent
        self.last_dream: dict[str, datetime] = defaultdict(lambda: datetime.min)
        self.pending: list[tuple[float, str, DreamConfig]] = []  # (priority, agent_id, config)
        self._running = False
    
    def evaluate(self, reading: EntropyReading) -> Optional[DreamConfig]:
        """
        Determine if a dream is needed based on entropy reading.
        
        Returns:
            DreamConfig if dream should be scheduled, None otherwise.
        """
        agent_id = reading.agent_id
        time_since_last = (datetime.utcnow() - self.last_dream[agent_id]).total_seconds()
        
        if reading.status == EntropyStatus.DANGER:
            # Emergency dream — override cooldown
            return DreamConfig(
                max_duration_ms=5000,
                freeze_all=True,
                merge_threshold=0.8,     # More aggressive merge
                generalize_min_examples=3,  # Generalize sooner
                bake_confidence=0.9,      # Lower baking threshold
                bake_usage_count=30,      # Bake after fewer uses
                forget_confidence=0.3,    # Forget more aggressively
                forget_age_days=14,       # Forget faster
                vent_noise_floor=0.05,    # Vent more
            )
        
        if reading.status == EntropyStatus.CRITICAL:
            if time_since_last >= self.min_interval:
                return DreamConfig(
                    max_duration_ms=3000,
                    freeze_all=True,
                    merge_threshold=0.85,
                    generalize_min_examples=5,
                    bake_confidence=0.95,
                    bake_usage_count=100,
                    forget_confidence=0.1,
                    forget_age_days=30,
                    vent_noise_floor=0.01,
                )
            return None  # Respect cooldown even during critical
        
        if reading.status == EntropyStatus.ELEVATED:
            if time_since_last >= self.min_interval * 2:
                return DreamConfig(
                    max_duration_ms=1000,
                    freeze_all=True,
                    merge_threshold=0.9,
                    generalize_min_examples=10,
                    bake_confidence=0.98,
                    bake_usage_count=200,
                    forget_confidence=0.05,
                    forget_age_days=60,
                    vent_noise_floor=0.005,
                )
            return None
        
        # NORMAL — schedule micro-dream during downtime
        if reading.pending_count == 0 and time_since_last >= self.min_interval * 3:
            return self._micro_dream_config()
        
        return None
    
    def schedule(self, reading: EntropyReading, config: DreamConfig):
        """Add dream to priority queue."""
        # Priority: DANGER > CRITICAL > ELEVATED > NORMAL
        priority_map = {
            EntropyStatus.NORMAL: 0,
            EntropyStatus.ELEVATED: 1,
            EntropyStatus.CRITICAL: 2,
            EntropyStatus.DANGER: 3,
        }
        priority = -priority_map[reading.status]  # Negative = min-heap extracts highest first
        heappush(self.pending, (priority, reading.agent_id, config, datetime.utcnow()))
    
    async def run_cycle(self, entropy_reading: EntropyReading) -> Optional[asyncio.Task]:
        """Evaluate entropy reading and schedule dream if needed. Returns task or None."""
        config = self.evaluate(entropy_reading)
        if config is None:
            return None
        
        self.schedule(entropy_reading, config)
        return asyncio.create_task(self._execute_dream(entropy_reading.agent_id, config))
    
    def _micro_dream_config(self) -> DreamConfig:
        """Micro-dream: 50ms compression burst."""
        return DreamConfig(
            max_duration_ms=50,
            freeze_all=False,           # Don't freeze — too slow
            merge_threshold=0.95,       # Only merge near-identical
            generalize_min_examples=50,  # Don't generalize — too slow
            bake_confidence=0.99,        # Only bake extremely confident
            bake_usage_count=500,        # Only bake very frequent
            forget_confidence=0.01,      # Only forget extremely low
            forget_age_days=90,          # Only forget very old
            vent_noise_floor=0.001,      # Only vent the worst noise
        )
    
    async def _execute_dream(self, agent_id: str, config: DreamConfig) -> DreamResult:
        """Execute a dream cycle with timeout enforcement."""
        self.last_dream[agent_id] = datetime.utcnow()
        
        try:
            result = await asyncio.wait_for(
                self._run_phases(agent_id, config),
                timeout=config.max_duration_ms / 1000.0,
            )
            return result
        except asyncio.TimeoutError:
            return DreamResult(
                dream_id=f"timeout_{agent_id}_{int(time.time())}",
                agent_id=agent_id,
                started_at=datetime.utcnow(),
                completed_at=datetime.utcnow(),
                duration_ms=config.max_duration_ms,
                entries_before=0,
                entries_after=0,
                shorthand_before=0,
                shorthand_after=0,
                quality_score=0.0,
                hallucination_risk=1.0,
                anomalous_findings=["Dream cycle timed out"],
            )
    
    async def _run_phases(self, agent_id: str, config: DreamConfig) -> DreamResult:
        """
        Execute the 5-phase dream protocol.
        
        FREEZE → MERGE → GENERALIZE → BAKE → VENT
        
        Returns the DreamResult populated with before/after metrics.
        """
        # In practice, this interfaces with the agent's garden.
        # For this spec, we show the protocol structure.
        started_at = datetime.utcnow()
        
        # Phase 1: FREEZE — snapshot belief state
        frozen = await self._phase_freeze(agent_id)
        
        # Phase 2: MERGE — collapse near-duplicates
        merged = await self._phase_merge(agent_id, config.merge_threshold)
        
        # Phase 3: GENERALIZE — extract rules
        generalized = await self._phase_generalize(
            agent_id, config.generalize_min_examples
        )
        
        # Phase 4: BAKE — promote to reflexes
        baked = await self._phase_bake(
            agent_id, config.bake_confidence, config.bake_usage_count
        )
        
        # Phase 5: VENT — purge noise
        vented = await self._phase_vent(
            agent_id, config.forget_confidence, config.vent_noise_floor
        )
        
        completed_at = datetime.utcnow()
        duration_ms = (completed_at - started_at).total_seconds() * 1000.0
        
        return DreamResult(
            dream_id=f"dream_{agent_id}_{int(started_at.timestamp())}",
            agent_id=agent_id,
            started_at=started_at,
            completed_at=completed_at,
            duration_ms=duration_ms,
            entries_before=frozen.entries_before,
            entries_after=vented.entries_after,
            shorthand_before=frozen.shorthand_before,
            shorthand_after=vented.shorthand_after,
            frozen_entries=frozen.frozen_ids,
            merged_entries=merged.merged_pairs,
            generalized_rules=generalized.new_rules,
            baked_reflexes=baked.reflex_ids,
            vented_entries=vented.vented_ids,
            quality_score=0.0,  # Computed post-hoc by dream quality evaluator
            hallucination_risk=0.0,
        )
```

### 4.2 Micro-Dream Protocol (50ms Burst)

The micro-dream is the most time-critical component. It must complete within 50ms wall-clock time.

```python
import time
import gc

class MicroDream:
    """
    50ms compression burst. Designed for minimal latency.
    
    Protocol:
    1. SAMPLE current entropy reading
    2. FORGET: remove entries with confidence < forget_threshold
       (don't freeze — too slow; don't merge — too slow; don't generalize)
    3. VENT: purge entries below noise floor
    4. RESUME
    
    If 50ms budget is exceeded, abort gracefully without partial mutation.
    """
    
    DEADLINE_NS = 50_000_000  # 50ms in nanoseconds
    
    def __init__(self, agent_garden_accessor):
        self.garden = agent_garden_accessor
        self.start_ns: int = 0
    
    async def execute(self, config: DreamConfig) -> DreamResult:
        self.start_ns = time.perf_counter_ns()
        started_at = datetime.utcnow()
        
        entries_before = self.garden.entry_count()
        shorthand_before = self.garden.shorthand_count()
        
        vented: list[str] = []
        
        # Phase: VENT (most important — noise increases entropy fastest)
        vented = await self._vent_noise(config.vent_noise_floor)
        
        # Phase: FORGET (if time remains)
        if self._remaining_ns() > 20_000_000:  # >20ms left
            vented.extend(
                await self._forget_old(config.forget_age_days, config.forget_confidence)
            )
        
        entries_after = self.garden.entry_count()
        shorthand_after = self.garden.shorthand_count()
        
        completed_at = datetime.utcnow()
        duration_ms = (completed_at - started_at).total_seconds() * 1000.0
        
        # Force GC after micro-dream to prevent latency buildup
        gc.collect()
        
        return DreamResult(
            dream_id=f"micro_{self.garden.agent_id}_{int(started_at.timestamp())}",
            agent_id=self.garden.agent_id,
            started_at=started_at,
            completed_at=completed_at,
            duration_ms=duration_ms,
            entries_before=entries_before,
            entries_after=entries_after,
            shorthand_before=shorthand_before,
            shorthand_after=shorthand_after,
            vented_entries=vented,
            quality_score=0.5,  # Micro-dreams get neutral quality (by design)
            hallucination_risk=0.0,  # Micro-dreams don't synthesize
        )
    
    def _remaining_ns(self) -> int:
        return self.DEADLINE_NS - (time.perf_counter_ns() - self.start_ns)
    
    async def _vent_noise(self, noise_floor: float) -> list[str]:
        """Remove entries below noise floor. Must complete in <5ms."""
        threshold_ns = 5_000_000  # 5ms hard limit
        vented = []
        
        for entry_id, entry in self.garden.lowest_confidence_entries(noise_floor):
            if self._remaining_ns() < threshold_ns:
                break
            self.garden.remove_entry(entry_id)
            vented.append(entry_id)
        
        return vented
    
    async def _forget_old(self, max_age_days: int, min_confidence: float) -> list[str]:
        """Remove entries that are old AND low confidence."""
        forgotten = []
        for entry_id, entry in self.garden.old_low_confidence_entries(max_age_days, min_confidence):
            if self._remaining_ns() < 5_000_000:
                break
            self.garden.remove_entry(entry_id)
            forgotten.append(entry_id)
        return forgotten
```

### 4.3 Emergency Dream Protocol

```python
class EmergencyDream:
    """
    Emergency dream cycle — triggered when entropy exceeds CRITICAL threshold.
    
    Unlike normal dreams, emergency dreams:
    - Preempt current agent operation (must interrupt whatever the agent is doing)
    - Run the full 5-phase protocol at maximum aggressiveness
    - Bypass cooldown periods
    - Log to system health monitor at ERROR severity
    """
    
    async def execute(
        self,
        agent_id: str,
        reading: EntropyReading,
        garden_accessor,
        cryogenic_bridge,
    ) -> DreamResult:
        """
        Execute emergency dream protocol.
        
        1. IMMEDIATELY: Broadcast "DREAMING" status to all agents
        2. Phase 1: FREEZE entire agent state (forced snapshot)
        3. Phase 2-5: Run aggressive 5-phase protocol
        4. EVERY pruned pattern goes to cryogenic memory (not discarded)
        5. COMPLETE: Broadcast "AWAKE" status to all agents
        """
        # 1. Broadcast DREAMING
        await self._broadcast_status(agent_id, "DREAMING", reading)
        
        try:
            # 2. Aggressive dream with danger config
            config = DreamConfig(
                max_duration_ms=5000,
                freeze_all=True,
                merge_threshold=0.7,       # Very aggressive merge
                generalize_min_examples=2,  # Generalize from minimal data
                bake_confidence=0.85,       # Low baking threshold
                bake_usage_count=10,        # Bake after few uses
                forget_confidence=0.4,      # Aggressive forgetting
                forget_age_days=7,          # Forget recent too
                vent_noise_floor=0.1,       # Vent aggressively
            )
            
            result = await DreamScheduler()._run_phases(agent_id, config)
            
            # Send all pruned patterns to cryogenic memory
            await self._archive_pruned_patterns(result, cryogenic_bridge)
            
            return result
            
        finally:
            # 5. Broadcast AWAKE
            await self._broadcast_status(agent_id, "AWAKE", None)
```

---

## 5. Integration with the Cryogenic Memory Layer

The Entropy Engine interfaces with `vaas-memory` through a well-defined bridge. Every pruned pattern is archived to cryogenic storage, and the engine can query for thaw triggers.

```python
class EntropyMemoryBridge:
    """
    Bridge between Entropy Engine and vaas-memory cryogenic storage.
    
    Every pruned pattern is archived here. The bridge also monitors
    cryogenic patterns and emits thaw signals when the agent's
    behavior correlates with a frozen pattern.
    """
    
    async def archive_patterns(
        self,
        agent_id: str,
        patterns: list[PrunedPattern],
        dream_result: DreamResult,
    ) -> int:
        """
        Archive pruned patterns to cryogenic memory.
        
        Returns number of patterns successfully archived.
        """
        archived = 0
        for pattern in patterns:
            success = await self._write_to_cryo(pattern)
            if success:
                archived += 1
        return archived
    
    async def check_thaw_triggers(
        self,
        agent_id: str,
        recent_behavior: dict,
    ) -> list[PrunedPattern]:
        """
        Check if agent's recent behavior correlates with any frozen pattern.
        
        Returns list of patterns to thaw (move from cryo → active garden).
        """
        candidates = await self._query_cryo_by_agent(agent_id)
        triggered = []
        
        for pattern in candidates:
            correlation = self._compute_behavior_correlation(
                recent_behavior, pattern.pattern_content
            )
            if correlation > 0.7:  # Threshold for thaw
                triggered.append(pattern)
                pattern.thawed_at = datetime.utcnow()
                pattern.thaw_trigger = f"behavior_correlation_{correlation:.2f}"
        
        return triggered
    
    def _compute_behavior_correlation(
        self, behavior: dict, pattern: str
    ) -> float:
        """
        Simplified correlation: measure if the agent is re-encountering
        patterns similar to those that were pruned.
        
        In production, this would use vector embeddings and cosine similarity.
        For now, use Jaccard similarity on pattern signatures.
        """
        behavior_sigs = set(behavior.get('pattern_signatures', []))
        pattern_sigs = set(_extract_signatures(pattern))
        
        if not behavior_sigs or not pattern_sigs:
            return 0.0
        
        intersection = behavior_sigs & pattern_sigs
        union = behavior_sigs | pattern_sigs
        
        return len(intersection) / len(union)
```

---

## 6. The Apoptosis Monitor

```python
class ApoptosisMonitor:
    """
    Monitors agent health and triggers programmed self-destruction
    when the agent is beyond recovery.
    
    Apoptosis is the last resort — after entropy has remained CRITICAL
    through multiple dream cycles, and internal inconsistency is
    confirmed, the agent should die gracefully rather than produce
    toxic output.
    """
    
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.consecutive_critical_readings: int = 0
        self.max_critical_before_apoptosis: int = 3
    
    def check(
        self,
        reading: EntropyReading,
        agent_state: dict,
        last_dream_result: Optional[DreamResult],
    ) -> ApoptosisSignal:
        """
        Check if this agent should enter apoptosis.
        
        Conditions for apoptosis:
        1. Entropy has been CRITICAL for max_critical_before_apoptosis consecutive readings
        2. Internal inconsistency score > 0.7
        3. Last dream cycle failed (timeout) or produced hallucinations
        4. Sensor health is significantly degraded
        
        If ≥3 conditions met → initiate apoptosis.
        """
        health = HealthScore(
            consistency=1.0 - measure_internal_inconsistency(agent_state),
            sensor_health=agent_state.get('sensor_health', 1.0),
            prediction_accuracy=agent_state.get('prediction_accuracy', 1.0),
            last_heartbeat_ms=agent_state.get('ms_since_last_heartbeat', 0),
        )
        
        if reading.is_emergency:
            self.consecutive_critical_readings += 1
        else:
            self.consecutive_critical_readings = 0
        
        conditions_met = 0
        
        # Condition 1: Consecutive critical
        if self.consecutive_critical_readings >= self.max_critical_before_apoptosis:
            conditions_met += 1
        
        # Condition 2: Internal inconsistency
        if health.consistency < 0.3:
            conditions_met += 1
        
        # Condition 3: Dream failure
        if last_dream_result and last_dream_result.hallucination_risk > 0.8:
            conditions_met += 1
        
        # Condition 4: Sensor degradation
        if health.sensor_health < 0.3:
            conditions_met += 1
        
        if conditions_met >= 3:
            return ApoptosisSignal(
                agent_id=self.agent_id,
                decision=ApoptosisDecision.INITIATE,
                reason=f"Apoptosis triggered: {conditions_met}/4 conditions met",
                health_score=health,
                final_state_dump=_serialize_state(agent_state),
                death_broadcast=[
                    "hermes",
                    "boat-agent",
                    "terminal",
                    "human",  # Human must always know
                ],
            )
        
        return ApoptosisSignal(
            agent_id=self.agent_id,
            decision=ApoptosisDecision.CONTINUE,
            reason="",
            health_score=health,
            final_state_dump=b"",
            death_broadcast=[],
        )
```

---

## 7. Test Plan

### 7.1 Unit Tests

| Test ID | Test Name | What It Verifies | Priority |
|---------|-----------|-----------------|----------|
| ENT-001 | `test_shannon_entropy_delta_distribution` | Delta distribution → H=0.0 | P0 |
| ENT-002 | `test_shannon_entropy_uniform_distribution` | Uniform distribution → H=1.0 | P0 |
| ENT-003 | `test_shannon_entropy_known_values` | Verify against hand-calculated entropy values | P0 |
| ENT-004 | `test_shannon_entropy_empty_beliefs` | Empty belief vector → H=0.0 | P1 |
| ENT-005 | `test_shannon_entropy_single_entry` | Single belief → H=0.0 | P1 |
| ENT-006 | `test_temporal_entropy_zero_drift` | No drift → H=0.0 | P0 |
| ENT-007 | `test_temporal_entropy_critical_drift` | Drift = 50% of interval → H=1.0 | P0 |
| ENT-008 | `test_temporal_entropy_linear_scale` | Linear scaling between 0 and max | P1 |
| ENT-009 | `test_interaction_entropy_all_novel` | All interactions novel → H=1.0 | P0 |
| ENT-010 | `test_interaction_entropy_all_known` | All interactions known → H=0.0 | P0 |
| ENT-011 | `test_interaction_entropy_mixed` | 50% novel → H=0.5 | P1 |
| ENT-012 | `test_composite_entropy_normal` | Low components → NORMAL status | P0 |
| ENT-013 | `test_composite_entropy_elevated` | Moderate components → ELEVATED | P0 |
| ENT-014 | `test_composite_entropy_critical` | High components → CRITICAL | P0 |
| ENT-015 | `test_composite_entropy_danger` | Very high components → DANGER | P0 |
| ENT-016 | `test_read_all_fields` | EntropyReading has all required fields | P1 |
| ENT-017 | `test_margin_positive_normal` | Margin positive for NORMAL | P1 |
| ENT-018 | `test_margin_negative_critical` | Margin negative for CRITICAL | P1 |

### 7.2 Dream Cycle Tests

| Test ID | Test Name | What It Verifies | Priority |
|---------|-----------|-----------------|----------|
| DREAM-001 | `test_normal_dream_scheduling` | NORMAL entropy → scheduled during downtime | P0 |
| DREAM-002 | `test_critical_dream_immediate` | CRITICAL entropy → scheduled immediately | P0 |
| DREAM-003 | `test_danger_emergency_bypass_cooldown` | DANGER → bypasses cooldown | P0 |
| DREAM-004 | `test_cooldown_respected_elevated` | ELEVATED → respects cooldown | P1 |
| DREAM-005 | `test_micro_dream_50ms` | Micro-dream completes in <50ms | P0 |
| DREAM-006 | `test_micro_dream_abort_on_timeout` | Micro-dream aborts cleanly on timeout | P1 |
| DREAM-007 | `test_deep_dream_5_phases_executed` | All 5 phases run in order | P0 |
| DREAM-008 | `test_deep_dream_timeout_handling` | Deep dream timeout → graceful failure | P1 |
| DREAM-009 | `test_dream_result_metrics` | DreamResult has correct before/after counts | P1 |
| DREAM-010 | `test_emergency_dream_broadcast` | Emergency dream broadcasts DREAMING/AWAKE | P0 |
| DREAM-011 | `test_consecutive_dreams_not_thrash` | Cooldown prevents dream thrashing | P1 |
| DREAM-012 | `test_cryogenic_archive_on_prune` | Every pruned pattern goes to cryo | P0 |

### 7.3 Entropy Engine Integration Tests

| Test ID | Test Name | What It Verifies | Priority |
|---------|-----------|-----------------|----------|
| INT-001 | `test_engine_lifecycle` | 1000 entropy readings + dream cycles without crash | P0 |
| INT-002 | `test_engine_memory_no_leak` | Memory stable over 10,000 cycles | P0 |
| INT-003 | `test_engine_concurrent_agents` | 5 agents measured concurrently without race | P1 |
| INT-004 | `test_entropy_drops_after_dream` | Entropy decreases after successful dream | P0 |
| INT-005 | `test_entropy_rises_without_dreams` | Entropy increases over time without dreams | P1 |
| INT-006 | `test_cryogenic_thaw_cycle` | Pruned pattern thawed → active garden | P1 |
| INT-007 | `test_weight_update_after_dream` | Component weights update after effective dream | P2 |
| INT-008 | `test_entropy_reading_idempotent` | Same state → same reading (deterministic) | P1 |

### 7.4 Apoptosis Tests

| Test ID | Test Name | What It Verifies | Priority |
|---------|-----------|-----------------|----------|
| APOP-001 | `test_healthy_agent_continues` | Normal health → CONTINUE | P0 |
| APOP-002 | `test_critical_inconsistency_initiates` | High inconsistency → INITIATE | P0 |
| APOP-003 | `test_apoptosis_includes_state_dump` | State dump non-empty on initiate | P0 |
| APOP-004 | `test_apoptosis_broadcasts_death` | DeathBroadcast sent to configured agents | P0 |
| APOP-005 | `test_apoptosis_counts_consecutive` | Counter increments on consecutive critical | P1 |
| APOP-006 | `test_apoptosis_resets_on_elevated` | Counter resets on non-critical reading | P1 |

### 7.5 Performance Benchmarks

| Benchmark | Metric | Target | Priority |
|-----------|--------|--------|----------|
| `bench_shannon_entropy_1k` | 1000 beliefs | <100μs | P0 |
| `bench_shannon_entropy_10k` | 10,000 beliefs | <1ms | P1 |
| `bench_composite_measurement` | Full measurement | <1ms | P0 |
| `bench_micro_dream_duration` | 50ms burst | <50ms (p99) | P0 |
| `bench_deep_dream_100_entries` | 100-entry garden | <500ms | P1 |
| `bench_deep_dream_1000_entries` | 1000-entry garden | <5s | P2 |
| `bench_concurrent_5_agents` | 5 agent monitors | <5ms total | P1 |
| `bench_concurrent_10_agents` | 10 agent monitors | <10ms total | P2 |
| `bench_entropy_engine_24h_stability` | 24h run | No crash, no leak | P0 |
| `bench_cryogenic_archive_1000` | 1000 patterns | <100ms | P1 |

---

## 8. State Diagram

```
┌──────────┐   entropy rises    ┌──────────┐   threshold exceeded    ┌──────────┐
│  NORMAL  │ ──────────────────▶│ ELEVATED │ ──────────────────────▶│ CRITICAL │
│  (cool)  │                    │  (warm)  │                         │  (hot)   │
└──────────┘                    └──────────┘                         └────┬─────┘
      ▲                               │                                   │
      │                      time-based│                          immediate│
      │                     micro-dream│                     emergency dream│
      │                               ▼                                   │
      │                         ┌──────────┐                             │
      │                         │  MICRO-   │  dream successful?          │
      │                         │  DREAM    │ ──────yes───────▶ NORMAL    │
      │                         │  (50ms)   │                             │
      │                         └────┬─────┘      no ──────────────────┐
      │                              │                                 ▼
      │                     ┌──────────────────┐               ┌──────────┐
      │                     │    SCHEDULED      │              │  DANGER  │
      │                     │   DEEP DREAM      │              │ (dying)  │
      │                     │   (500ms-5s)      │              └────┬─────┘
      │                     └────────┬─────────┘                   │
      │                              │                     apoptosis check
      │                     dream completes                 │
      │                              │                  ┌───┴───┐
      │                              ▼                  │       │
      │                     ┌──────────────────┐   ┌────────┐  ┌──────────┐
      │                     │  DREAM QUALITY    │   │APOPTOSIS│  │ APOPTOSIS│
      │                     │  EVALUATION       │   │CHECKS   │  │INITIATED │
      │                     │                   │   └────────┘  │(agent dies│
      │                     │ quality > 0.5 ────▶ NORMAL        │ gracefully│
      │                     │ quality < 0.5 ────▶ ELEVATED      └──────────┘
      └─────────────────────┴───────────────────┘
```

---

## Summary

The Entropy Engine is the first and most critical implementation target of the VaaS Resonance Substrate. It provides the self-regulating cognitive metabolism that prevents agent hallucination and collapse. The engine measures three orthogonal entropy components (Shannon, temporal, interaction), schedules appropriate dream cycles (micro/50ms, deep/5s, emergency), prunes gardens through a 5-phase protocol, archives patterns to cryogenic memory, and monitors agent health for graceful apoptosis. Phase 1 of the 100-day plan prioritizes this engine as the first deliverable — without it, the substrate has no immune system.
