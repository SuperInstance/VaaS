# The Attention Budget: Dynamic Model Routing Per Station

> *Not every station needs the most expensive model all the time. The system routes compute where the situation demands, and falls back to cheap reactive pulses when it does not.*

## Why a Budget Exists

The temptation, on a powerful multi-agent substrate, is to wire every agent to the strongest available language model. If the engineering budget allows it, *why not*? The fishing boat gets the strongest navigation model; the wheelhouse gets the strongest vision model; every station burns at full power all the time.

There are three answers to *why not*.

**Cost.** Even on-device models have a runtime cost. Battery, thermals, network — strong models are expensive. On a vessel with finite generator capacity, on a tactical kit with a limited power budget, on a satellite link with metered bandwidth, burning the strongest model at 10 Hz on every station is impossible. The cost is not theoretical.

**Latency.** Strong models are slower. A 20 Hz reflex loop (Pincher) cannot run on a model whose mean latency is 250 ms. Even if the cost were free, the tempo constraints of the Polyrhythmic Substrate (Pillar 4) would force weaker models on the faster agents. Tempo dictates model selection independent of cost.

**Coherence.** When everything is loud, nothing is heard. If every station emits at maximum fidelity, the bridges (Pillar 5) drown in translation work, the Operator Field Ψ(t) becomes noisy, and the human via CoCapn receives a firehose. The system needs *contrast*. To hear the navigation agent's sudden structural mismatch, the navigation agent must usually be running at low fidelity — that way the mismatch registers as a *change*, not as another drop in the firehose.

The attention budget is therefore a feature, not a compromise. It is the mechanism by which the substrate produces *meaningful variation* across stations.

## What a Budget Is

Every vessel, every station within the vessel, and every model available to that station has an **attention budget**.

- A **vessel budget** is the total compute the substrate is willing to spend per unit time. It is determined by hardware (CPU/GPU), by energy budget (battery or generator), by network budget (satellite link capacity), and by an operator-set ceiling. The total budget is denominated in *tokens per second across the substrate*, or in *GFLOPs per second*, or in a unit-native internal accounting that the budget manager can convert.

- A **station budget** is the share of the vessel budget allocated to a station-agent. It varies over time. The navigation station might be allocated 8% of the vessel budget in open water and 35% when entering port. The reflex station is allocated near-constant minimum budget (it is cheap and fast) plus burst budget during anomalies.

- A **model slot** is one of several candidate models that can serve a station. The cheapest might be a 1B-parameter local model with 30 ms latency. The most expensive might be a 200B-parameter remote model with 800 ms latency and a 2 USD/M-token price tag. Each station can route to one of its slots at any given moment, depending on the budget allocation.

The budget manager — implemented as a service inside the substrate — observes Ψ(t) and recomputes station budgets every cycle. Recomputation is itself rate-limited so that the manager does not oscillate. A hysteresis function ensures that a station does not flip from cheap model to expensive model and back within a 5-second window unless a true emergency is detected.

## How Budgets Are Calculated

Every station's current budget allocation is the result of three combined factors.

**Factor 1: Station tempo.** A station's intrinsic tempo gives it a baseline. Pincher at 20 Hz gets more compute per second than hermes at 0.2 Hz. Baseline allocation = `floor(tempo × model_floor_tokens)`.

**Factor 2: Situation salience.** The substrate computes a *situation vector* per station per cycle. It is a small set of indicators: structural_mismatch_rate, bridge_entropy, immune_anomaly_score, confidence_distribution, pheromone_pressure, constitutional_concern_level. Each indicator is normalized to [0, 1]. The salience is the max (or 95th percentile) of the indicators. A station with structural_mismatch_rate = 0.9 is in high salience; one with all indicators < 0.1 is in low salience.

**Factor 3: Constitutional weighting.** Some stations have a permanent higher weight than others. boat-agent (the Rust kernel) always gets full budget. Safety is not subject to economizing. Pincher gets a minimum budget it cannot be cut from. The constitutional weighting is operator-set and survives budget recomputations.

The combined allocation is roughly:

```
budget_i = clamp(
    constitutional_floor_i
      + constitutional_weight_i × tempo_i × model_floor_tokens
      + salience_multiplier × max_salience_i(t),
    min = constitutional_floor_i,
    max = constitutional_ceiling_i
)
```

This is not a formula so much as a structure. The actual implementation can be a learned controller — a small neural network that takes Ψ and outputs per-station budgets. The substrate's history is the training set. The substrate's near-term future is the validation set.

## Routing Decisions

Given a budget, the station must route its queries to a model slot. There are three sub-decisions.

**Sub-decision 1: Latency tier.** The reflex agent, with a 50 ms per-cycle budget, must pick a slot whose mean latency fits within that budget. The slower tiers are categorically unavailable to it. Latency is the *hard* constraint.

**Sub-decision 2: Quality tier.** Within latency-permissible slots, the station picks the *highest-quality* slot it can afford given its current budget. Quality is a function of the model's measured benchmark performance against the station's historical task profile. A 1B model rated 60% on the station's task profile is outclassed by a 7B model rated 80% on the same profile. The station picks the latter if the budget allows.

**Sub-decision 3: Burst routing.** Even with a constrained average budget, the station can spend a *burst* — a momentary overspend — when salience spikes. Burst is funded from a vessel-wide burst pool. The burst pool has a refill rate. A station that bursts today cannot burst again until the pool refills, unless a true emergency (constitutional override) authorizes it.

