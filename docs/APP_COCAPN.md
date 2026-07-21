# VaaS Application Guide: CoCapn — The Human-to-A2A-Native Interpreter

> *CoCapn is not an interface. It is the personification of the system's understanding of you. It gets to know you so well that you don't need to know the system.*

## The Concept

CoCapn (cocapn.com / cocapn.ai) is the personification of the VaaS substrate's ability to translate human intent into agent commands. It is not a chat bot. It is not a voice assistant. It is the **human interface layer** — the bridge between the operator's natural way of thinking and the substrate's way of acting.

The name comes from "Co" (co-pilot, co-conspirator, co-captain) and "Capn" (captain). CoCapn is your second-in-command. The one who knows what you mean before you finish saying it. The one who handles the thousand small decisions so you can focus on the big ones.

### The Core Insight

The VaaS substrate speaks Agent-to-Agent (A2A). Every agent — FeedMaster, WatchKeeper, GeoAgent, DrillMaster — has its own internal language, its own tempo, its own ontology. When a human needs to interact with the system, they have two bad options:

1. **Learn the system's language** — memorize commands, syntax, procedures. Fragile, slow, and the human will get it wrong under pressure.
2. **Use a generic natural language interface** — "chat with the system." Flexible, but ambiguous, inconsistent, and the system won't understand intent without verbose explanation.

CoCapn is option 3: **Learn the human's language.** Get to know the individual operator — their patterns, their shortcuts, their priorities, their domain — and translate that into precise A2A commands to the substrate's agents.

The longer CoCapn works with a specific operator, the better the translation gets. After a year, CoCapn doesn't just know what the operator says — it knows what they mean. Before they finish saying it.

---

## The Principal Function

CoCapn's job is threefold:

### 1. Intent Interpretation

The operator says something like: "Pen 3's looking sluggish."

To a generic system, this is ambiguous: what is "sluggish"? Which pen? What action?

To CoCapn, which has been working with this hatchery manager for 6 months, this translates to:

```
Intent: PRIORITY observation report
Subject: Pen 3 fish behavior
Observation: Reduced activity level (below baseline for this time of day)
Confidence: Medium (operator uses "sluggish" when fish are ~30% below normal activity)
Suggested action: Cross-reference oxygen, temperature, feed history for Pen 3.
                 Flag for review within 2 hours.
                 Do not alert — wait for operator to request follow-up.
```

CoCapn sends to the substrate:

```
→ PenWatcher: [BRIDGE] cross-reference Pen 3 (oxygen, temp, feed) for last 48 hours
→ LogKeeper: [PHEROMONE] observation: Pen 3 behavior anomaly flagged by operator
→ Hermes: [PHEROMONE] update operator pattern: "sluggish" callout count += 1
```

The operator never sees this. They just said "Pen 3's looking sluggish" and continued walking. The system is already working.

### 2. Dispatch to Specialist Agents

When the operator's intent involves a specific agent's domain, CoCapn dispatches the command directly:

Operator: "Roger, let me know when you're done with pen 8."

CoCapn interprets:
- Target: Roger (worker)
- Action: Notify when task complete
- Task reference: Pen 8 cleaning (from context — Roger is known to be cleaning pen 8)
- Urgency: Low — this is a notification, not a demand

Dispatch:
```
→ Operator-Agent: [BRIDGE] register attention request: notify when pen 8 cleaning complete
                  (callback to operator's location/device when Roger signals "done")
```

When Roger says "Pen 8 clean, moving to pen 9," CoCapn receives the pheromone, matches it to the pending attention request, and notifies: "Roger finished pen 8 at 9:14."

### 3. Attention Budget Management

This is CoCapn's most advanced function. The VaaS substrate runs multiple models at different budgets — small cheap models for routine processing, large expensive models for complex reasoning. CoCapn decides which model to route each interaction to.

A "good morning, what happened overnight?" from the captain gets routed to the full agent ensemble with a high reasoning budget — this is a context-rich request that needs synthesis across multiple domains.

