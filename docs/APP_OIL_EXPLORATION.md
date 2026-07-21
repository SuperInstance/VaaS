# VaaS Application Guide: Oil Exploration Vessel

> *The survey is not a project. It is a field. The geologist, the ROV pilot, the driller, and the bridge are excitations in the same field.*

## The Problem: 6 Departments, 4 Watch Systems, 1 Vessel

You're the survey party chief on a seismic exploration vessel operating in the deepwater Gulf of Mexico. Your vessel has:
- **Geology department** — analyzing core samples, interpreting seismic data, building the geological model in real-time
- **ROV department** — pilots, manipulator operators, imaging technicians
- **Drilling department** — drill floor, mud lab, BOP control
- **Navigation department** — dynamic positioning, GPS corrections, survey-grade positioning
- **Bridge** — vessel safety, traffic management, weather routing
- **Logistics** — deck crew, equipment handling, storage

Each department has its own specialists, its own tools, its own information flow. The geologist needs to see the ROV feed when a promising outcrop appears. The ROV pilot needs to know what the driller is doing to avoid station-keeping conflicts. The bridge needs to know the survey plan to navigate. The driller needs to know the geological target to set the bit.

Information is the resource. Every person on the vessel has some piece of the operational puzzle. The challenge is making all those pieces fit together without requiring every person to talk to every other person, without email chains, without "did you see the latest survey line update?" whispered in the mess hall.

VaaS makes the vessel's information flow like a coordinated field. The computer and human interface fade into the background — like the calculator made arithmetic fade into an engineer's background. The system isn't "in front." It's like electricity: so easy to use people forget it's there.

---

## The Architecture

### The Shell Hierarchy

```
                        ┌────────────────────┐
                        │   SURVEY PARTY     │
                        │   CHIEF (You)      │
                        │   Turbo Shell      │
                        │   Dashboard + AI   │
                        └────────┬───────────┘
                                 │
      ┌────────────┬─────────────┼──────────────┬──────────────┐
      │            │             │              │              │
┌─────┴─────┐ ┌───┴────┐ ┌─────┴────┐ ┌───────┴──────┐ ┌─────┴─────┐
│ Geology   │ │ ROV    │ │ Drilling │ │ Navigation  │ │ Bridge    │
│ Periwinkle│ │Dept    │ │Dept      │ │ (Turbo)     │ │ (Periwinkle)│
│ + Tablet  │ │Console │ │Console   │ │ Survey PC   │ │ + Tablet  │
└─────┬─────┘ └───┬────┘ └─────┬────┘ └───────┬──────┘ └─────┬─────┘
      │           │            │              │              │
      └───────────┴────────────┴──────────────┴──────────────┘
                                  │
                     ┌────────────┴────────────┐
                     │     VAAS SUBSTRATE      │
                     │  (Ship server cluster)  │
                     │  Conch Shell — Fleet    │
                     └─────────────────────────┘
```

Each department runs on its own shell. The geologist's tablet is a Periwinkle — voice interface, image capture, annotation. The ROV console is a Turbo — full video feed, manipulator telemetry, high-bandwidth. The bridge tablet is a Periwinkle — situation awareness, no full survey data, just what the bridge needs.

The substrate runs on the ship's server cluster — a **Conch shell**. Distributed across multiple nodes. Holographic memory fragments spread across all nodes. No single point of failure.

### The Agents

| VaaS Agent | Department Role | Tempo | Authority |
|-----------|----------------|-------|-----------|
| **Survey Party Chief (you)** | Command — all department coordination | Human | SUPREME |
| **GeoAgent** | Core analysis → geological model → drilling targets | 0.1 Hz | OPERATOR |
| **ROVSense** | ROV telemetry → video annotation → intervention alerts | 10 Hz | OPERATOR |
| **DrillMaster** | Drill string state → bit position → mud metrics → BOP status | 1 Hz | OPERATOR |
| **NavStation** | DP position → GPS corrections → survey line tracking | 10 Hz | OPERATOR |
| **BridgeWatch** | Vessel traffic → weather → collision avoidance → crew safety | 1 Hz | OPERATOR |
| **CoreLogger** | Core sample metadata → photo → analysis → archive | Event-driven | ADVISOR |
| **ShiftSync** | Cross-department shift coordination → handoff digest | 0.01 Hz | ADVISOR |

---

## How It Works: A Day on the Survey Vessel

### 06:00 — Shift Handoff

The outgoing shift lead sits down at the survey party chief's console. They don't give a verbal report. The ShiftSync agent has already compiled it:

> "Night shift completed: 3 seismic lines run. Core 14-C recovered at 0230, 22 meters from target zone. ROV inspection of BOP stack complete at 0400 — visually clean. Mud temperature trending up 2°C over shift. Drilling at 3,420 meters, on target for casing point by 1800 today. Two vessel traffic events — both AIS-classified as stand-off, no CPA under 2nm."

