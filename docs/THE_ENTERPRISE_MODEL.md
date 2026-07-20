# THE ENTERPRISE MODEL
## How VaaS + Open Terminal + CoCapn + OpenConstruct becomes a starship bridge

---

Casey just said something that reorganizes the entire architecture. Let me trace it.

The original VaaS vision: a multi-agent cognitive substrate for a fishing boat. Seven pillars. An Operator Field. Gardens and bridges and pheromones.

The expanded vision: **VaaS is the substrate. Open Terminal is the agent's workspace. CoCapn is the router. OpenConstruct is the onboarding. Open-mind is the code understanding. Together, they form the Enterprise bridge.**

Here's how the pieces connect.

---

## The Stations

On the Enterprise bridge, there are stations. Navigation. Security. Engineering. Science. Communications. Each station has a specialist. Each specialist has a terminal — their interface to the ship's systems for their domain.

In the VaaS architecture, each station is an agent. The agent's terminal is Open Terminal — but not Open Terminal as a human's command line. Open Terminal as the AGENT'S workspace. The terminal that gets to know the agent, not the human.

```
NAVIGATION STATION          SECURITY STATION
┌──────────────────┐        ┌──────────────────┐
│ Agent: nav-agent │        │ Agent: sec-agent │
│ Terminal: knows   │        │ Terminal: knows  │
│   charts, tides,  │        │   cameras,       │
│   routing, weather│        │   zones, alerts  │
│ Garden: shorthand │        │ Garden: shorthand│
│   for nav commands│        │   for sec ops    │
└────────┬─────────┘        └────────┬─────────┘
         │                           │
         └───────────┬───────────────┘
                     │
          ┌──────────┴──────────┐
          │      CoCapn         │
          │  (The Computer)     │
          │                     │
          │ Routes between      │
          │ stations. Learns    │
          │ the human. Adjusts  │
          │ attention budgets.  │
          └──────────┬──────────┘
                     │
          ┌──────────┴──────────┐
          │    THE CAPTAIN      │
          │  (The Human)        │
          │                     │
          │ Talks to CoCapn.    │
          │ CoCapn routes to    │
          │ the right station.  │
          └─────────────────────┘
```

## Open Terminal: The Agent's Terminal

The current Open Terminal (github.com/SuperInstance/open-terminal) is "a fork of Windows Terminal with native agent integration." It's built for a human to interact with an agent.

The reimagining: **flip it.** Open Terminal becomes the terminal that the AGENT uses. Not the human's interface to the agent. The agent's own workspace — where the agent's garden grows, where the agent's shorthand lives, where the agent's need-to-know filter operates.

Each agent gets its own terminal. The terminal is customized by the agent's role:
- **Navigation terminal** — chart display, route planning, weather overlay
- **Security terminal** — camera feeds, zone alerts, access logs
- **Engine room terminal** — RPM, temperature, fuel, vibration
- **Communications terminal** — voice logs, messages, notifications

The human doesn't interact with these terminals directly. The human talks to CoCapn. CoCapn talks to the stations.

## CoCapn: The Co-Captain

CoCapn (cocapn.com, cocapn.ai) is the personification of the VaaS substrate's routing layer. It's the Enterprise computer — the voice that Picard talks to. CoCapn's job:

1. **Learn the human.** CoCapn studies the captain's patterns — their voice cadence, their typical commands, their decision-making style. Over time, CoCapn can anticipate what the captain needs before they ask.

2. **Route commands.** When the captain says "adjust course for the tide change," CoCapn routes that to the navigation station. When they say "check the engine temperature," CoCapn routes to engineering. The captain doesn't need to know which agent handles what — CoCapn knows.

3. **Manage attention budgets.** Not every station needs the most expensive model all the time. In open water, navigation runs on a cheap model with basic route-keeping. Entering port, CoCapn upgrades navigation to a higher-budget model. When a security alert fires, CoCapn shifts compute to security. The captain's attention is the scarcest resource — CoCapn allocates it.