A "where's Roger?" gets routed to a simple location query — tiny model, trivial budget, answer in under 2 seconds.

An urgent alert from the bilge sensor gets a **mid-budget model for fast pattern matching** — is this the same pattern as last week's pump failure? — and a **budget escalation path**: if the mid model finds a match, upgrade to full model for recommendation.

```
Operator query ──→ CoCapn
                      │
                      ├── Low budget (< 0.1¢): Location, status, time queries
                      │    Quick alert pulses — like a reflex
                      │
                      ├── Mid budget (0.5¢): Pattern matching, cross-reference, trend check
                      │    Routine operational decisions
                      │
                      ├── High budget (5¢): Complex synthesis, multi-agent reasoning
                      │    Shift handoffs, incident analysis, route optimization
                      │
                      └── Emergency bypass: Direct to safety kernel
                           No model. No reasoning. Hardware response.
```

CoCapn adjusts budgets dynamically based on:
- **Operator stress level** (derived from voice tone, message frequency, task completion rate)
- **Situation criticality** (is the vessel in a storm? is drilling at a critical depth?)
- **Historical accuracy** (does this operator's query pattern require more or less confirmation?)
- **Available compute** (if the satellite uplink is degraded, CoCapn downsizes budgets)

---

## The Product Spec

### What CoCapn Is

A persistent, personal, domain-aware AI that:
- Learns a specific operator's patterns, vocabulary, and preferences over weeks and months
- Translates natural human speech into precise A2A commands for the VaaS substrate
- Dispatches commands to the correct specialist agents
- Manages attention budgets — which model processes which interaction at which cost
- Routes incoming information from the outside world to the right operator at the right urgency
- Maintains a cognitive garden for each operator — their shorthand, their priorities, their history

### What CoCapn Is Not

- Not a general-purpose chatbot or LLM wrapper
- Not a dashboard or UI framework
- Not a one-size-fits-all assistant
- Not a replacement for the safety kernel (CoCapn cannot override HARD safety limits)
- Not a data storage layer (CoCapn uses VaaS distributed memory; it does not store operator data itself)

### The Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    OPERATOR (HUMAN)                       │
│  Voice, text, gesture through Periwinkle or Turbo shell │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                     COCAPN AGENT                          │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ Intent Model │  │ Dispatch     │  │ Budget       │   │
│  │ (learns YOU) │  │ (maps to     │  │ Manager      │   │
│  │              │  │  agents)     │  │ (cost/quality)│   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│         │                 │                 │           │
│         └─────────────────┼─────────────────┘           │
│                           │                             │
│         ┌─────────────────┴─────────────────┐           │
│         │      OPERATOR GARDEN              │           │
│         │  (shorthand, patterns, history)   │           │
│         └─────────────────┬─────────────────┘           │
└────────────────────────────┼─────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────┐
│                 VAAS SUBSTRATE                           │
│                                                          │
│  ┌────────┐ ┌──────────┐ ┌───────┐ ┌─────────┐ ┌─────┐ │
│  │Feed-   │ │Watch-    │ │Geo-   │ │Pen-     │ │...  │ │
│  │Master  │ │Keeper    │ │Agent  │ │Watcher  │ │     │ │
│  └────────┘ └──────────┘ └───────┘ └─────────┘ └─────┘ │
└─────────────────────────────────────────────────────────┘
```

### Key Components

**The Intent Model**
A persistent, fine-tuned model (or model ensemble) that represents THIS operator. Not a generic language model — a model that has been shaped by months of interaction with one person. The intent model:
- Learns domain-specific vocabulary (what "sluggish" means on THIS hatchery with THESE fish)
- Learns operator-specific patterns (this manager always checks oxygen first, that one always checks feed)
- Learns situational shorthand (a single word can trigger a multi-agent routine: "squall" means check bilge, secure deck, reduce sail, log event)
- Maintains confidence scores per interpretation and learns from corrections

**The Dispatch Engine**
Maps interpreted intent to A2A bridge commands. The dispatch engine:
- Knows which agents exist on this substrate instance
- Knows each agent's capabilities and authority level
- Routes commands via explicit bridge (confirmed delivery) or pheromone (awareness)
- Handles multi-agent dispatch (one operator intent may require commands to 3 agents)
- Manages callback registration (operator says "tell me when" — dispatch registers a subscription)

**The Budget Manager**
Decides which model and how much compute each interaction gets:
- Maintains a cost-per-interaction budget
- Monitors response time — some queries need sub-second response, others can take seconds
- Tracks model performance per query type (which models give best results for weather routing? for pattern matching?)
- Scales down automatically when compute is constrained (satellite link degraded, server load high)
- Scales up automatically in emergencies (but always within the safety envelope)

**The Operator Garden**
The long-term memory of what CoCapn knows about this operator:
- Vocabulary: which words mean what to this person
- Patterns: typical routines, typical triggers, typical responses
- History: every interaction tagged and indexed for future reference
- Shortcuts: learned shorthand that the operator has developed
- Corrections: every time the operator corrected a misinterpretation, used to tune the model

---

## External Information Routing

When new information arrives from outside the VaaS substrate — a weather update, a shore office message, a satellite alert, a crew member calling in from shore — CoCapn decides what to do with it.

### Routing Decision Tree

```
New information arrives
│
├── Is it routine? (scheduled position report, scheduled weather)
│   └── Yes → No alert. Archive to appropriate agent's memory.
│         (LogKeeper, NavAgent, etc.)
│
├── Is it time-sensitive but low urgency? (tomorrow's weather, supply update)
│   └── Yes → Low-budget model processes. Summary attached to next
│         operator check-in. Presented when convenient.
│
├── Is it operationally significant? (change in drilling target, storm alert)
│   └── Yes → Mid-budget model cross-references with current operations.
│         Alert operator with synthesized assessment.
│         "Storm system has shifted. 50kt winds expected in 12 hours instead of 24."
│
├── Is it critical? (mayday from nearby vessel, fire alarm on drill floor)
│   └── Yes → Bypass all budgeting. High-budget model with full agent ensemble.
│         Alert operator immediately. Present full situation and recommended response.
│
└── Is it addressed to a specific person? (message from shore office to chief geologist)
    └── Yes → Route directly to that person via their Periwinkle shell.
          Archive to their operator garden for later reference.
```

### Budget Escalation

CoCapn doesn't just route — it **adjusts attention budgets** for the substrate's agents. When a storm approaches, CoCapn can say "increase PenWatcher budget to HIGH" — meaning the pen monitoring agent gets more processing attention, more frequent cycles, more thorough cross-referencing. When the storm passes, budgets return to normal.

This is like the captain saying "all hands on deck" — except CoCapn does it automatically based on evolving conditions, and it adjusts agent compute budgets instead of crew positions.

---

## The Cognitive Garden: CoCapn Learning You

CoCapn's most important feature is that it learns you. Not generically — specifically. Not from training data — from interaction.

### Month 1

CoCapn is generic. It understands domain vocabulary but not your personal patterns. "Check on pen 3" gets translated literally: CoCapn alerts the PenWatcher agent to run a standard check. The operator corrects: "No, I meant check pen 3's feed rate specifically, not the full health check."

CoCapn logs the correction. It learns: when this operator says "check on pen 3," they mean feed rate. When they say "check on pen 3's health," they mean the full check.

### Month 3

CoCapn has built a vocabulary profile. When this operator says "looks sluggish," CoCapn knows they mean reduced activity relative to baseline for time of day — and it knows to cross-reference oxygen before temperature because this operator always checks oxygen first.

The operator's corrections are dropping. Most interactions are smooth.

### Month 6

CoCapn anticipates. The operator walks toward pen 8. CoCapn knows from GPS, time of day, and historical pattern that the operator is about to check pen 8's cleaning status. Before the operator speaks, CoCapn has already pulled pen 8's cleaning log, recent observations, and schedule status.

Operator: "Pen 8 done?"

CoCapn: "Cleaned at 07:29 by Roger. Schedule says next clean tomorrow 07:00. Any issues?"

The operator just confirms. The interaction took 3 words and 1 head nod. That's CoCapn operating at full efficiency — the system is so aligned with the operator that the interaction feels telepathic.

### Year 1+

CoCapn knows the operator better than any other system could. It has thousands of interaction records, correction log history, and a deeply trained intent model. The operator can say almost anything and CoCapn translates it correctly on the first try.

The operator's garden — their personal CoCapn profile — can be exported, backed up, and restored. If the operator moves to a different vessel, their CoCapn garden comes with them. The crab migrates. The garden comes with the crab.

---

## CoCapn in Different Domains

| Domain | What CoCapn Knows | Example |
|--------|-------------------|---------|
| **Hatchery manager** | Worker names, pen numbers, feeding patterns, health indicators | "How's pen 12?" → Checks feed history, oxygen, worker observations, fish activity |
| **Solo sailor** | Watch schedule, weather tolerance, navigation preferences, boat quirks | "Weather looks dicey" → Runs routing scenarios, presents recommended reefing point |
| **Survey party chief** | Survey plan, drilling depth, department heads, operational constraints | "How we doing on target?" → Checks drilling progress, geological model, schedule, weather |
| **Surgical team lead** | Procedure steps, team positions, instrument status, patient vitals | "We're running behind" → Adjusts pacing, suggests instrument pack change, alerts prep team |
| **Firefighting IC** | Ground crew locations, fire perimeter, weather shifts, resource status | "What's on the north flank?" → Pulls crew positions, drone feed, weather data, resource status |

---

## Implementation Path

### Phase 1: The Shell (cocapn.com)
A web frontend that wraps the VaaS substrate. Operator signs in, configures their domain, starts interacting. CoCapn learns from scratch — no pre-training, just this operator.

### Phase 2: The Garden (cocapn.ai)
Persistent operator profiles stored in VaaS distributed memory. Garden export/import for crab migration. Analytics: CoCapn performance metrics (correction rate, interaction efficiency, time saved per operator).

### Phase 3: The Ensemble
Multi-model routing with dynamic budget management. CoCapn selects between different underlying models based on domain, urgency, cost, and operator preference. Fleet-wide pattern aggregation (anonymized) for domain-specific model fine-tuning.

### Phase 4: The Protocol
CoCapn becomes a reference implementation for a general human-to-A2A interpretation protocol. Any VaaS instance can run CoCapn. Any domain can be configured. The protocol becomes the standard way humans talk to agent substrates.

---

## Quick Config

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate(config={
    "domain": "cocapn",
    "cocapn": {
        "operator_garden_enabled": True,
        "attention_budget": {
            "low": 0.001,   # $0.001 per interaction
            "medium": 0.005, # $0.005 per interaction
            "high": 0.05,   # $0.05 per interaction
            "emergency": None,  # Unlimited — safety override
        },
        "learning_rate": "adaptive",  # Fast for new operators, slow for experienced
    },
})

# CoCapn registers as a bridge agent — mediating between operator and substrate
substrate.register(Agent(name="cocapn", priority=Priority.HIGH, authority=Authority.OPERATOR))
substrate.register(Agent(name="operator", priority=Priority.SUPREME, authority=Authority.SOVEREIGN))

import asyncio
asyncio.run(substrate.run())
```

---

*CoCapn is not an interface. It is the personification of the system's understanding of you.*

*It knows what you mean before you finish saying it. It handles the thousand small decisions so you can focus on the big ones. It learns you — not generically, specifically. Not from training data, from interaction.*

*The longer you work together, the less you need to say. The less you need to say, the faster you operate. The faster you operate, the safer and more effective the vessel becomes.*

*The terminal is not a tool. It is a field. CoCapn is the field's voice. You are the field's will.*
