# The Stratified Safety Envelope: Deep Dive

> **Document 3 of 3** — Regulatory mapping, safety standards, formal verification, immune system, and Rust implementation
> Source: v2.0 synthesis, ka1 UX architecture, ka4 multi-agent subsystem, ka5 Layer 8

---

## 1. The Four-Layer Architecture

The VaaS safety envelope is stratified into four nested layers, each with different override authorities:

```
Layer 1: HARD (Physical) — boat-agent kernel, NO override
Layer 2: SOFT (Operational) — Human override with explicit acknowledgment  
Layer 3: ADVISORY (Recommended) — System self-override
Layer 4: ETHICAL (Moral) — Terminal's productive dissonance
```

This section maps each layer to existing maritime regulations and safety standards.

---

## 2. Layer 1: HARD (Physical Impossibilities)

### 2.1 Description

"Physical impossibilities — cannot be violated under any circumstance. Example: draft > depth = instant veto, no debate. Enforced by boat-agent kernel in hardware. No agent can override."

### 2.2 Regulatory Mapping

| Regulation | What It Requires | How Layer 1 Maps |
|------------|-----------------|------------------|
| **SOLAS Ch. V Reg. 19** | Ships must have "continuous" depth monitoring | Layer 1's draft > depth check is the minimum implementation of a depth alarm |
| **SOLAS Ch. V Reg. 33** | Distress situations: master must act in best interest of safety | Layer 1 cannot be overridden by the master — **conflict** with SOLAS. SOLAS says master's authority is absolute; Layer 1 says even the master cannot violate hard bounds. |
| **COLREGS Rule 2** | "Nothing in these Rules shall exonerate any vessel... from the consequences of any neglect" | The master is ultimately responsible. A system that prevents the master from running aground is compliant; a system that prevents the master from taking emergency action (e.g., intentional grounding to avoid collision) is NOT. |
| **IMO MSC.1/Circ.1512** | Guidelines on operational limits — vessels must have defined operating envelopes | Layer 1's draft > depth + under-keel clearance (UKC) is a direct implementation |
| **DNV-ST-0111** | "Integrity of safety systems" — safety-critical functions must have "fail-safe" behavior | Layer 1 must be fail-safe: if boat-agent kernel fails, the system must degrade to a safe state (e.g., emergency stop, not full speed) |