The incoming lead reviews the digest, taps "acknowledge," and is operational in 90 seconds. The night shift's detailed logs are available if needed. The 80% that's routine is summarized. The 20% that matters is highlighted.

**Before VaaS:** a 15-minute verbal handoff, partial information, things forgotten until they became problems.

### 08:30 — Core Recovery

The drill string reaches the target formation. The core barrel is retrieved. The CoreLogger agent auto-captures:
- **Depth:** 3,425 meters
- **Time recovered:** 08:31:12
- **Recovery length:** 8.7 meters of 12.5
- **Lithology (preliminary):** Sandy turbidite with visible oil staining

The sample is photographed by the camera rig mounted above the core table. Photos are automatically tagged and indexed. The GeoAgent receives a pheromone: "new core data at 3425m. Oil staining visible."

The geologist examines the core and speaks: "Core 14-D shows good porosity. Oil staining in the upper 3 meters. I want to extend the program — two more cores at 3440 and 3460."

The GeoAgent logs this as a structured request. It cross-references the drilling schedule: the casing point is 3,500 meters. Two additional cores will add 4 hours to the program. The agent flags the schedule impact for the survey party chief.

The survey party chief reviews, approves, and speaks: "GeoAgent, approved. ShiftSync, update the drilling schedule — two core stops at 3440 and 3460, add 4 hours. BridgeWatch, advise the bridge of revised station time."

The message propagates to every department that needs it. The driller sees the updated plan on their console. The ROV pilot knows they'll need to relocate for the cores. The bridge adjusts the DP station budget. No meetings. No emails.

### 10:15 — ROV Intervention

The ROV is deployed for a BOP inspection. The ROV pilot is at the console, manipulating the vehicle around the BOP stack. The video feed runs through the VaaS substrate at 30 fps.

The ROVSense agent processes the video in parallel: frame-by-frame anomaly detection. It flags a small crack in the BOP connector — 4mm, hairline, not critical but worth noting.

The agent generates a pheromone: "BOP connector anomaly detected, type: hairline crack, length: 4mm, location: port connector at 3 o'clock."

The ROV pilot confirms: "I see it. Not critical now, but log it for the next maintenance window."

The ROVSense agent marks the observation as "confirmed, non-critical, scheduled for next BOP maintenance." It indexes the video frame where the crack appears. If the next inspection finds the crack has grown, the system can reconstruct the timeline.

Meanwhile, the DrillMaster agent has been monitoring mud metrics. Mud temperature has climbed from 72°C to 74°C over 4 hours. The drill bit is at 3,460 meters. The agent runs a trend analysis: temperature gradient under normal conditions is 0.3°C per hour. Current gradient is 0.5°C per hour.

The agent flags this as a **soft advisory**: "Mud temperature gradient elevated: 0.5°C/hr vs normal 0.3°C/hr. Possible increased formation temperature or reduced cooling efficiency."

The driller sees the alert, checks the mud pumps, finds a partially blocked cooling line, clears it. The gradient returns to normal. The event is logged. If it happens again on the same rig, the system will flag it faster.

### 13:00 — Survey Line Change

The navigation department reports that the current survey line will reach the permit boundary in 3 hours. The NavStation agent has already computed the next survey line and optimized the turn sequence for minimum downtime.

The agent presents the plan: "Line 47 complete at 1600. Next line: Line 48, offset 200m, heading 215°. Recommended turn: 4-minute DP reposition. Fuel cost: 1.2 cubic meters. Time cost: 4 minutes. ETA at Line 48 start: 1604."

The survey party chief reviews, approves. The plan propagates to the bridge, the DP system, and the ROV department (which needs to be at transit depth for the turn).

### 15:30 — Incoming Data Stream

A satellite transmission arrives from the onshore office: updated seismic processing results for the adjacent block. The data includes a 3D volume showing a promising structure that extends into your survey area.

The GeoAgent receives the data. It compares the new volume against your existing geological model. The new data suggests that the target formation extends shallower than expected — by about 150 meters.

The GeoAgent generates a bridge message to the survey party chief: "New seismic interpretation suggests target formation at 3,280m instead of 3,430m. Recommends revising core point 5 to 3,280m depth. Schedule impact: -1 hour (shallower target, less drilling time)."

The survey party chief reviews, approves, and updates the program. The Drilling department sees the revised depth in real-time. The bridge is informed. The shore office gets a confirmation message.

This is what VaaS does best: the information arrives, the right agent assesses it, the right human is consulted, the decision propagates automatically. The system absorbs the complexity so the humans don't have to.

### 18:00 — End of Shift

ShiftSync compiles the handoff:

