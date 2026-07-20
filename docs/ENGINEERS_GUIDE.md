# The Engineer's Guide to VaaS

> Written for mechanics, builders, and integrators. You know hydraulic systems, electrical panels, and engines. This guide translates VaaS into your language.

## Think of VaaS Like a Hydraulic System

If you understand hydraulic systems, you understand VaaS. Every concept has a mechanical analog.

| VaaS Concept | Hydraulic Analog |
|-------------|-----------------|
| Agent | A pump — it does one specific job |
| Pheromone | Pressure in the line — other pumps sense it |
| Explicit bridge | A direct hose from pump A to pump B with a check valve |
| Safety envelope | A relief valve — opens before pressure gets dangerous |
| Garden | The pump's character — how it's tuned, what pressures it likes |
| Dream cycle | A filter flush — cleaning the lines after a hard day |
| Bridge entropy | Pressure loss at a fitting — more fittings = more loss |
| Operator Field | System pressure gauge — tells you the health of the whole circuit |
| Human veto | Manual bypass valve — grab it and you bypass everything |
| Apoptosis | A pump that detects it's cavitating and shuts itself down |

## The Hardware Stack (What Goes Where)

```
┌──────────────────────────────────────────────┐
│           THE WHEELHOUSE PC                   │
│        (The "Turbo Shell" for the crab)       │
│                                               │
│  Software agents run here as processes:       │
│                                               │
│  ┌────────────┐  ┌──────────┐  ┌──────────┐ │
│  │ boat-agent │  │ Hermes   │  │ Pincher  │ │
│  │ (SAFETY)   │  │ (MEMORY) │  │ (REFLEX) │ │
│  │ Rust       │  │ Python   │  │ Python   │ │
│  │ 10 Hz      │  │ 0.2 Hz   │  │ 20 Hz    │ │
│  └──────┬─────┘  └────┬─────┘  └────┬─────┘ │
│         └──────────────┼──────────────┘      │
│                   ┌────┴────┐                  │
│                   │ Substrate│  (The manifold) │
│                   └────┬────┘                  │
│                        │                       │
│              ┌─────────┴─────────┐             │
│              │   NMEA 0183 Bus   │  (The line) │
│              │   (Serial / USB)  │             │
│              └─────────┬─────────┘             │
├────────────────────────┼──────────────────────┤
│              ┌─────────┴─────────┐             │
│              │   PHYSICAL I/O    │             │
│              │                   │             │
│  GPS    Sounder    Autopilot    Compass       │
│  (in)   (in)       (out)        (in)          │
└───────────────────────────────────────────────┘
```

## The NMEA Bus Is the Hydraulic Line

NMEA 0183 is the standard marine data protocol. It's a serial connection (like RS-422) that carries sentences — text strings that encode position, depth, heading, speed, etc.

```
$GPGGA,104800,5547.300,N,13141.780,W,1,08,0.9,00010,M,,,,*hh
       │       │           │              │
       time    latitude    longitude      altitude
```

Every instrument on the boat outputs NMEA sentences. The wheelhouse PC reads them. boat-agent processes them. The substrate routes them.

For more on NMEA, see [the NMEA tutorial](https://en.wikipedia.org/wiki/NMEA_0183).

## The Safety Chain (Read This Twice)

This is how the boat stays safe. Nothing bypasses this chain.

1. **The captain grabs the wheel.** A physical switch detects human hands on the helm. The Rust kernel sees this at 10 Hz. Instantly — within 100ms — all AI commands to the autopilot are cut. The captain has raw hydraulic control.

2. **boat-agent checks every command.** Before any AI command reaches the serial port, the Rust kernel checks it against `vessel.toml` — a configuration file that defines the boat's physical limits:
   ```toml
   [limits]
   max_rate_of_turn_deg = 15.0
   max_speed_kts = 10.0
   draft_m = 4.5
   min_depth_below_keel_m = 2.0
   ```

3. **If a command violates a limit, it's rejected.** The autopilot never sees it. A voice alert tells the captain what happened.

4. **Nothing can bypass this.** Not a bug. Not a hack. Not the AI. The kernel runs in Rust on bare metal. The check happens in the kernel, not in user space.

## What To Check When Things Go Wrong

### Diagnostic Procedure (Like Troubleshooting An Engine)

**Step 1: Is the PC on?** (Check the breaker.)
**Step 2: Is the serial cable connected?** (Check the NMEA adapter.)
**Step 3: Is the GPS outputting?** (Check the status light on the GPS.)
**Step 4: Is the safety kernel running?**
```bash
vaas status --agent boat-agent
# Should show: health=1.0, envelope=active
```

### Common Problems

| Symptom | Like In An Engine | Fix |
|---------|-------------------|-----|
| Autopilot ignores AI | Manual override engaged (like the bypass valve is open) | Normal. Release the wheel. |
| System sluggish | Filter clogged (entropy too high) | Force a dream cycle (flush the filter) |
| Agent died | Pump cavitation detected, self-shutdown | Restart the agent. Check sensor inputs. |
| Vision missing things | Gauge reading low (GPU overloaded) | Reduce scan frequency. Check GPU temperature. |
| Voice not working | Intercom broken (mic/Bluetooth issue) | Check Bluetooth connection. Check background noise. |
| Chart marks missing | Gauge disconnected (TimeZero import folder) | Check folder permissions. |

## The Hermit Crab Migration

The crab (software agent with all memories and habits) can move between shells (hardware):

```
FROM (old shell)          TO (new shell)
──────────────            ──────────────
Phone/table          →    Wheelhouse PC
vaas export > g.json      vaas import g.json

Wheelhouse PC        →    Fleet cluster
vaas export > g.json      vaas import g.json
```

The crab carries everything. The new shell just provides more muscle.

---

[← Back to README](../README.md) · [→ Captain's Guide](CAPTAINS_GUIDE.md) · [→ Safety Deep Dive](SAFETY_DEEP_DIVE.md)