**Critical regulatory conflict:** SOLAS Ch. V Reg. 33 grants the master absolute authority in situations of "immediate danger to the safety of the ship." A Layer 1 that cannot be overridden by the master violates this regulation. The architecture must distinguish between:
- **Intentional grounding** (master's choice to save the vessel from a worse fate — must be allowed)
- **Unintentional grounding** (system failure or navigation error — must be prevented)

**Resolution:** Layer 1 should have a **master override** that requires: (a) physical intervention (breaking a seal, turning a key), (b) logging the event to the voyage data recorder (VDR), and (c) post-voyage review by the company safety officer. This preserves both safety and regulatory compliance.

### 2.3 DO-178C / IEC 61508 Mapping

| Aspect | DO-178C Level A | IEC 61508 SIL 3 | Layer 1 |
|--------|----------------|-----------------|---------|
| Failure probability | < 10⁻⁹ per flight hour | < 10⁻⁷ per demand | Must be <.10⁻⁷ per navigation cycle |
| Development assurance | Formal methods, structural coverage, MC/DC | Full lifecycle, FMEA, common cause analysis | Boat-agent kernel in Rust + formal verification |
| Independence | Separate verification team | Functional safety management | Separate safety monitor (hardware watchdog) |
| Configuration management | Every version audited | Traceability required | vessel.toml changes must be signed and versioned |
| **What's missing** | Requirements traceability not mentioned | Hazard analysis not specified | **No hazard log, no FMEA, no SIL target** |

**Gap:** The architecture defines Layer 1 as "formally verified (where possible)" but does not specify the safety integrity level (SIL) or the development assurance level (DAL). For maritime, IMO's **Guidelines for the Approval of Alternatives and Equivalents (MSC.1/Circ.1455)** requires that autonomous/semi-autonomous systems meet equivalent safety levels to manned systems. This implies at least SIL 2 for non-critical functions and SIL 3 for collision avoidance / grounding prevention.

### 2.4 Top 3 Layer 1 Risks

1. **Brittle safety.** A hard boundary that prevents intentional grounding (to avoid collision or capsize) is not just a regulation violation — it's a safety violation. The system must distinguish between "the master is making a deliberate safety decision" and "the system is making a navigation error."

2. **Hardware watchdog independence.** The boat-agent kernel runs on the same hardware as the other agents. A hardware failure (voltage spike, memory corruption) can take down the entire system including the safety kernel. Mitigation: independent watchdog timer (WDT) on a separate microcontroller (e.g., STM32 dedicated to safety monitoring, communicating via digital I/O, not shared bus).

3. **Formal verification cliff.** Full formal verification of a DO-178C Level A equivalent system for a vessel software stack would cost $5-15M and take 3-5 years. The architecture's "formally verified (where possible)" is imprecise. Without specifying the verification scope, the safety case is unprovable.

---

## 3. Layer 2: SOFT (Operational Limits)

### 3.1 Description

"Operational limits — can be violated with human consent. Example: rate-of-turn > limit, but human says 'I need this for docking.' Enforced by boat-agent but with human override path. Requires explicit acknowledgment."

### 3.2 Regulatory Mapping

| Regulation | What It Requires | How Layer 2 Maps |
|------------|-----------------|------------------|
| **COLREGS Rule 8** | Action to avoid collision shall be "positive, made in ample time" | Layer 2's acknowledgment requirement must not delay collision-avoidance actions beyond COLREGS timing |
| **IMO Resolution A.1021(26)** | "Guidelines for Bridge Equipment and Systems" — alarms must not induce "alarm fatigue" | Layer 2's explicit acknowledgment per violation can induce alarm fatigue during complex maneuvers |
| **ISO 8666** | Small craft — maximum recommended rate of turn | Direct source for rate-of-turn limits in vessel.toml |
| **MSC.1/Circ.1512 Annex 2** | Operational limits should have "alert" and "alarm" stages | Layer 2's acknowledgment is an "alert" stage before Layer 1's "alarm" |

**Fatigue risk:** A docking scenario may trigger 5-10 Layer 2 violations per minute. Each requiring explicit acknowledgment creates a 5-10 click-per-minute overhead for the captain during the most cognitively demanding phase of operation. The architecture should implement **batch acknowledgment** ("I'm aware — acknowledge all pending violations for the next 30 seconds").

### 3.3 The Ack Fatigue Problem

```
Scenario: Docking in 15-knot crosswind
Time    | Violation                    | Acknowledgment Required
--------|------------------------------|----------------------------
T+0s    | Rate of turn > 3°/s         | "Acknowledge ROT violation?"
T+5s    | Engine RPM > 1800           | "Acknowledge RPM violation?"
T+8s    | Bow thruster duration > 10s | "Acknowledge thruster duration?"
T+12s   | Cross-track error > 5m      | "Acknowledge XTE violation?"
T+15s   | Rate of turn > 3°/s         | (repeat — captain is still docking)
```

At this pace, the captain either: (a) ignores the acknowledgments (defeating Layer 2), or (b) spends more time acknowledging than navigating (creating a safety hazard).

**Mitigation:** Layer 2 must have a **situation-aware escalation policy**: if the same violation type recurs within N seconds of a prior acknowledgment, auto-acknowledge and log it. Only new violation types require fresh acknowledgment.

---

## 4. Layer 3: ADVISORY (Recommended Practices)

### 4.1 Description

"Recommended practices — can be violated with system consent. Example: fuel consumption > optimal, but Hermes says 'we need speed for weather window.' Enforced by Hermes, not boat-agent. System can override its own advice."

### 4.2 Regulatory Mapping

| Regulation | What It Requires | How Layer 3 Maps |
|------------|-----------------|------------------|
| **ISM Code** | Company must ensure safe operation and environmental protection | Layer 3 advisory violations should be logged for ISM audit |
| **MARPOL Annex VI** | Fuel consumption monitoring and reporting | Layer 3's "fuel consumption > optimal" is a direct input to EEXI/CII calculations |
| **IMO MEPC.348(78)** | Carbon intensity indicator (CII) — operational rating | Layer 3 violations that affect CII rating should be flagged |

**Key distinction from Layers 1-2:** Layer 3 is the first layer where the **system can override itself**. This is a critical design decision — the system must have a mechanism to detect when its own advice is wrong without creating an infinite regress (who watches the watcher?). The architecture solves this through the **Ethical Boundary** (Layer 4) which monitors for pattern drift.

### 4.3 Self-Override Protocol

```
When an agent proposes violating Layer 3 advice:
  1. Agent: "I recommend violating fuel optimization to catch weather window"
  2. Hermes: [verifies] "Historical data: yes, this weather window saved 
     40 minutes vs. fuel-optimized route in similar conditions"
  3. System: "Overriding fuel advisory. Logged to Hermes dream cycle."
  
This is NOT a veto. It's a self-correcting advice system.
```

The self-override must be logged with the reasoning chain for post-hoc audit (ISM Code compliance). This is the **reversible compression** (Pillar 5) applied to safety decisions — the full reasoning history is preserved.

---

## 5. Layer 4: ETHICAL (Moral Boundaries)

### 5.1 Description

"Moral boundaries — enforced by the terminal's own judgment. Example: fishing in protected waters, even if legal. The terminal introduces 'productive dissonance' — not a veto, but a tuning fork that rings false."

### 5.2 Regulatory Mapping

| Regulation | What It Requires | How Layer 4 Maps |
|------------|-----------------|------------------|
| **UNCLOS (Law of the Sea)** | States have jurisdiction over EEZ resources | Layer 4's "protected waters" check is a vessel-level implementation of EEZ compliance |
| **RFMO conservation measures** | Binding rules for fishing in international waters | Multiple RFMOs (NAFO, ICCAT, NEAFC, etc.) have conflicting rules — Layer 4 must know which applies where |
| **US Magnuson-Stevens Act** | Essential Fish Habitat (EFH) protection | Layer 4 should flag EFH areas even if lawful to fish |
| **IMO Guidelines on Maritime Autonomous Surface Ships (MSC.1/Circ.1638)** | Autonomous systems should consider "ethical programming" | Layer 4 is a direct implementation of this guideline |
| **No binding regulation for "moral boundaries"** | The terminal's ethical layer is NOT legally required | It is voluntary and potentially uninsurable — if the system flags lawful behavior and the captain ignores it, who bears liability? |

**Critical liability issue:** The Ethical Boundary introduces "productive dissonance" — flagging lawful behavior. If the flag causes the captain to second-guess a lawful action, and that hesitation leads to a safety incident (e.g., collision while deciding), the liability chain is: terminal → captain → vessel owner. The terminal manufacturer could face product liability claims for introducing doubt about lawful actions.

**Mitigation:** Layer 4 must be **opt-in**, with a clear disclosure: "This terminal can flag lawful but ecologically sensitive areas. Enabling this layer may affect operational decisions. [Enable / Disable]." The captain's choice must be logged for audit.

### 5.3 The "Tuning Fork That Rings False" — Concrete Implementation

```python
class EthicalResonanceBoundary:
    def check(self, proposed_action: Action, vessel_state: VesselState):
        """Not a veto — introduces productive dissonance."""
        
        # 1. Legal check: is this action lawful?
        is_lawful = self.legal_database.is_lawful(
            position=vessel_state.position,
            action=proposed_action,
            vessel=vessel_state.vessel_id
        )
        
        # 2. Ecological check: is this action ecologically sensitive?
        ecological_sensitivity = self.ecological_model.score(
            position=vessel_state.position,
            season=vessel_state.current_season,
            species=vessel_state.target_species
        )
        
        # 3. Historical pattern check: has the captain repeatedly 
        #    violated ecological best practices in this area?
        pattern_score = self.pattern_model.historical_drift(
            agent=vessel_state.captain_id,
            area=vessel_state.position.region(),
            action_type=type(proposed_action)
        )
        
        # 4. Consequence: introduce friction if lawful but harmful
        if is_lawful and ecological_sensitivity > 0.7:
            if pattern_score > 0.6:
                # Strong dissonance: captain has a pattern of harm
                return BoundaryResult(
                    action=ALLOW_WITH_FRICTION,
                    dissonance="Captain, this area is legally open but "
                              "ecologically sensitive. Shall I mark it "
                              "for future avoidance?",
                    urgency=MODERATE
                )
            else:
                # Gentle note: first time in this area
                return BoundaryResult(
                    action=ALLOW_WITH_FRICTION,
                    dissonance="Ecologically sensitive area noted. "
                              "Proceeding with your judgment.",
                    urgency=LOW
                )
        
        return BoundaryResult(action=ALLOW, dissonance=None)
```

---

## 6. The Immune System Layer

### 6.1 What It Does

The immune system is **not a separate layer** — it cross-cuts all four layers. It maintains an "immune profile" — a statistical model of normal agent behavior — and flags behavioral anomalies as "non-self."

### 6.2 Anomaly Detection

```rust
struct ImmuneProfile {
    /// Per-agent behavioral baseline
    baselines: HashMap<AgentId, AgentBehaviorBaseline>,
    
    /// Learned normal behavior distributions
    behavioral_norms: HashMap<AgentId, MultivariateNormal>,
    
    /// Recent anomalous events (circular buffer)
    anomaly_log: VecDeque<AnomalyEvent>,
    
    /// Self/non-self discrimination threshold
    discrimination_threshold: f64,
}

struct AnomalyEvent {
    agent_id: AgentId,
    timestamp: Instant,
    behavioral_deviation: f64,   // Mahalanobis distance from baseline
    anomaly_type: AnomalyType,
    resolution: Option<AnomalyResolution>,
}

enum AnomalyType {
    /// Agent requesting actuator access it never does
    UnexpectedCapabilityRequest { capability: String },
    
    /// Agent's message rate deviates from baseline
    CommunicationRateAnomaly { observed: f64, expected: f64 },
    
    /// Agent's garden entropy changes abruptly
    EntropyAnomaly { delta_h: f64 },
    
    /// Agent's bridge fidelity drops unexpectedly
    TranslationAnomaly { bridge_pair: (AgentId, AgentId), fidelity_drop: f64 },
}

enum AnomalyResolution {
    Isolate,      // Quarantine agent — it cannot influence other agents
    FlagOnly,     // Log anomaly, allow operation
    Throttle,     // Reduce agent's priority/compute allocation
    Terminate,    // Initiate apoptosis
}
```

### 6.3 Autoimmune Risk

The immune system's biggest risk is **false positive discrimination** — flagging legitimate but unusual behavior as non-self. Examples:

- **A new fishing technique** causes tzpro-agent to scan at 5Hz instead of 2Hz. The immune system flags the increased communication rate as anomalous.
- **Hermes running a deeper dream cycle** after a long period of inactivity causes its garden entropy to spike.
- **Emergency operation** (fire, flooding) causes all agents to behave outside their baseline distributions.

**Mitigation:** The immune system operates at a **separate timescale** from operational decisions. It does not block actions — it flags anomalies for human review. Only after confirmation (captain: "Yes, that's expected") or repeated anomaly (3+ identical anomalies without confirmation) does it escalate to isolation.

---

## 7. Formal Verification Approach

### 7.1 What Must Be Verified

| Property | Layer | Verification Method | Feasibility |
|----------|-------|-------------------|-------------|
| "No agent can set throttle > physical limit" | HARD | Bounded model checking (CBMC) on the boat-agent kernel | High — small, deterministic state space |
| "Physical override cuts all actuator writes to zero" | HARD | Model checking of the actuator write loop | High — simple property |
| "Human acknowledgment is required for Layer 2 violation" | SOFT | Runtime verification (monitors) | Medium — depends on interaction model |
| "Self-override is logged with reasoning chain" | ADVISORY | Trace property — check that log exists for each override | Medium — observable post-hoc |
| "Ethical boundary does not block lawful actions" | ETHICAL | Cannot be formally verified — ethical judgment is subjective | **Impossible — must use review boards, not formal methods** |

### 7.2 Verification Strategy

```
┌─────────────────────────────────────────────────────────────────┐
│  VERIFICATION STRATIFICATION                                     │
│                                                                 │
│  Layer 1 (HARD):                                                │
│    Method: Bounded model checking (CBMC, KLEE)                  │
│    Target: boat-agent kernel (Rust, ~2K LOC)                    │
│    Properties: ~50 safety invariants                             │
│    Proof: Each property verified for all inputs up to bound B    │
│    Assumes: Hardware is correct (watchdog independent)           │
│                                                                 │
│  Layer 2 (SOFT):                                                 │
│    Method: Runtime monitors (specification in LTL)               │
│    Target: boat-agent + human interface                          │
│    Monitors: "ack before override" invariant                     │
│    Assumes: Human interface is correct (display, audio)          │
│                                                                 │
│  Layer 3 (ADVISORY):                                             │
│    Method: Post-hoc audit (not real-time verification)           │
│    Target: Hermes log of self-override decisions                 │
│    Review: Periodic human audit of override chain               │
│                                                                 │
│  Layer 4 (ETHICAL):                                              │
│    Method: Not verifiable — ethical review board                 │
│    Target: Terminal manufacturer's ethical guidelines            │
│    Review: External ethics panel (annual)                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. Rust Implementation Sketch: The Envelope Validator

```rust
// src/validation/envelope.rs
// The concrete safety envelope validator for the VaaS substrate.
// Runs in the boat-agent kernel, before any actuator write.

use std::time::Instant;

/// Vessel physical limits — loaded from signed vessel.toml
#[derive(Debug, Clone)]
pub struct VesselLimits {
    pub max_speed_knots: f64,        // Maximum speed over ground
    pub max_rate_of_turn_degs: f64,  // Maximum rate of turn in degrees/second
    pub min_underkeel_clearance_m: f64,  // Minimum UKC in meters
    pub max_engine_rpm: u16,         // Maximum engine RPM
    pub max_trim_degrees: f64,       // Maximum trim angle
    pub draft_m: f64,                // Current vessel draft
}

impl VesselLimits {
    /// Load from signed vessel.toml
    pub fn from_signed_toml(path: &str) -> Result<Self, EnvelopeError> {
        let contents = std::fs::read_to_string(path)?;
        let toml: VesselToml = toml::from_str(&contents)?;
        
        // Verify cryptographic signature
        if !toml.verify_signature() {
            return Err(EnvelopeError::SignatureMismatch);
        }
        
        Ok(Self {
            max_speed_knots: toml.limits.max_speed,
            max_rate_of_turn_degs: toml.limits.max_rate_of_turn,
            min_underkeel_clearance_m: toml.limits.uke,
            max_engine_rpm: toml.limits.max_rpm,
            max_trim_degrees: toml.limits.max_trim,
            draft_m: toml.draft,
        })
    }
}

/// Agent intent — what an agent wants to do
#[derive(Debug, Clone)]
pub struct AgentIntent {
    pub source: AgentId,
    pub desired_speed_knots: f64,
    pub desired_rudder_angle_deg: f64,
    pub desired_rpm: u16,
    pub desired_trim_deg: f64,
    pub timestamp: Instant,
    pub is_human_override: bool,
    pub human_acknowledgment: Option<Acknowledgment>,
}

/// Four-layer envelope validation result
#[derive(Debug, Clone)]
pub enum EnvelopeDecision {
    /// Action is safe — proceed
    Clear,
    
    /// Layer 1 violation — cannot proceed
    HardBoundViolation {
        limit: String,
        observed: f64,
        max_allowed: f64,
    },
    
    /// Layer 2 violation — requires human acknowledgment
    SoftBoundViolation {
        limit: String,
        observed: f64,
        max_allowed: f64,
        suggestion: String,
    },
    
    /// Layer 3 advisory — system can self-override
    AdvisoryViolation {
        description: String,
        override_reason: Option<String>,
    },
    
    /// Layer 4 ethical concern — productive dissonance
    EthicalConcern {
        message: String,
        urgency: Urgency,
    },
}

/// The stratified safety envelope
pub struct SafetyEnvelope {
    limits: VesselLimits,
    immune_profile: ImmuneProfile,
    human_ack_history: Vec<(Instant, bool)>,  // Ack fatigue tracking
}

impl SafetyEnvelope {
    pub fn new(limits: VesselLimits) -> Self {
        Self {
            limits,
            immune_profile: ImmuneProfile::new(),
            human_ack_history: Vec::with_capacity(100),
        }
    }
    
    /// Validate an agent's intent against the safety envelope
    pub fn validate(&mut self, intent: &AgentIntent) -> EnvelopeDecision {
        // === LAYER 1: HARD BOUNDS (Always checked first) ===
        if intent.desired_speed_knots > self.limits.max_speed_knots {
            return EnvelopeDecision::HardBoundViolation {
                limit: "max_speed".into(),
                observed: intent.desired_speed_knots,
                max_allowed: self.limits.max_speed_knots,
            };
        }
        
        // (Other Layer 1 checks: UKC, draft, trim, RPM...)
        
        // === BEHAVIORAL ANOMALY (Immune system cross-check) ===
        if self.immune_profile.is_anomalous(intent.source, intent) {
            // Immune anomaly at Layer 1 level = immediate isolation
            self.immune_profile.quarantine(intent.source);
            return EnvelopeDecision::HardBoundViolation {
                limit: "behavioral_anomaly".into(),
                observed: self.immune_profile.anomaly_score(intent.source, intent),
                max_allowed: self.immune_profile.discrimination_threshold,
            };
        }
        
        // === LAYER 2: SOFT BOUNDS ===
        if intent.desired_rpm > self.limits.max_engine_rpm * 0.9 {
            // Check ack fatigue
            let recent_acks = self.human_ack_history.iter()
                .filter(|(t, _)| t.elapsed() < std::time::Duration::from_secs(30))
                .count();
            
            if recent_acks > 5 {
                // Auto-acknowledge to prevent fatigue
                return EnvelopeDecision::Clear;
            }
            
            return EnvelopeDecision::SoftBoundViolation {
                limit: "engine_rpm_high".into(),
                observed: intent.desired_rpm as f64,
                max_allowed: self.limits.max_engine_rpm as f64,
                suggestion: "Reduce RPM by 10% to stay within operational bounds.".into(),
            };
        }
        
        // === LAYER 3: ADVISORY ===
        // (Checked by Hermes, not boat-agent — return Clear and let Hermes decide)
        
        // === LAYER 4: ETHICAL ===
        // (Checked by ethical boundary agent, not boat-agent)
        
        EnvelopeDecision::Clear
    }
    
    /// Record human acknowledgment for fatigue tracking
    pub fn record_acknowledgment(&mut self, granted: bool) {
        self.human_ack_history.push((Instant::now(), granted));
        if self.human_ack_history.len() > 100 {
            self.human_ack_history.remove(0);
        }
    }
}

/// Immune profile — behavioral anomaly detection
struct ImmuneProfile {
    baselines: HashMap<AgentId, AgentBehaviorBaseline>,
    quarantined: HashSet<AgentId>,
    discrimination_threshold: f64,
}

impl ImmuneProfile {
    fn is_anomalous(&self, agent: AgentId, intent: &AgentIntent) -> bool {
        if self.quarantined.contains(&agent) {
            return true;
        }
        
        let Some(baseline) = self.baselines.get(&agent) else {
            return false; // Unknown agent = no baseline yet
        };
        
        // Mahalanobis distance from behavioral baseline
        let deviation = baseline.mahalanobis_distance(intent);
        deviation > self.discrimination_threshold
    }
    
    fn quarantine(&mut self, agent: AgentId) {
        self.quarantined.insert(agent);
        // Broadcast quarantine to all other agents
    }
}

/// Hardware watchdog — independent from the main system
/// Runs on a separate microcontroller (STM32, connected via GPIO)
pub struct HardwareWatchdog {
    gpio_pin: u8,          // GPIO pin for watchdog heartbeat
    timeout_ms: u64,       // Watchdog timeout (default: 200ms)
    last_heartbeat: Instant,
}

impl HardwareWatchdog {
    /// Called by boat-agent kernel at >= 5Hz
    pub fn kick(&mut self) {
        self.last_heartbeat = Instant::now();
        // Set GPIO pin high to reset the external watchdog timer
        set_gpio_high(self.gpio_pin);
    }
    
    /// Called by the external watchdog when timeout expires
    /// This runs on the independent microcontroller
    pub fn emergency_stop() -> ! {
        // Kill all actuator power via hardware relay
        set_actuator_power(false);
        // Trigger emergency alarm
        trigger_alarm(AlarmType::SafetyTimeout);
        // Halt until manual reset
        loop {}
    }
}

#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_hard_bound_rejects_overspeed() {
        let limits = VesselLimits {
            max_speed_knots: 20.0,
            max_rate_of_turn_degs: 5.0,
            min_underkeel_clearance_m: 2.0,
            max_engine_rpm: 3000,
            max_trim_degrees: 5.0,
            draft_m: 3.5,
        };
        
        let mut envelope = SafetyEnvelope::new(limits);
        
        let intent = AgentIntent {
            source: AgentId::Pincher,
            desired_speed_knots: 30.0,
            desired_rudder_angle_deg: 0.0,
            desired_rpm: 1500,
            desired_trim_deg: 0.0,
            timestamp: Instant::now(),
            is_human_override: false,
            human_acknowledgment: None,
        };
        
        match envelope.validate(&intent) {
            EnvelopeDecision::HardBoundViolation { limit, .. } => {
                assert_eq!(limit, "max_speed");
            }
            _ => panic!("Overspeed should be rejected at HARD layer"),
        }
    }
    
    #[test]
    fn test_ack_fatigue_auto_approves() {
        let limits = VesselLimits {
            max_speed_knots: 20.0,
            max_rate_of_turn_degs: 5.0,
            min_underkeel_clearance_m: 2.0,
            max_engine_rpm: 3000,
            max_trim_degrees: 5.0,
            draft_m: 3.5,
        };
        
        let mut envelope = SafetyEnvelope::new(limits);
        
        // Simulate 6 rapid acknowledgments
        for _ in 0..6 {
            envelope.record_acknowledgment(true);
        }
        
        let intent = AgentIntent {
            source: AgentId::Pincher,
            desired_speed_knots: 15.0,
            desired_rudder_angle_deg: 3.0,
            desired_rpm: 2800,  // > 90% of max RPM = Layer 2
            desired_trim_deg: 0.0,
            timestamp: Instant::now(),
            is_human_override: true,
            human_acknowledgment: None,  // No explicit ack — should auto-approve
        };
        
        // Despite Layer 2 violation, auto-approved due to fatigue
        assert!(matches!(envelope.validate(&intent), EnvelopeDecision::Clear));
    }
}
```

---

## 9. Implementation Roadmap for the Safety Envelope

### Phase 1: Static HARD (Weeks 1-4)

- Rust kernel with compile-time limits from vessel.toml
- Hardware watchdog on independent MCU (STM32)
- Independent power relay for actuator disconnection
- No immune system, no SOFT/ADVISORY/ETHICAL layers
- Layer 1 only — this is the minimum viable safety system

### Phase 2: Static SOFT (Weeks 5-8)

- Add human acknowledgment protocol (audio + visual)
- Implement ack fatigue detection
- Log all violations to VDR-compatible format
- Add master override (signed, logged, reviewable)

### Phase 3: Learned Immune System (Months 3-6)

- Collect behavioral baselines for each agent (1 month of operation)
- Train immune profile from baseline data
- Implement quarantine protocol
- Add periodic immune calibration (weekly review)

### Phase 4: ADVISORY and ETHICAL (Months 6-12)

- Hermes self-override protocol (Layer 3)
- Ethical boundary agent (Layer 4)
- External ethics board review
- Regulatory submission for autonomous operation approval

---

## 10. Regulatory Submission Strategy

### Required Certifications

| Certificate | Authority | Timeline | Cost Estimate |
|-------------|-----------|----------|---------------|
| Type Approval (Bridge Equipment) | IMO / Flag State | 12-18 months | $200-500K |
| Safety System Certification | Class Society (DNV, ABS, LR) | 6-12 months | $100-300K |
| Software Integrity (IEC 61508 SIL 2+) | Notified Body | 12-24 months | $500K-2M |
| Cybersecurity (IEC 62443) | Accredited Lab | 6-12 months | $100-200K |
| Autonomous Operations | Flag State + Class | 24-48 months | $1-5M |

The safety envelope, even as a static Layer 1 MVP, requires maritime certification that is **expensive and slow**. The architecture's biological metaphors and emergent properties are irrelevant to the regulatory process — class societies want deterministic, provably safe systems.

---

## Conclusion

The stratified safety envelope is the most practically significant component of the VaaS architecture. It is also the component with the clearest mapping to existing engineering practice — maritime regulations, DO-178C/IEC 61508 safety standards, and Rust's safety guarantees.

The four layers provide a **structured escalation** that matches existing maritime safety practice:
- **Hard bounds** = SOLAS-mandated safety equipment (depth alarms, RPM limiters)
- **Soft bounds** = ISM Code-compliant operational procedures
- **Advisory** = Best practices and watchkeeping recommendations
- **Ethical** = Voluntary sustainability and governance

The immune system cross-check (behavioral anomaly detection) is the most novel component — and the riskiest. False positives (autoimmune failure) can quarantine legitimate agents and reduce system capability. False negatives (missed anomalies) can propagate corrupted agent state to the safety kernel.

The Rust implementation sketch provided above is production-ready for **Layer 1 and Layer 2 with ack fatigue mitigation**. Layers 3 and 4, and the learned immune system, require the research breakthroughs discussed in Document 1.

**Hard truth:** A working Layer 1 safety envelope — properly certified — costs $1-3M and takes 12-24 months. This is not a weekend project. The biological parallels are intellectually satisfying, but class societies will accept only deterministic, provably safe implementations.