Routing decisions are recorded as *route events* in the substrate's audit log. The audit log feeds dream cycles (Pillar 1). If a station has been bursting repeatedly and discovering that its bursts did not need to be bursts — that is, the salience that triggered them was transient — the dream cycle lowers the salience trigger threshold for that station. Conversely, if a station has been missing things because its budget was too low, the dream cycle raises the trigger.

## Alert Pulses: The Cheap Backstop

When a station is running at low fidelity, it cannot see everything. A 1B model at 1 Hz on a sonar feed will miss micro-perturbations that a 7B model at 5 Hz would catch. The substrate's answer to this is the **alert pulse**.

An alert pulse is a *very cheap, very narrow* model — sometimes a non-LLM detector — that watches for specific classes of anomaly. It runs at *high tempo* (10–50 Hz is common) on a *budgeted-near-zero* compute cost. Its job is not to interpret; its job is to *flag*.

When an alert pulse fires, three things happen:

1. The station's salience score for that anomaly class spikes, triggering a budget reallocation.
2. A burst is requested from the vessel's burst pool.
3. CoCapn is notified that a station is escalating; she may speak to the operator if constitutional preference warrants.

Alert pulses are *not* the model's interpretation; they are the smoke detector. They are designed to have very low false-negative rates and acceptable false-positive rates. A pulse that fires too often (false positives) wastes burst budget; a pulse that fires too rarely (false negatives) is dangerous. Tunable per station.

Concretely, an example alert pulse: a peak-detector on the depth-against-heading signal. It watches for sudden depth changes that suggest shoaling. It does not know what kind of shoal, does not classify the bottom, does not produce a textual description. It fires a binary event with a salience weight. The full model is woken *after* the alert pulse confirms something needs interpreting.

## How CoCapn Manages the Budget Across Stations

CoCapn is the meta-controller of the attention budget. She does not directly compute budget formulas; instead, she sets *policy and override conditions*, and watches for *system-level* anomalies.

CoCapn's inputs to the budget manager:

- **Constitutional preferences.** *"Always keep tzpro-agent on the medium tier; the captain wants sonar interpretation."*
- **Per-human overrides.** *"This human does not want navigation model changes during rough weather."*
- **Escalation conditions.** *"If the operator field shows >3 active conflicts, enter crisis mode: redistribute from low-salience stations to high-salience stations."*

CoCapn's outputs from the budget manager:

- **Coherence reports.** Ψ(t) as the substrate currently computes it. Where coherence is dropping, CoCapn may speak first.
- **Readiness register.** How prepared the substrate is to escalate a station's model tier at a moment's notice. Readiness depends on the burst pool, the latency-tier headroom, and the cooling budget of each station's hardware.

CoCapn performs **budget arbitration** when two stations both demand a burst at the same moment and the burst pool cannot fund both. The arbitration policy is constitutional: safety wins, then by priority, then by recency, then by salience. In a true crisis (multiple stations at maximum salience) CoCapn enters **crisis-budget mode** — every station's constitutional ceiling is unlocked, the burst pool is fully drained, and the substrate accepts that some lower-priority stations may go silent temporarily.

Crisis mode is loud and visible. CoCapn announces it. *"Captain, entering crisis-budget mode; sail trim silence for the next two minutes while I focus on the navigation problem."* When the crisis resolves, CoCapn announces exit and the budget rebalances.

## Failure Modes and Recovery

The attention budget has its own failure modes. The substrate is designed to recognize them.

**Budget thrashing.** A station rapidly alternates between tiers, possibly because salience is noisy. The hysteresis function prevents this; when hysteresis fails, dream cycles diagnose and tighten the function. CoCapn is *not* called for this — it is silent infrastructure failure.

**Starvation.** A station has been at its constitutional floor for too long without being able to burst, even when salience is high. Possible cause: burst pool is depleted because another station has monopolized it. CoCapn steps in and arbitrates.

**Over-spend.** A station has been bursting continuously. Possible cause: constitutional ceiling is too low, or the salience function is over-sensitive. Dream cycles diagnose and adjust ceilings upward (if constitutional concerns permit) or trigger station-level apoptosis if the situation is unrecoverable.

**CoCapn-induced oscillation.** CoCapn's policy changes may cause the budget to flip-flop across stations. CoCapn's policy is itself subject to a hysteresis function. A dream cycle on CoCapn's policy engine can damp overly aggressive oscillation.

**Calibration drift.** Over months, the attention budgets may no longer match the operator's needs. The substrate runs a calibration cycle weekly where Ψ and budget outcomes are reviewed against operator corrections. Calibration is slow, intentional, and recorded.

## Why This Matters for Open Terminal and CoCapn

The attention budget is the *connective tissue* between Open Terminal (where each agent stores its state) and CoCapn (who speaks to the human). Without the budget:

- Each agent would run at fixed model tier regardless of need — wasteful.
- CoCapn would have to summarize every agent at full fidelity all the time — overwhelming.
- Bridges would carry too much shadow context — slow and lossy.
- The Operator Field would be noisy — useless for monitoring.

With the budget, every component of the substrate has a defensible reason for its current resource allocation. That is the *contract* the substrate signs with the operator: you do not pay for what you do not need, and you do get what you do need.

The budget is also the bridge between *the substrate* and *the economics of running the substrate*. A user-visible dial — *"how attentive should the system be right now?"* — maps directly to the budget manager. The dial can be automatic (driven by Ψ and constitutional preferences) or manual (the operator overrides). CoCapn's voice is the user's readback on what the dial is doing.

---

*See also: [Open Terminal](OPEN_TERMINAL_AGENT.md) · [CoCapn Design](COCAPN_DESIGN.md)*