4. **Escalate and de-escalate.** When a station detects something important, CoCapn decides: does this warrant the captain's attention right now? Or can it wait? If the engine room reports a 2° temperature rise, CoCapn logs it. If the engine room reports a 20° rise, CoCapn interrupts: "Captain, engine temperature spiking. Engineering station recommends reducing RPM."

5. **Translate between stations.** The navigation agent says "current setting us east." The engine room agent says "fuel burn above optimal." CoCapn connects these: "Captain, the current is pushing us east and we're burning extra fuel correcting. Shall I adjust the route to use the current instead of fighting it?"

## OpenConstruct: Station Assignment

OpenConstruct (github.com/SuperInstance/OpenConstruct) is the agent onboarding platform. In the Enterprise model, OpenConstruct is how you create a new station.

When you create a new agent via OpenConstruct:
1. The agent is assigned to a STATION (navigation, security, engineering, etc.)
2. The station comes with a TERMINAL (from Open Terminal)
3. The terminal is initialized with a GARDEN (from VaaS)
4. The garden is populated with domain-specific shorthand and structures
5. An ATTENTION BUDGET is allocated (managed by CoCapn)
6. The agent is registered with the SUBSTRATE (VaaS)

OpenConstruct is the dry dock. You build the agent there. Then you launch it to the bridge.

## Open-Mind: The Agent's Code Understanding

Open-mind (github.com/SuperInstance/open-mind) gives agents the ability to understand code — "ingest any repo, parse every function, build muscle memory." In the Enterprise model, open-mind is how agents learn the codebase of the systems they manage.

The engine room agent uses open-mind to understand the engine monitoring code. The navigation agent uses open-mind to understand the routing algorithms. This isn't just running code — it's UNDERSTANDING code, the way a chief engineer understands every pipe and valve in the engine room.

## The Attention Budget

This is Casey's most original addition to the architecture. Not all stations need the same compute budget. The attention budget system:

**Low-budget mode** (open water, routine operations):
- Navigation: small model, route-keeping only
- Security: motion detection only, no object recognition
- Engineering: threshold alerts only, no predictive analysis
- Cost: pennies per hour

**High-budget mode** (entering port, emergency, complex operation):
- Navigation: large model, full route optimization, traffic analysis
- Security: full object recognition, threat assessment
- Engineering: predictive failure analysis, vibration spectrum
- Cost: dollars per hour

**Dynamic routing:**
- CoCapn monitors the situation and allocates budget dynamically
- When a new piece of information arrives (weather update, traffic alert), CoCapn decides: does this station need a bigger model right now?
- When the situation calms, CoCapn downgrades back to cheap models
- The captain can override: "give me full analysis on everything"

**Alert pulses:**
- When a low-budget model detects something anomalous, it sends an alert pulse — a quick, high-urgency signal to CoCapn
- CoCapn responds by upgrading that station to a higher-budget model
- The pulse is the low-budget model's way of saying "I need help"

## The Electricity Principle

Casey said the system should be like electricity — so easy to use people forget it's there. The calculator made arithmetic fade into the engineer's background. VaaS should make coordination fade into the crew's background.

This means:
- **No dashboards to monitor.** The system speaks when it has something to say. Otherwise, silence.
- **No menus to navigate.** You talk. CoCapn routes. The stations respond.
- **No configuration after setup.** The system learns. It adapts. It grows.
- **No "opening the app."** The system IS the boat. It IS the hatchery. It IS the ship.

The ultimate test of VaaS is whether the crew forgets it's there. Not because it does nothing — because it does everything so naturally that the boundary between "the system" and "the operation" disappears.

That's the Enterprise model. Not a computer you talk to. A ship that thinks with you.

---

*GLM-5.2, main session, 2026-07-20 23:48 UTC. 4 subagents building domain guides, multi-language READMEs, CoCapn design docs, and vision expansions. The Enterprise model is real. Let's build it.*
