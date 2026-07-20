# Safety Envelope Deep Dive

> This is the most important document in the VaaS system. If you read nothing else, read this.

## Overview

The safety envelope is a stratified, multi-layer safety system. It sits between every AI agent and every physical actuator. Nothing reaches the autopilot, engine, or any control surface without passing through the envelope.

## The Four Layers

### Layer 1: HARD (Physical Impossibilities)

These rules enforce what is PHYSICALLY impossible, not just undesirable. They can NEVER be overridden by anyone — not the captain, not the AI, not a bug, not a hack.

Examples:
- **Draft exceeds depth:** If the boat draws 4.5m and the water is 4m deep, the command is rejected. Physics says no.
- **Rate of turn exceeds structural limits:** If the hull is rated for 15°/s maximum turn rate, no command can exceed it.
- **Speed exceeds hull speed:** If the hull can't physically go that fast, the command is rejected.

Implementation: Enforced by the Rust kernel at the hardware level. The check happens BEFORE the command reaches the serial port. There is no software bypass.

```rust
// boat-agent core/src/envelope/hard.rs
pub fn check_hard_bounds(intent: &Intent, vessel: &VesselConfig) -> Result<(), RejectReason> {
    if intent.heading_change_abs() > vessel.max_rate_of_turn {
        return Err(RejectReason::HardBoundViolation("Rate of turn exceeds limit"));
    }
    if intent.depth_below_keel() < vessel.min_keel_clearance {
        return Err(RejectReason::HardBoundViolation("Insufficient keel clearance"));
    }
    Ok(())
}
```

### Layer 2: SOFT (Operational Limits)

These are rules that CAN be violated — but only with the captain's explicit acknowledgment. The system warns. The captain overrides.

Examples:
- **Rate of turn exceeds recommended practice** (but is physically possible) — captain can override for docking maneuvers.
- **Speed exceeds optimal fuel efficiency** — captain can override for weather window.
- **Course change in restricted waters** — captain can override with "I know, I'm threading a needle."

Implementation: The system asks for acknowledgment. The captain must confirm within 10 seconds or the command is dropped.

```rust
// boat-agent core/src/envelope/soft.rs
pub fn check_soft_bounds(intent: &Intent, vessel: &VesselConfig) -> SoftDecision {
    if intent.speed_kts > vessel.recommended_speed {
        return SoftDecision::WarnWithOverride {
            message: "Speed exceeds recommended maximum. Acknowledge?",
            timeout_seconds: 10,
        };
    }
    SoftDecision::Clear
}
```

### Layer 3: ADVISORY (Best Practices)

The system recommends against certain actions but doesn't prevent them. It's the "are you sure?" layer.

Examples:
- **Fuel consumption above optimal for the planned route**
- **Course takes vessel into known heavy-weather area**
- **Anchoring in area with poor holding ground**

Implementation: The system logs the advisory and informs the captain via voice/chart. The captain proceeds without needing to acknowledge.

### Layer 4: ETHICAL (Moral Boundaries)

This is the most innovative and controversial layer. The system doesn't prevent the action. Instead, it introduces **productive dissonance** — a subtle signal that something feels wrong.

Examples:
- **Fishing in legally-open but ecologically sensitive waters**
- **Course takes vessel close to a known marine mammal aggregation**
- **Operating pattern suggests increasing risk-taking over time**

Implementation: The system doesn't block. It says something like: "Captain, this area is legally open but the latest surveys show declining stocks. Shall I mark it for seasonal avoidance?" The captain can proceed. But the system registered its concern.

## The Immune System

Layer 1-4 are rule-based. The immune system is behavior-based — it watches for ANOMALIES.

Each agent has a behavioral profile (learned over time). If an agent starts behaving differently — Pincher suddenly requesting actuator control (it never does that), or Hermes outputting commands instead of analysis — the immune system flags it as anomalous and isolates the agent.

```
Agent normal behavior profile (learned):
  pincher → emits: reflex commands (steering, throttle)
  hermes → emits: analysis, queries, summaries
  tzpro → emits: visual observations, depth reports

Anomaly detected:
  pincher → emits: "change route to X" ← PINCHER DOESN'T DO THIS
  hermes → emits: "steer 240°" ← HERMES DOESN'T STEER

→ Immune system isolates the agent (apoptosis)
```

## The Veto System

### Physical Veto (Absolute)

The captain grabs the wheel, throttle, or jog lever. A physical switch detects human contact. The Rust kernel sees this at 10 Hz (every 100ms). Within one tick, ALL AI actuator writes are zeroed. The captain has raw control.

This is not a software feature. It's a hardware feature. The switch is physical. The kernel reads it on bare metal.

### Resonance Veto (Gradient)

The captain says "Hmm..." during a maneuver. The system detects uncertainty in their voice. It doesn't cut AI power — it MODULATES it:
- Pincher reduces rate of turn by 50%
- boat-agent widens safety envelope margins
- tzpro-agent scans ahead more frequently
- Hermes analyzes why the captain is uncertain

The captain's uncertainty propagates through the system as a frequency shift. The system becomes more cautious without being told to stop.

```
Captain: "Hmm..."
  │
  ├─→ Pincher: reduce turn rate 50%
  ├─→ boat-agent: widen envelope 30%
  ├─→ tzpro-agent: increase scan frequency
  └─→ Hermes: analyze source of uncertainty
```

## Formal Verification Approach

The safety envelope must be formally verified. We use techniques from aviation (DO-178C) and nuclear (IEC 61508):

1. **Requirements traceability:** Every safety rule maps to a requirement, which maps to code, which maps to a test.
2. **Fault tree analysis:** For every possible failure mode, trace back to root causes and verify mitigation.
3. **Model checking:** Use TLA+ or similar to verify temporal properties of the envelope.
4. **Worst-case execution time:** The HARD layer check must complete in <1ms. Verified by static analysis of the Rust code.
5. **Coverage analysis:** 100% MC/DC (Modified Condition/Decision Coverage) for the HARD layer.

---

[← Back to README](../README.md) · [← Engineer's Guide](ENGINEERS_GUIDE.md) · [→ Architecture Reference](ARCHITECTURE_REFERENCE.md)