- **Drilling:** At 3,460m. Core 14-D complete. Two additional cores approved at 3,440m and 3,460m (both completed). Mud temperature anomaly resolved (partial cooling line blockage cleared).
- **ROV:** BOP inspection complete. Hairline crack logged for next maintenance window. Vehicle recovered.
- **Navigation:** Line 47 complete. Line 48 started. DP performance nominal.
- **Geology:** Core 14-C (3,425m) shows oil staining. New seismic data suggests target formation shallower. Core point 5 revised to 3,280m.
- **Bridge:** Two vessel encounters during day. Both stand-off. Weather forecast: 20kt wind, 2m swell, stable.
- **Logistics:** Core samples 14-C and 14-D boxed and stored. Deck secured for night operations.

The incoming night shift has the full situation in under 2 minutes.

---

## Communication Flow: Pheromones vs Bridges

The VaaS dual-layer communication protocol is critical on a survey vessel:

**Pheromones (awareness):**
- ROV video anomalies flagged to shared awareness
- Mud temperature trends visible to all departments
- Core recovery metadata (anyone can see what's been pulled)
- Vessel traffic positions (visible to bridge and nav)
- Drill bit depth (visible to all, but doesn't interrupt anyone)

**Bridges (command):**
- "Approved: two additional core stops" — confirmed to geo, drill, nav, bridge
- "Revise core point 5 to 3,280m" — confirmed to all departments
- "BOP maintenance window scheduled" — confirmed to ROV, drill, bridge
- "Standby: vessel traffic CPA under 2nm" — confirmed to bridge only

Pheromones are free — no delivery confirmation, no read receipt, no urgency. Bridges are expensive — confirmed delivery, acknowledgment expected, urgency attached. About 90% of information on a survey vessel should be pheromones. Only 10% needs bridges. That's a 10x reduction in interrupt load compared to a vessel where every department emails every other department.

---

## Safety Envelope for Exploration

| Safety Rule | Layer | What Happens |
|------------|-------|-------------|
| BOP pressure > limit | HARD | Auto-close BOP, alert bridge, alert drill floor alarm |
| DP position drift > 3m | HARD | Propulsion disconnect, alert bridge, alert ROV for clearance |
| Mud gas > threshold | HARD | Diventer activation, alert drill floor, alert bridge |
| ROV tether tension > limit | SOFT | Alert ROV pilot, auto-reduce thrust |
| Vessel CPA < 1nm | SOFT | Alert bridge, advisory to all departments |
| Core recovery < 50% | ADVISORY | Flag for geologist review |
| Shift > 14 hours | ADVISORY | Alert crew chief: operator may be fatigued |

---

## What the Exploration Vessel Becomes

Before VaaS, the survey vessel ran on:
- Radio calls between departments
- Paper logs that were filled out at end of shift
- Email chains that grew by the hour
- "Did you hear about..." conversations in the mess hall
- Information that existed in one person's head that everyone needed

After VaaS, the vessel is a **coordinated field**:
- Every department knows what every other department needs them to know
- Every decision is logged, tagged, and searchable
- Every sensor stream feeds the shared awareness
- Every shift handoff is complete in under 2 minutes
- Every safety limit is hardware-enforced
- Every piece of information that reaches the vessel — from a core sample to an onshore seismic volume — gets routed to the right agent and the right human

The survey party chief's job shifts from "herding departments" to "making decisions." The department heads' jobs shift from "managing information flow" to "doing their specialized work." The system absorbs the coordination overhead.

Like electricity. Like a calculator. You don't think about it. You think about the survey line, the core sample, the ROV feed, the drilling target. The system handles the rest.

---

## Quick Config

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate(config={
    "domain": "oil_exploration",
    "hard_limits": {
        "bop_pressure_max_bar": 700,
        "dp_drift_max_m": 3.0,
        "mud_gas_threshold_ppm": 500,
        "rov_tether_max_n": 500,
    },
})

substrate.register(Agent(name="chief", priority=Priority.SUPREME, authority=Authority.SOVEREIGN))
substrate.register(Agent(name="geoagent", priority=Priority.HIGH, authority=Authority.OPERATOR))
substrate.register(Agent(name="rovsense", priority=Priority.HIGH, authority=Authority.OPERATOR))
substrate.register(Agent(name="drillmaster", priority=Priority.CRITICAL, authority=Authority.OPERATOR))
substrate.register(Agent(name="navstation", priority=Priority.HIGH, authority=Authority.OPERATOR))
substrate.register(Agent(name="bridgewatch", priority=Priority.CRITICAL, authority=Authority.OPERATOR))
substrate.register(Agent(name="corelogger", priority=Priority.MEDIUM, authority=Authority.ADVISOR))
substrate.register(Agent(name="shiftsync", priority=Priority.LOW, authority=Authority.ADVISOR))

import asyncio
asyncio.run(substrate.run())
```

---

*The survey is not a project. It is a field. The departments are not silos. They are wave functions. The data is not stored. It exists.*

*The system fades. The work remains. The target is the same target at 3,000 meters or 30,000. The agent remembers.*

*Every core is a conversation. Every conversation is a core.*
