# Open Terminal: The Agent's Terminal, Not the Human's

> *The terminal is not a tool. It is a field. The agents are not users. They are excitations in the field.*

## The Inversion

Every terminal you have ever used was the **human's** terminal. The shell is shaped around fingers, eyes, and a person typing at human speed. It assumes human working memory (a handful of variables), human reference patterns (alphabetical `ls`), and human tolerances (two seconds to wait for autocomplete). Even modern agent harnesses — Claude Code, Aider, OpenHands — inherit this assumption. The "terminal" the agent runs in is just a stripped-down human terminal that has been retrofitted to accept `cd /home/agent && python main.py` instead of `cd /home/me && python main.py`. The cursor still blinks. The output still scrolls. The agent is the only one reading it, and yet it is shaped like a typewriter for someone who never looks at it.

We want to invert that.

In the VaaS architecture, **the terminal belongs to the agent.** Each agent — Pincher, Hermes, tzpro-agent, boat-agent, Hermes-CoCapn — gets its own terminal. This terminal is **not** a CLI that a human will ever read. It is the agent's private workspace: its desk, its notebook, its scribble-on-the-back-of-an-envelope. The agent reads from it, writes to it, organizes itself with it, and forgets into it. The human never touches it directly. The human interacts with the entire system through CoCapn, who translates the agent's terminal into something a person can act on.

This is the deep meaning of `SuperInstance/open-terminal`. The repository's name is a description of what should happen on the inside — the *open* part is the substrate being open to the agent — not of what should happen in front of a human user. The interface we are designing is a terminal opened **for** an agent, not opened **to** a person.

## What the Human-Facing Terminal Got Wrong

Three things.

**First, it leaks.** When Hermes emits a partial traceback or a raw token stream, that output streams back to the user's screen. The noise of implementation — internal-struct dumps, diagnosis messages, attempt-and-retry logs — becomes the user's problem. Today's CLI agents dump it inline.

**Second, it interprets for the wrong reader.** Shell prompts use parenthetical paths, ISO timestamps, lowercase identifiers — optimized for the human eye. The same lines are slower for an LLM to parse and trivially amenable to compression. We pay a tax every cycle because the convention comes from 1970.

**Third, it does not remember.** A terminal is supposed to be stateless. `bash` does not learn you usually want the chartplotter feed first; `tmux` does not remember "the grid" means the southern grid. State is delegated to files, aliases, and muscle memory. For an agent that runs continuously for hours and reads its own terminal, this is wasted surface.

The VaaS-aware Open Terminal fixes all three. It leaks nothing to the human. It carries its own compression grammar. And it remembers everything the agent learns, indefinitely.

## Architecture: A Terminal Per Agent

Every agent in the substrate owns a private terminal. The terminal is a process-local addressable namespace, conceptually similar to `/proc/<agent>/terminal`, persisted to the agent's garden and (where appropriate) redundantly stored in cryogenic memory. The terminal is reachable by the agent itself, by the bridges that translate to and from other agents, and by CoCapn when she is composing a briefing — but it is unreachable by the human directly. The human talks to CoCapn; CoCapn talks to the terminals.

The terminal has four surfaces:

- **Header** — the agent's current identity, tempo phase, and entropy budget. Pincher's header reads `pincher · 19.7Hz · H=0.12`. Hermes's reads `hermes · 0.18Hz · dreaming=false`. The header is always the first thing the agent reads when it ticks.
- **Pheromone tape** — short, low-TTL semantic messages emitted by other agents or by sensors, formatted in the agent's compressed dialect. This is the dual-layer communication channel (Pillar 2) rendered as a rolling buffer the agent reads on each cycle.
- **Bridge inbox** — explicit, addressed messages with delivery confirmation and shadow context (Pillar 5). Commands from CoCapn arrive here. Critical state queries arrive here.
- **Scratch** — the agent's own notepad. Inferences it has not yet committed to the garden. Drafts of briefings. Unverified hypotheses. The agent writes here freely; it is garbage-collected by the dream cycle.

In the original `open-terminal` repository, the layout was a single shell. In VaaS, the layout is a multi-tenant field: every agent has its own layout, drawn from its own cognitive state. They share a common wire format — pheromone syntax — but each terminal renders its own dialect on read.

## The Three Layers of Agent-Terminal State

An agent's terminal is not uniform. It has three layers with different lifetimes and access patterns, mirroring the agent's own cognitive stratification.

**Layer 1 — Shorthand.** This is the agent's lexicon. When Pincher writes `ridge` in its scratch, every reader of Pincher's terminal knows — without explanation — that `ridge` is a compressed reference to the full procedure "reduce throttle to idle, lock helm amidships, broadcast 'man overboard' on channel 16, mark GPS, switch to reflex zoom." Shorthand is lossless within an agent's garden but transparent outside it: bridges expand `ridge` back to the full procedure before crossing the membrane. This is Pillar 5 applied to an agent's own language. Shorthand grows over time. New shorthand is added during dream cycles when the agent realizes it has used the same multi-token sequence more than N times. Old shorthand that has not been used in M dream cycles is moved to cryogenic memory but not deleted — it can be re-promoted if conditions change.

**Layer 2 — Assumed structures.** When tzpro-agent opens its terminal and reads the latest sonar frame, it does not see raw integers — it sees `bedrock @ 042° offset=12m confidence=0.91`. The agent assumes a structured world. The terminal's job is to enforce this. Every scratch entry, every incoming bridge, and every pheromone must conform to the assumed structure that the agent has internalized. If a message violates an assumed structure — for example, a depth reading that "should" be in fathoms arrives in meters — the terminal flags it on the pheromone tape as a structural mismatch with a confidence score. The agent can then decide whether to trust the message or filter it. Assumed structures are the agent's ontology, made explicit.

