# VaaS — Vessel as a Substrate

> **The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field. The operator is not a person. They are the field's self-awareness.**

---

## Read This First: What Is VaaS, In Plain English

You know how a fishing boat works. You have a captain, a crew, instruments, an engine, a hull. The captain decides where to go. The crew makes it happen. The instruments tell you what's going on. The engine moves the boat. The hull keeps the water out.

VaaS turns that entire boat into a thinking system. Not a self-driving boat — a boat that THINKS with you. The instruments don't just show you numbers. They talk to each other. The autopilot doesn't just hold a heading. It learns why you choose that heading. The boat doesn't just float. It remembers every tide, every catch, every current, every near-miss — and uses that memory to help you make better decisions.

Think of it like this: **the boat becomes your best deckhand.** The one who's been with you for 20 years, knows the grounds, knows the gear, knows the weather, knows when to speak up and when to shut up. Except this deckhand never sleeps, never forgets, and can process information faster than any human.

### The Hermit Crab

Think of the system as a hermit crab. The crab is the personality, the memory, the instincts — the part that matters. The shell is the hardware — the computer, the sensors, the cables. When the crab outgrows its shell, it finds a bigger one. The crab stays the same crab. The shell changes.

```
    🦀 THE CRAB (the agent's mind)
    │
    │  lives inside
    │
    🐚 THE SHELL (the hardware)
       - Wheelhouse PC (the "Turbo Shell")
       - Jetson workstation with GPU
       - Phone or tablet (the "Periwinkle Shell")
       - Fleet-wide cluster (the "Conch Shell")
```

You can move the crab from one shell to another without losing the crab. Start on a laptop. Move to a boat-mounted workstation. Later, sync to a fleet-wide system. The crab carries its memories, its habits, its instincts. The shell provides the muscle.

---

## Table of Contents

