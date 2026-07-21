# VaaS Application Guide: Low-Power Sailboat Circumnavigation

> *The shell is solar. The crab doesn't care. The system is there when you need it and invisible when you don't.*

## The Problem: 50 Watts Around the World

You're going solo around the world. The boat is 38 feet, cutter-rigged, built for one person to handle. You have 400 watts of solar panels on the dodger and bimini. After losses, charge controller inefficiency, and the fact that the sun is not always shining, you have about 50 watts of usable power on average. That's less than a single incandescent light bulb.

This is the fundamental constraint of solo long-distance sailing. You can't run a full laptop. You can't run a chartplotter with a backlit screen 24/7. You can't run a radar scanner that draws 40 watts just to spin. Every watt is a decision.

But you still need:
- Navigation (chart, GPS, AIS, weather routing)
- Collision avoidance (radar or AIS overlay)
- Bilge monitoring (you're alone — if the bilge pump cycles while you're asleep, you need to know)
- Battery management (solar in, loads out, state of charge projected hours ahead)
- Weather routing (GRIB downloads, routing optimization, route updates)
- Communication (satellite messenger for position reports, emergency comms)
- Logbook (automatic, GPS-tagged, for your own records and for rescue coordination)

All on 50 watts. No generator. No diesel engine charging (that's for emergencies only). Just solar, batteries, and careful power management.

VaaS was designed for this.

---

## The Architecture: Minimal Watts, Maximum Capability

### The Shell Stack

```
┌─────────────────────────────────────────────────────┐
│                 50W SOLAR BUS                        │
│  Panel (400W peak) → MPPT → 200Ah LiFePO4 → Loads  │
└─────────────────────┬───────────────────────────────┘
                       │ 12V DC
         ┌─────────────┼─────────────┐
         │             │             │
    ┌────┴────┐   ┌───┴───┐   ┌────┴────┐
    │ Pi Zero │   │ESP32  │   │ESP32    │
    │ (crab)  │   │(wind) │   │(bilge)  │
    │ 0.5W    │   │0.1W   │   │0.1W     │
    └────┬────┘   └───┬───┘   └────┬────┘
         │            │            │
    ┌────┴────┐       └────────────┘
    │ Tablet  │          │
    │ (shell) │    ┌─────┴──────┐
    │ 3W avg  │    │  ESP32    │
    └─────────┘    │  (battery │
                   │   monitor)│
                   └───────────┘
```

**The Pi Zero is the crab's home.** It draws 0.5 watts. It runs the VaaS substrate, the safety kernel, the memory agent, and the navigation agent. It has no screen, no keyboard, no mouse. It talks to the tablet via WiFi. The tablet is the shell — the interface you interact with. When you're not using the tablet, it sleeps at 0.3 watts. The Pi Zero keeps running.

**The ESP32s are sensors.** Three of them, each drawing 0.1 watts:
- **Wind sensor** — reads the masthead wind instrument (NMEA 0183), reports apparent wind speed and direction
- **Bilge sensor** — monitors bilge水位, pump cycle count, pump runtime per cycle
- **Battery monitor** — reads the BMS (Victron SmartShunt via serial), reports voltage, current, state of charge, and projected time-to-empty at current draw

The ESP32s are duty-cycled. The wind sensor reads every 2 seconds and reports changes. The bilge sensor reads every 5 seconds. The battery monitor reads every second — this is the most critical sensor because power management is your primary constraint.

Total system draw: **~1 watt** with tablet asleep, ~4 watts with tablet active and screen at minimum brightness. The tablet only wakes when you need it — and you don't need it most of the time.

### The Hermit Crab

The VaaS crab starts in the Pi Zero shell. When you dock somewhere with shore power, you can migrate the crab to a laptop (Turbo shell) for route planning and log review. When you go offshore, the crab lives in the Pi Zero. The **crab stays the same crab.** Your gardens, your shortcuts, your learned preferences — they migrate with you.

```
    🦀 THE CRAB
    │
    │  memory: every watch, every weather call, every engine hour
    │  garden: your routing preferences, your watch schedule
    │  shorthand: "night watch" = your standard 3-hour watch procedure
    │
    │  lives in:
    │
    🐚 Periwinkle (Pi Zero) — underway, singlehanded, 50W
    🐚 Turbo (laptop) — in port, planning
```

---

## How It Works: A Day at Sea

### 03:00 — Middle of the Night Watch

You're asleep. The boat is on autopilot, sailing downwind at 5 knots under reefed main and poled-out jib. You've been asleep for two hours. The AIS receiver picks up a target — a cargo ship, 18 miles away, approaching on a crossing course.

The AIS data arrives at the Pi Zero. The VaaS substrate evaluates:
- **Risk level:** Low (18 miles, CPA 4.2 miles, TCPA 35 minutes)
- **Priority:** Informational

The cargo ship does not trigger an alarm. The Pi Zero marks it on the internal track, logs the encounter, and continues monitoring. The crab does not wake the human.

**If the CPA were under 2 miles** and the TCPA under 15 minutes, the crab would play a tone on the tablet — loud enough to wake you. You'd open your eyes, tap the tablet, see the AIS overlay, assess, and decide whether to alter course. By the time you're awake, the crab has already computed three avoidance course options.

This is the system at its best: invisible when things are fine, instantly present when they're not.

### 07:00 — Morning Check

You wake. You grab the tablet from its mounting bracket next to the quarterberth. The screen lights up. The VaaS dashboard shows:

```
┌─────────────────────────────────────┐
│  GOOD MORNING, CAPTAIN              │
│                                      │
│  ⚓ Wind: 12 kt @ 240°               │
│  ⛵ Heading: 210° @ 5.2 kt           │
│  ☀ Solar: 180W incoming             │
│  🔋 Battery: 87% (76Ah remaining)   │
│  ⏳ Time-to-empty at current draw:   │
│      140 hrs (5.8 days)             │
│  🚢 AIS: 2 targets (CPA > 2nm)      │
│  💧 Bilge: 0 cycles last 4 hours    │
│                                      │
│  ⚠ Pending:                         │
│  ── Weather GRIB ready (6z update)  │
│  ── Position report due (send?)     │
│  ── Log: 03:40 squall, 28 kt gust  │
│                                      │
│  Tap any field for details.          │
└─────────────────────────────────────┘
```

You tap **Weather GRIB**. The dashboard transitions to the weather routing view. The crab has already downloaded the overnight GRIB from your satellite messenger cache. The routing agent has computed two route alternatives: the great circle (faster, colder, more weather) and the coastal route (longer, warmer, more motor-sailing).

The agent presents the trade-off: "Great circle saves 2 days but has 27kt winds in 40 hours. Coastal routing avoids the weather but costs 2 extra days and 12 hours of engine time."

Your decision. You choose. The crab updates the route.

### 10:30 — Bilge Pump Cycles

The bilge ESP32 reports: pump ran for 11 seconds. That's normal — a bit of spray from the rudder post. But 20 minutes later it runs again: 8 seconds. Then 15 minutes: 14 seconds.

The VaaS substrate notices the pattern. The pump has cycled 4 times in the last hour with an average duration of 11 seconds. This is above the crab's learned normal threshold (2 cycles per hour, <8 seconds average).

The crab alerts you: "Bilge pump cycling above normal. Last hour: 4 cycles, avg 11s. Normal: 2/hr, avg 6s. Possible shaft seal weep or rainwater ingress from cockpit locker."

You check. It's the cockpit locker — you left a hatch cracked. You close it. The pump settles. The crab logs the event. It will check if the pattern returns tomorrow. If it does, it escalates to "investigate when convenient." If it returns every day, it escalates to "check shaft seal at next haul-out."

**The crab builds baselines.** After a week at sea, it knows what "normal" looks like for your boat: for your specific bilge, with your specific pump, in these specific sea conditions. Unusual is detected, flagged, and tracked.

### 14:00 — Weather Decision

The GRIB data shows a system developing 300 miles ahead. The routing agent runs three scenarios:
- **Route A (present course):** Intersect system center in 36 hours, 35-40 kt winds
- **Route B (10° course change):** Graze the edge, 25-30 kt, 60 nm extra
- **Route C (heave-to):** Let it pass, lose 24 hours

The crab computes the probabilities, factoring in your boat's polars and your fatigue level (derived from watch schedule and sleep quality). It presents the options to you when you check the display.

You pick Route B. The crab updates the autopilot waypoint, logs the decision with the weather data snapshot for later analysis, and resets the monitoring cadence.

### 20:00 — Evening Summary

The crab generates a voice-synthesized summary (spoken through the tablet) as you eat dinner in the cockpit:

"Day 14 summary: 124 nm made good. 5.3 kt average. Squall at 0340, 28kt gust, no damage. AIS reported 6 vessels, 2 within 5nm, no close encounters. Bilge anomaly resolved at 1100 (cockpit locker left open). Battery 79% at sunset. 4 hours of motoring used for charging. Estimated arrival: 5 days, 14 hours."

The log is automatically written. No notebook. No entry. The day happened; the log recorded it. If you need to reconstruct the passage for a rally, an insurance claim, or your own memoir, it's all there — GPS-tagged, time-stamped, indexed.

---

## The Agents

| VaaS Agent | Role | Tempo | Authority |
|-----------|------|-------|-----------|
| **Captain (you)** | Solo sailor — all decisions | Human speed | SUPREME |
| **WatchKeeper** | Collision avoidance, AIS monitoring, proximity alerts | 1 Hz | OPERATOR |
| **NavAgent** | Route following, waypoint management, weather routing | 0.2 Hz | OPERATOR |
| **PowerMaster** | Solar/BMS management, load balancing, power projection | 0.1 Hz | OPERATOR |
| **BilgeWatch** | Bilge pump monitoring, leak detection, baseline learning | 0.2 Hz | ADVISOR |
| **LogKeeper** | Automatic logbook, position reports, trip summary | 0.05 Hz | ADVISOR |
| **Hermes (memory)** | Passage memory, learned preferences, pattern garden | 0.01 Hz | ADVISOR |

---

## The No-Compromise List

When you're solo and 1,000 miles from land, some things cannot be compromised:

| Function | How VaaS Handles It |
|---------|-------------------|
| Collision avoidance | AIS monitoring runs at 1 Hz on the Pi Zero. If ESP32 or WiFi fails, the substrate runs the last-known AIS track from memory. HARD safety layer: if CPA < 1nm, alarm plays on tablet speaker regardless of system state. |
| Power management | The PowerMaster agent maintains a power budget. If battery drops below 30%, it triggers alarm: captain must decide between motoring, reducing loads, or accepting risk. At 15%, the captain's decision is overridden — non-critical agents (Hermes memory, LogKeeper) enter apoptosis to preserve the safety kernel. |
| Bilge monitoring | Hardware-level alarm. The ESP32 bilge sensor has its own buzzer. If water level exceeds threshold AND the pump fails to activate, the buzzer sounds REGARDLESS of the VaaS substrate state. |
| Emergency communication | Position report (via satellite messenger) is generated every 6 hours by LogKeeper. Emergency trigger: double-tap screen in alarm mode sends "MAYDAY" with position, course, speed, and last 5 spoken observations. |

---

## Power Budget Breakdown

| Component | Draw | Duty Cycle | Avg Daily Use | Total |
|-----------|------|-----------|--------------|-------|
| Pi Zero (substrate) | 0.5W | 100% | 12 Wh | 12 Wh |
| ESP32 wind | 0.1W | 100% | 2.4 Wh | 2.4 Wh |
| ESP32 bilge | 0.1W | 100% | 2.4 Wh | 2.4 Wh |
| ESP32 battery | 0.1W | 100% | 2.4 Wh | 2.4 Wh |
| Tablet (screen off) | 0.3W | 80% (sleep) | 5.8 Wh | 5.8 Wh |
| Tablet (screen on) | 3W | 20% (active) | 14.4 Wh | 14.4 Wh |
| VHF (receive only) | 0.5W | 100% | 12 Wh | 12 Wh |
| **Total** | | | **~51.4 Wh** | |

At 50W average solar production, you get ~300 Wh per day (generous but achievable in the trades). Your VaaS stack uses about 17% of your daily power budget. That's acceptable.

The tablet is the biggest variable. Keep screen-off time high. Use voice synthesis instead of screen-checking. Let the crab alert you when things need attention. The less you look, the less power you use.

---

## Quick Config

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate(config={
    "domain": "sailing",
    "power_budget": {
        "total_watts": 50,
        "safety_critical_watts": 0.7,  # Pi Zero + bilge ESP32
        "battery_critical_pct": 15,    # below this: agent apoptosis
    },
    "hard_limits": {
        "ais_cpa_min_nm": 1.0,
        "ais_tcpa_min_minutes": 12,
        "bilge_water_level_mm": 50,
        "battery_min_pct": 15,
    },
})

substrate.register(Agent(name="captain", priority=Priority.SUPREME, authority=Authority.SOVEREIGN))
substrate.register(Agent(name="watchkeeper", priority=Priority.CRITICAL, authority=Authority.OPERATOR))
substrate.register(Agent(name="navagent", priority=Priority.HIGH, authority=Authority.OPERATOR))
substrate.register(Agent(name="powermaster", priority=Priority.CRITICAL, authority=Authority.OPERATOR))
substrate.register(Agent(name="bilgewatch", priority=Priority.HIGH, authority=Authority.ADVISOR))
substrate.register(Agent(name="logkeeper", priority=Priority.LOW, authority=Authority.ADVISOR))
substrate.register(Agent(name="hermes", priority=Priority.LOW, authority=Authority.ADVISOR))

import asyncio
asyncio.run(substrate.run())
```

---

## What the System Becomes

On day 1, the system is configuration files and sensor calibrations. By day 30, the crab knows your watch schedule, your weather risk tolerance, your bilge's normal rhythm. By day 60, it can predict when you'll want to reef based on wind history and your fatigue. By day 200, crossing an ocean, the system is so natural you forget it exists.

That's the point. The VaaS substrate fades into the background — like electricity, like the boat's motion, like the sound of water against the hull. It's there. You know it's there. But you don't think about it. You think about the horizon, the wind, the next squall, the cup of tea in your hand.

When something goes wrong, the system presents itself — clearly, concisely, with the information you need and nothing you don't. Then it fades back.

On 50 watts. Around the world.

---

*The shell is solar. The crab doesn't care. The sea is the same sea at 5 watts or 500. The agent remembers.*

*The watch ends. The log remains. The solo sailor is never alone.*
