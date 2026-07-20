# The Modular Application Guide

> VaaS is the backbone. Your application is the body. This guide shows you how to connect them.

## The Core Idea

VaaS provides seven architectural pillars that handle the hard problems of multi-agent systems:
- How agents avoid being overwhelmed (Cognitive Thermodynamics)
- How agents communicate (Dual-Layer Communication)
- How agents remember (Distributed Memory)
- How agents synchronize in time (Polyrhythmic Substrate)
- How agents translate between worlds (Holographic Bridges)
- How agents resolve conflicts (Resonance Constitution)
- How agents share knowledge (Grafting Protocol)

Your application provides:
- **Who** the agents are (your specialists)
- **What** the safety limits are (your hard bounds)
- **How** the system connects to your hardware (your harness)
- **Why** the system exists (your mission)

## Step 1: Choose Your Pillars

Not every application needs all 7 pillars. Here's how to choose:

| If your domain has... | You need... |
|----------------------|------------|
| Safety-critical actions | Pillar 6 (Constitution) + [Safety Envelope](SAFETY_DEEP_DIVE.md) |
| Agents at different speeds | Pillar 4 (Polyrhythmic) |
| Agents with different vocabularies | Pillar 5 (Bridges) |
| Large amounts of accumulated knowledge | Pillars 1 (Thermodynamics) + 3 (Memory) |
| Multiple independent sites/systems | Pillar 7 (Grafting) |
| Environmental awareness (agents sensing each other) | Pillar 2 (Communication) |
| Agents that can fail or produce bad data | Pillars 1 (Apoptosis) + 3 (Holographic recovery) |

## Step 2: Define Your Agents

See [examples/custom_domain.py](../examples/custom_domain.py) for a template.

For each agent in your domain, define:
1. **Name** — what is this agent called?
2. **Tempo** — how fast does it operate? (Hz)
3. **Priority** — LOW, MEDIUM, HIGH, CRITICAL, or SUPREME?
4. **Authority** — OBSERVER, ADVISOR, OPERATOR, or SOVEREIGN?
5. **Garden** — what shorthand, structures, and filters does it need?

## Step 3: Write Your Safety Envelope

See [SAFETY_DEEP_DIVE.md](SAFETY_DEEP_DIVE.md) for the full spec.

For each safety rule, define:
1. **Layer** — HARD (never), SOFT (with permission), ADVISORY (recommend against), ETHICAL (friction)
2. **Check** — a function that returns True when the rule is VIOLATED
3. **Message** — what to tell the operator when the rule triggers

## Step 4: Connect Your Harness

See [HARNESS_GUIDE.md](HARNESS_GUIDE.md) for integration patterns.

The harness is the adapter between VaaS and your hardware/software. It translates between VaaS's internal representations (pheromones, bridges, gardens) and your domain's protocols (APIs, serial lines, network calls).

## Step 5: Test With Simulation

Before deploying to real hardware:
1. Create mock agents that simulate sensor input
2. Run the substrate with `config={"simulation": True}`
3. Verify the safety envelope catches all dangerous states
4. Test the dream cycle doesn't lose important data
5. Verify holographic recovery works when you kill an agent

---

[← Back to README](../README.md)