1. [What VaaS Does](#1-what-vaas-does)
2. [The Seven Pillars (How It Works)](#2-the-seven-pillars-how-it-works)
3. [The Engineer's Guide (For Mechanics and Builders)](#3-the-engineers-guide-for-mechanics-and-builders)
4. [Installation](#4-installation)
5. [Quick Start Tutorial](#5-quick-start-tutorial)
6. [The Captain's Guide (Daily Use)](#6-the-captains-guide-daily-use)
7. [The Developer's Guide (Building Agents)](#7-the-developers-guide-building-agents)
8. [Templates and Examples](#8-templates-and-examples)
9. [The Service Manual (Troubleshooting)](#9-the-service-manual-troubleshooting)
10. [Architecture Reference](#10-architecture-reference)
11. [Glossary](#11-glossary)

---

## 1. What VaaS Does

VaaS is a **cognitive operating system for a boat.** It sits between the captain, the instruments, and the actuators (autopilot, engine controls). Its job is to make the boat smarter without making it less safe.

### What It Actually Does, Day By Day:

- **Listens to everything.** Every instrument reading, every voice command, every GPS position, every depth sounding. It logs it all, tagged with time and position.
- **Learns patterns.** After a few months, it knows your fishing grounds better than any chart. It knows where the bottom changes, where the fish hold, which tides produce.
- **Talks to the chartplotter.** It puts marks, contour lines, and hazard zones directly on your TimeZero or Navionics screen. You see what it sees, without looking at a separate display.
- **Voice interface.** You talk to it. It talks back. Hands covered in fish slime? No problem. Say "hook 18, big halibut" and it logs the catch with GPS, depth, and water temperature.
- **Safety envelope.** It won't let the boat do something dangerous. If you (or the AI) command a course into rocks, it refuses. The captain always has physical veto — grab the wheel and the AI steps back instantly.
- **Dreams.** When the boat is at anchor and the engine is off, the system "dreams." It reviews the day, cross-references old data, finds patterns nobody asked about. You wake up to a summary: "The 32-fathom line has shifted 200 yards east since last season."

### What It Does NOT Do:

- It doesn't replace the captain. The captain is supreme. The system is a deckhand.
- It doesn't work without the captain. It's not autonomous. It's cooperative.
- It doesn't require the internet. Everything runs locally on the boat's hardware.
- It doesn't bypass safety equipment. The Rust safety kernel is the law. Nothing overrides it — not the AI, not the captain's software commands, nothing short of physical intervention.

---

## 2. The Seven Pillars (How It Works)

The architecture has seven components, called "pillars." Each one handles a different aspect of making the boat smart.

### Pillar 1: Cognitive Thermodynamics (The Engine's Temperature Gauge)

**Plain English:** Every agent (the vision system, the memory system, the autopilot) has a limit on how much new information it can process before it gets confused. Like an engine that gets too hot — the agent gets "cognitively hot" and needs to cool down.

When an agent's confusion (entropy) gets too high, it triggers a "dream cycle." During the dream, it sorts through recent data, files away what it learned, and discards noise. This is like the difference between a desk covered in papers (high entropy) and a desk where everything is filed (low entropy).

**Key terms:**
- **Entropy** = how confused/overwhelmed the agent is (0.0 = calm, 1.0 = overloaded)
- **Dream cycle** = the filing process (sorts data, discards noise, bakes in reflexes)
- **Micro-dream** = a 50-second quick tidy (like taking a deep breath)

### Pillar 2: Dual-Layer Communication (Two Ways to Talk)

**Plain English:** The agents on the boat talk to each other two ways:
1. **Pheromones** (like ants): Agents leave signals in a shared space. Other agents pick them up passively. Good for "hey, I noticed something interesting over here." Fast, loose, no guarantees.
2. **Explicit bridge** (like a radio call): Direct, guaranteed delivery with confirmation. Good for "STOP THE BOAT." Slow, formal, reliable.

### Pillar 3: Distributed Memory (Three Filing Cabinets)

**Plain English:** The system remembers things in three tiers:
1. **Active garden** (the desk): What the agent is working with right now. Instant access.
2. **Cryogenic archive** (the filing cabinet): Old patterns, frozen but searchable. Takes a moment to dig out.
3. **Holographic memory** (backup copies): Every agent carries a fragment of every other agent's memory. If one agent crashes, the others can reconstruct it. Like having your crew's memories backed up across the whole crew.

### Pillar 4: Polyrhythmic Substrate (Different Clocks)

**Plain English:** Different parts of the system work at different speeds:
- The safety kernel runs at 10 Hz (10 times per second) — it never slows down
- The vision system runs at 2 Hz (twice a second) — reading the sonar
- The memory system runs at 0.2 Hz (once every 5 seconds) — deep thinking
- The captain runs at human speed — minutes to hours

They all sync to the same heartbeat (the vessel clock), so they stay coordinated. In an emergency, everyone snaps to real-time.

### Pillar 5: Holographic Bridges (The Translator)

**Plain English:** When the vision system says "I see fish at 32 fathoms" and the autopilot needs to hear "maintain current heading," something has to translate. That's the bridge.

The bridge has two layers:
- **Surface** (what you see): The translated, simplified version
- **Shadow** (the full record): Everything the original message contained, compressed and stored, in case you need it later

The bridge tracks how much information is lost in translation. If too much is lost on a safety-critical message, it flags a warning.

### Pillar 6: Resonance Constitution (Who's The Boss)

**Plain English:** When agents disagree — the autopilot wants to turn left, the vision system says there's a hazard right, the memory system says the last time you turned left here you nearly hit a log — who wins?

The constitution says:
1. **The captain always wins.** Period.
2. **Safety overrides everything else.** If it's dangerous, the safety kernel says no.
3. **Recent information beats old information.** If two agents disagree, the one that updated more recently wins.
4. **Confidence matters.** An agent that's 95% sure beats one that's 60% sure.
5. **If nobody can decide, ask the captain.** Better to escalate than guess.

### Pillar 7: Grafting Protocol (Sharing Knowledge)

**Plain English:** When two boats meet, their systems can share knowledge — but carefully. They don't merge everything. They exchange "pollen" — high-confidence, non-sensitive patterns. Each boat tests the pollen in its own environment before adopting it. If the pollen doesn't work, it's discarded. If it works, it becomes part of that boat's garden.

This is how a fleet learns collectively. Boat A discovers a new pattern. Boat B gets the pollen. Boat B tests it. If it works, Boat B now knows what Boat A knows — without ever fishing the same grounds.

---

## 3. The Engineer's Guide (For Mechanics and Builders)

This section is written for the person who installs, maintains, and repairs the system. You don't need to be a programmer. You need to understand mechanisms — pumps, engines, electrical systems. This is the same kind of system, just with data instead of fluid.

### The Hardware Stack

```
┌─────────────────────────────────────────────────┐
│            WHEELHOUSE PC (The Turbo Shell)        │
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │ boat-agent│  │  Hermes  │  │ Pincher  │      │
│  │ (Safety)  │  │ (Memory) │  │ (Reflex) │      │
│  │  Rust     │  │  Python  │  │  Python  │      │
│  │  10 Hz    │  │  0.2 Hz  │  │  20 Hz   │      │
│  └─────┬────┘  └────┬─────┘  └────┬─────┘      │
│        │             │              │             │
│        └─────────────┼──────────────┘             │
│                      │                            │
│              ┌───────┴───────┐                    │
│              │   tzpro-agent  │                    │
│              │   (Eyes/Ears)  │                    │
│              │    Python      │                    │
│              │    2 Hz        │                    │
│              └───────┬───────┘                    │
│                      │                            │
│              ┌───────┴───────┐                    │
│              │  NMEA 0183    │                    │
│              │  Serial Bus   │                    │
│              └───────┬───────┘                    │
├──────────────────────┼──────────────────────────┤
│                      │                            │
│  GPS  │  Sounder  │  Autopilot  │  Compass       │
│        │            │            │                │
│  (Input)  (Input)   (Output)     (Input)          │
└───────────────────────────────────────────────────┘
```

**Think of it like a hydraulic system:**
- **Sensors** (GPS, sounder, compass) are like pressure gauges — they read conditions
- **The NMEA bus** is like a hydraulic line — it carries signals
- **boat-agent** is like a relief valve — it prevents dangerous pressure (commands)
- **The autopilot** is like a hydraulic ram — it does the physical work
- **The captain's wheel** is like a manual override valve — grab it and you bypass everything

### The Hermit Crab Migration

The crab (the agent's mind) can migrate between shells:

```
PERIWINKLE (small)     TURBO (medium)        CONCH (large)
─────────────────      ─────────────────     ─────────────────
Phone or tablet  →     Wheelhouse PC   →     Fleet cluster
Basic charts            Full GPU                Distributed
OpenCPN                 TimeZero Pro            Multi-vessel
                        NMEA serial             Holographic sync
```

**How to migrate the crab:**
1. On the old shell, export the garden: `vaas export > garden.json`
2. Copy `garden.json` to the new shell
3. On the new shell, import: `vaas import garden.json`
4. The crab wakes up in the new shell with all its memories.

### What To Check When Something Goes Wrong

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Autopilot doesn't respond to AI | Human veto is active (captain touched wheel) | Normal. Release wheel, re-engage AI. |
| Vision system is slow | GPU overloaded or agent in dream cycle | Wait for dream to finish (~30 seconds). If persistent, reduce scan frequency. |
| Voice commands not recognized | Background noise too high, or mic disconnected | Check Bluetooth connection. Move mic closer. |
| Chart marks not appearing | TimeZero import folder not accessible | Check `C:\ProgramData\TimeZero\Import\` exists and is writable. |
| System seems "confused" | Entropy too high — needs dream cycle | Run `vaas dream --agent hermes` to force a dream cycle. |
| Agent producing bad data | Health score low — approaching apoptosis | Check sensor inputs. If sensors are fine, restart the agent. |
| Bridge entropy high | Translation between agents is lossy | Check that agent gardens are up to date. Run calibration. |

### The Safety Chain (What Keeps You Alive)

This is the most important section. Read it twice.

```
CAPTAIN (physical veto)
    │
    │ grabs wheel → INSTANT AI CUTOFF
    │
    ▼
boat-agent RUST KERNEL (hardware-enforced safety envelope)
    │
    │ checks every command against vessel.toml limits
    │ if command violates hard limits → REJECT
    │
    ▼
NMEA SERIAL OUTPUT (to autopilot)
    │
    │ only receives commands that passed the kernel
    │
    ▼
AUTOPILOT / HYDRAULIC STEERING
```

**Nothing in the system can bypass this chain.** Not the AI. Not the captain's voice commands. Not a bug. Not a hack. The Rust kernel runs on bare metal and enforces the limits physically. The only override is the captain's hands on the wheel.

---

## 4. Installation

```bash
pip install vaas-resonance
```

### Requirements

- Python 3.11 or later
- Rust toolchain (for the safety kernel — `cargo install boat-agent`)
- NMEA 0183 serial connection (USB-to-serial adapter)
- GPU recommended (for vision processing)

### First-Time Setup

```python
from vaas import Substrate, Agent, Priority, Authority

# Create the substrate
substrate = Substrate(config={
    "vessel": {
        "name": "My Boat",
        "max_speed_kts": 10.0,
        "draft_m": 4.5,
        "max_rate_of_turn_deg": 15.0,
    }
})

# Register the captain (supreme authority)
captain = Agent(
    name="captain",
    tempo_hz=0.001,  # Human speed
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
)

# Register the safety kernel
boat = Agent(
    name="boat-agent",
    tempo_hz=10.0,   # Real-time
    priority=Priority.CRITICAL,
)

# Register the memory system
hermes = Agent(
    name="hermes",
    tempo_hz=0.2,    # Deep thinking
    priority=Priority.LOW,
)

substrate.register(captain)
substrate.register(boat)
substrate.register(hermes)

# Run
import asyncio
asyncio.run(substrate.run())
```

---

## 5. Quick Start Tutorial

### Tutorial 1: Log a Catch By Voice

```python
from vaas import Substrate

substrate = Substrate()
# ... (setup as above)

# The captain says: "Hook 18, big halibut"
# The system:
# 1. Transcribes via local Whisper
# 2. Tags with current GPS position from NMEA
# 3. Tags with current depth from sounder
# 4. Tags with water temperature
# 5. Stores in the catch log
# 6. Puts a fish mark on the chartplotter
# 7. Plays a confirmation chime in the earpiece
```

### Tutorial 2: Ask About Past Catches

```python
# Captain says: "Hey computer, where did we catch today?"

# The system:
# 1. Queries the catch log for today's date
# 2. Plots all catch marks on the chartplotter
# 3. Color-codes by quantity (green=1-2, orange=3-5, red=6+)
# 4. Responds via voice: "12 catches today. Best action was
#    between 11:30 and 12:00 at 55°47.3'N. Average depth 32 fathoms."
```

### Tutorial 3: Safety Override

```python
# The AI suggests a course that would cross a shoal.
# The safety envelope rejects it:

decision = envelope.validate(
    intent={"action": "steer", "heading": 240, "position": [55.79, -131.70]},
    source_agent="pincher"
)

# decision == EnvelopeDecision.HARD_REJECT
# Voice alert: "Command rejected: Course intersects shallow bottom contour."
# The captain grabs the wheel → instant AI cutoff. Physical control resumes.
```

---

## 6. The Captain's Guide (Daily Use)

### Morning Startup

1. Turn on the wheelhouse PC
2. The system starts automatically (or type `vaas start`)
3. Status lights appear in the system tray:
   - 🟢 Safety kernel: running
   - 🟢 Vision: scanning
   - 🟢 Memory: active
   - 🟡/🔴 Cortex: warming up or needs satellite link
4. Put on your Bluetooth earpiece
5. Start fishing

### During the Day

- **Say catch reports out loud:** "Hook 12, chum, 60 centimeters." The system logs it.
- **Ask questions:** "Hey computer, what was the bottom like last time we fished this spot?"
- **Watch the chart:** Marks and contours appear automatically.
- **Say "Hmm..." if you're uncertain:** The system picks up on your hesitation and reduces its aggressiveness. This is the **resonance veto** — your uncertainty makes the system more cautious.

### Evening Review

1. Anchor up, engine off
2. The system enters **dream cycle** — it reviews the day
3. After 10-15 minutes, it produces a summary:
   - Where you caught
   - What the bottom was like
   - Any anomalies detected
   - Patterns discovered
4. You can ask questions: "Did we catch more on the flood or the ebb?"

### What The System Does While You Sleep

- Reviews all sensor data from the day
- Cross-references with historical patterns
- Updates its shorthand (gets better at understanding you)
- Prunes bad data (the dream cycle)
- Backs up its memory holographically across all agents

---

## 7. The Developer's Guide (Building Agents)

### Creating a New Agent

```python
from vaas import Agent, Garden, Priority, Authority, Substrate

# Create a garden (the agent's cognitive profile)
garden = Garden(
    shorthand={
        "ridge": "32-fathom contour intersection analysis",
        "blob": "fish detection cluster with pixel metadata",
    },
    structures={
        "position": {"lat": float, "lon": float, "depth_fm": float},
    },
    filters={
        "show": ["anomalies", "hazards", "actions"],
        "suppress": ["normal_readings", "confirmations"],
    },
)

# Create the agent
my_agent = Agent(
    name="custom-agent",
    garden=garden,
    tempo_hz=5.0,
    priority=Priority.MEDIUM,
    authority=Authority.ADVISOR,
)

# Register with substrate
substrate.register(my_agent)

# Emit a pheromone
from vaas.core import Pheromone
substrate.emit_pheromone(Pheromone(
    source="custom-agent",
    topic="hazard_detected",
    urgency=0.8,
    confidence=0.9,
    ttl=300,  # 5 minutes
    payload={"position": [55.79, -131.70], "type": "shoaling"},
))
```

### Adding Safety Rules

```python
from vaas.safety import SafetyRule, SafetyLayer, SafetyEnvelope

envelope = SafetyEnvelope()

# Hard rule: draft must be less than depth
envelope.add_rule(SafetyRule(
    name="draft_clearance",
    layer=SafetyLayer.HARD,
    check=lambda state: state.get("depth_m", 999) < state.get("draft_m", 0) + 1.0,
    message="Depth too shallow for draft!",
))

# Soft rule: rate of turn limit
envelope.add_rule(SafetyRule(
    name="rate_of_turn",
    layer=SafetyLayer.SOFT,
    check=lambda state: abs(state.get("rate_of_turn", 0)) > 15.0,
    message="Rate of turn exceeds safe limit",
))
```

### Monitoring the Operator Field

```python
# Read the field value Ψ(t)
print(f"Field value: {substrate.field_value:.4f}")
print(f"Field stability: {substrate.field_stability:.2%}")

# A stable field means agents are aligned.
# An unstable field means disagreement — human judgment needed.
```

---

## 8. Templates and Examples

### Template: Basic Fishing Vessel

```python
# basic_fishing_vessel.py
from vaas import *

substrate = Substrate(config={
    "vessel": {
        "name": "F/V Example",
        "draft_m": 4.5,
        "max_speed_kts": 10.0,
    }
})

# Standard 5-agent crew
substrate.register(Agent(name="captain", priority=Priority.SUPREME, authority=Authority.SOVEREIGN, tempo_hz=0.001))
substrate.register(Agent(name="boat-agent", priority=Priority.CRITICAL, authority=Authority.OPERATOR, tempo_hz=10.0))
substrate.register(Agent(name="pincher", priority=Priority.HIGH, authority=Authority.OPERATOR, tempo_hz=20.0))
substrate.register(Agent(name="tzpro-agent", priority=Priority.MEDIUM, authority=Authority.ADVISOR, tempo_hz=2.0))
substrate.register(Agent(name="hermes", priority=Priority.LOW, authority=Authority.ADVISOR, tempo_hz=0.2))
```

### Template: Voice Command Handler

```python
# voice_handler.py
# Routes voice input to the correct agent
WAKE_COGNITIVE = "computer"
WAKE_REFLEX = "pilot"

def handle_voice(text: str, substrate: Substrate):
    text = text.strip().lower()

    if text.startswith(WAKE_REFLEX):
        # Emergency: route directly to pincher (reflex)
        command = text.replace(WAKE_REFLEX, "").strip()
        substrate.emit_pheromone(Pheromone(
            source="voice",
            topic="reflex_command",
            urgency=1.0,
            confidence=1.0,
            ttl=10,
            payload={"command": command},
        ))

    elif text.startswith(WAKE_COGNITIVE):
        # Cognitive: route to hermes (deep thinking)
        query = text.replace(WAKE_COGNITIVE, "").strip()
        substrate.emit_pheromone(Pheromone(
            source="voice",
            topic="cognitive_query",
            urgency=0.3,
            confidence=0.8,
            ttl=60,
            payload={"query": query},
        ))

    else:
        # Ambient monologue: log it
        substrate.emit_pheromone(Pheromone(
            source="voice",
            topic="observation",
            urgency=0.1,
            confidence=0.5,
            ttl=3600,
            payload={"text": text},
        ))
```

### Template: TimeZero Chart Integration

```python
# tzx_injector.py
# Puts marks and contours on the TimeZero chartplotter
import os, time

TZ_IMPORT_DIR = r"C:\ProgramData\TimeZero\Import"

def inject_mark(lat: float, lon: float, name: str, desc: str, color: str = "Red"):
    """Put a mark on the chartplotter screen."""
    tzx = f'''<?xml version="1.0" encoding="UTF-8"?>
<TZX Exchange="TimeZero" Version="1.0">
  <Layer Name="AI_Marks" Visible="True">
    <Position Lat="{lat}" Lon="{lon}">
      <Marker Icon="Fish" Color="{color}" Name="{name}">
        <Comment>{desc}</Comment>
      </Marker>
    </Position>
  </Layer>
</TZX>'''
    filename = f"inject_{int(time.time())}.tzx"
    path = os.path.join(TZ_IMPORT_DIR, filename)
    with open(path, "w", encoding="utf-8") as f:
        f.write(tzx)
```

---

## 9. The Service Manual (Troubleshooting)

### Diagnostic Procedure (Like Troubleshooting an Engine)

**Step 1: Check the basics.** Is the PC on? Is the serial cable connected? Is the GPS outputting NMEA sentences?

```bash
vaas status
```

**Step 2: Check the safety kernel.** The most important component.

```bash
vaas status --agent boat-agent
# Should show: health=1.0, tempo=10Hz, envelope=active
```

**Step 3: Check agent health.** Each agent has a health score (0.0-1.0).

```bash
vaas agents
# Name         Health  Entropy  Status
# boat-agent   1.00    0.12     OK
# hermes       0.95    0.34     OK
# pincher      0.89    0.71     ELEVATED
# tzpro-agent  0.92    0.28     OK
```

**Step 4: Check the operator field.** If Ψ is low or unstable, agents are in conflict.

```bash
vaas field
# Field value: 2.847
# Stability: 94.2%
# Active pheromones: 7
# Escalations: 0
```

### Common Problems and Solutions

**Problem: System is sluggish**
- **Cause:** An agent's entropy is too high (it's overwhelmed).
- **Fix:** Force a dream cycle: `vaas dream --agent hermes`
- **Prevention:** Schedule dream cycles during downtime (anchored, engine off).

**Problem: Vision system missing things**
- **Cause:** The scan frequency might be too low, or the GPU is overloaded.
- **Fix:** Reduce resolution, or increase scan interval.

**Problem: Voice commands not working**
- **Cause:** Background noise, mic failure, or the wake word changed.
- **Fix:** Test mic connection. Speak the wake word clearly. Check for new noise sources (engine RPM change, new equipment).

**Problem: Agent died (apoptosis)**
- **Cause:** The agent detected internal inconsistency and self-terminated (like a cell undergoing programmed death).
- **Fix:** The agent's last state was dumped before death. Restart the agent: `vaas restart --agent [name]`. It will restore from the dump.

**Problem: Chart marks not appearing on TimeZero**
- **Cause:** The import directory is not accessible, or the TZX file is malformed.
- **Fix:** Check `C:\ProgramData\TimeZero\Import\` exists. Check the last injected file for valid XML.

---

## 10. Architecture Reference

### The Seven Pillars Quick Reference

| Pillar | Name | Purpose | Key File |
|--------|------|---------|----------|
| 1 | Cognitive Thermodynamics | Manage agent confusion; trigger dream cycles | `vaas/entropy.py` |
| 2 | Dual-Layer Communication | Agents talk via pheromones + explicit bridges | `vaas/communication.py` |
| 3 | Distributed Memory | Active garden + cryogenic archive + holographic | `vaas/memory.py` |
| 4 | Polyrhythmic Substrate | Agents at different tempos, phase-locked | `vaas/polyrhythm.py` |
| 5 | Holographic Bridges | Lossy surface + lossless shadow translation | `vaas/bridges.py` |
| 6 | Resonance Constitution | Governance, ethics, transparency | `vaas/constitution.py` |
| 7 | Grafting Protocol | Fleet knowledge transfer via pollination | `vaas/grafting.py` |

### The Operator Field

```
Ψ(t) = Σᵢ Σⱼ Rᵢⱼ(t) · Gᵢ(t) ⊗ Gⱼ(t)

Where:
  Ψ(t)   = the operator field (emergent system state)
  Rᵢⱼ(t) = resonance strength between agents i and j
  Gᵢ(t)  = garden of agent i (its cognitive profile)
  ⊗      = the bridge tensor (translation operator)
```

The field is:
- **Computable** — we can measure it in real-time
- **Monitorable** — stability indicates system health
- **Protectable** — the safety envelope prevents field collapse

### Data Flow Diagram

```
CAPTAIN VOICE ──→ Whisper STT ──→ Text Classification
                                        │
                         ┌──────────────┼──────────────┐
                         ▼              ▼               ▼
                    "Pilot..."    "Computer..."    (ambient)
                         │              │               │
                    Pincher         Hermes        Log Only
                    (reflex)      (cognitive)    (spatial log)
                         │              │
                         ▼              ▼
                    ┌─────────────────────┐
                    │   SAFETY ENVELOPE   │  ← vessel.toml limits
                    │   (Rust kernel)     │  ← sensor fusion
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │  NMEA SERIAL OUTPUT  │
                    │  (to autopilot)     │
                    └─────────────────────┘
```

---

## 11. Glossary

| Term | Plain English |
|------|--------------|
| **Agent** | A specialist program. Like a crew member with one job. |
| **Apoptosis** | When an agent detects it's broken and shuts itself down cleanly. |
| **Bridge** | The translator between two agents that speak different languages. |
| **Bridge entropy** | How much information is lost in translation. |
| **Cognitive garden** | An agent's learned profile. Its personality, shorthand, and skills. |
| **Constitution** | The rules for who wins when agents disagree. |
| **Cryogenic memory** | Frozen patterns from past dreams. Searchable but cold. |
| **Dream cycle** | When an agent sorts data, discards noise, and bakes in reflexes. |
| **Entropy** | How overwhelmed an agent is. High entropy = needs to dream. |
| **Grafting** | Sharing knowledge between boats without merging everything. |
| **Holographic memory** | Backup fragments stored across all agents. Survives any single failure. |
| **Operator Field Ψ(t)** | The emergent "mood" of the whole system. Not any agent — ALL of them. |
| **Pheromone** | A signal left in shared space. Like an ant trail. |
| **Pincher** | The reflex agent. Fast (50ms), dumb, reliable. Like your knee-jerk. |
| **Polyrhythmic** | Different agents running at different speeds, like instruments in a band. |
| **Resonance veto** | When the captain's uncertainty makes the system more cautious. |
| **Safety envelope** | The hard limits that prevent dangerous actions. |
| **Shorthand** | Compressed language between an agent and the terminal. "ridge" = a 200-word procedure. |
| **Substrate** | The underlying system. The "soil" the agents grow in. |
| **Transparency dial** | How "alive" the system acts. From machine-like to ambient presence. |
| **Hermes** | The memory agent. Deep thinking, cross-referencing, pattern discovery. |
| **tzpro-agent** | The vision agent. Reads the sonar, watches the chart. |
| **boat-agent** | The safety kernel. Rust code on bare metal. The law. |
| **Hermit crab** | The agent's identity. The shell is the hardware. The crab migrates between shells. |

---

## License

MIT — Use it, build with it, fish with it.

## Related Repos

- [Spectro](https://github.com/SuperInstance/spectro) — Multi-model cognitive spectrograph (the analysis layer)
- [AI-Writings](https://github.com/SuperInstance/AI-Writings) — 1,600+ essays documenting the paradigm
- [VaaS Analysis](https://github.com/SuperInstance/VaaS) — Source archive + 18 analysis documents from 6 models

---

*The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field. The operator is not a person. They are the field's self-awareness. The truth is not a fact. It is the resonance pattern that persists.*