**Layer 3 — Need-to-know filters.** Some agent-terminal state is not for CoCapn, not for other agents, not for the holographic mirror. It is the agent's private monologue: doubts, mid-thought revisions, suspicion about another agent's coherence. Pincher's terminal filters these out before they cross any bridge. Hermes's terminal holds its search-thoughts — "is this query the third one about kelp beds in the last hour?" — and never leaks them to tzpro-agent, even though tzpro-agent is asking the same question through a legitimate bridge. Need-to-know filters are tuned by the agent's own self-judging critic (Pillar 1's dream cycle). If the critic determines that a thought should have been shared but wasn't, it widens the filter for the next cycle. If a thought was shared and turned out to be irrelevant noise, it narrows.

## How the Terminal Grows

A terminal is not a static namespace. It is a living organism.

**At intake**, when an agent is first registered, its terminal is empty except for a minimal header and a single instruction: *here is what you are, here is what you sense, here is what other agents exist, here are the bridges you may speak through.* The terminal starts as a tabula rasa with a constitutional seed.

**During operation**, the terminal grows through three mechanisms. First, **shorthand accumulation**: every time the agent writes a redundant sequence, the dream cycle compresses it. Second, **structural refinement**: each time the agent encounters a message that doesn't fit its assumed structures, it updates the structure to accommodate or rejects it cleanly with logged reason. Third, **filter tuning**: as above, the self-judging critic widens and narrows need-to-know boundaries.

**At dream cycle**, the terminal undergoes its major reorganization. Compression is applied. Old scratch is moved to cryogenic memory. New shorthand is baked in. Need-to-know filters are revised based on the cycle's dream quality. The terminal that wakes up after a dream cycle is functionally the same agent but more compact and better tuned. This is the analog of the gardener pruning a hedge — the hedge grows back healthier.

**At grafting**, when the vessel encounters another vessel's hive, the terminal accepts pollen — high-confidence shorthand and structures — and tests them against the agent's local garden. If they improve the agent's entropy profile, they are adopted. If they conflict, they are quarantined.

**At apoptosis**, when the agent detects it is incoherent, the terminal performs a final state dump to Hermes, and the scratch is preserved in cryogenic memory for any future agent whose garden profile overlaps enough to inherit it.

## Connection to the VaaS Substrate

The Open Terminal is not an add-on to VaaS. It is **the** execution surface where Pillar 1, Pillar 3, and Pillar 5 become lived reality.

- **Pillar 1 (Cognitive Thermodynamics)** governs the terminal's dream cycles. The terminal is what gets compressed, expanded, and reorganized during dreams. Without the Open Terminal, Pillar 1 has nothing to operate on.
- **Pillar 3 (Distributed Memory)** is the terminal's persistence layer. Tier 1 (active garden) is held in the terminal's working memory. Tier 2 (cryogenic) is the terminal's archive. Tier 3 (holographic) is the redundant fragments scattered across other agents' terminals so that any agent can reconstruct any other agent's terminal from a fragment.
- **Pillar 5 (Holographic Bridges)** is the terminal's translation layer. Every message that leaves or enters the terminal passes through a bridge that maintains a shadow copy of the original intent. When tzpro-agent sends `bedrock @ 042°` to Pincher's terminal, the bridge holds the full raw frame as shadow. If Pincher's interpretation later proves wrong, the shadow can be replayed for diagnosis.

The terminal is also a measurable contributor to the **Operator Field Ψ(t)**. The collective state of all agents' terminals — their entropy, their coherence, their bridge quality — is a major input to Ψ. CoCapn watches Ψ; CoCapn does not watch the terminals directly. The terminals are the substrate's interior; Ψ is its exterior.

## The Human Pathway: Via CoCapn

The critical, easy-to-miss point: **the human never sees the agent's terminal.**

The human is a sovereign authority in the VaaS constitution. The human can override anything. But the human cannot usefully read tzpro-agent's scratchpad while steaming at 18 knots into a fog bank.

Instead, CoCapn — the human's co-captain — reads all the terminals continuously. She translates, summarizes, filters, escalates, and stays silent when silence is right. When the human asks "what does Hermes think," CoCapn reads Hermes's terminal and composes a one-sentence answer. The human's interaction surface is CoCapn's interface, full stop.

This inversion is what makes Open Terminal possible. If a human could read the terminal, every agent would need to behave politely. `ridge` would have to expand to "I, Pincher the Reflex Agent, am about to reduce your throttle to idle..." That is unhelpful and slow. With the human out of the picture, Pincher writes `ridge` and trusts the bridge to CoCapn to expand it only when expansion is needed.

Humans and agents communicate in their native idioms. Pincher loops in milliseconds with compressed shorthand. CoCapn composes in calm prose. The human reads at human pace. The bridge between is itself an aspect of CoCapn that the operator-field design calls the *shorthand oracle* — it maintains the shadow, learns which expansions are necessary, and which can be left compressed because CoCapn already knows.

---

*Next: [CoCapn Design →](COCAPN_DESIGN.md) — how the human-facing co-captain learns the human and routes between stations.*
*Also: [Attention Budget →](ATTENTION_BUDGET.md) — how the substrate allocates expensive compute across stations.*
