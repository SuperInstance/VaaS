# Like Electricity: The Invisibility Imperative

> *Casey said the system should be like electricity — so easy to use people forget it's there. This essay asks what that means in practice, and what it costs to build something people can truly forget.*

---

## The Calculator Argument

Before 1965, an engineer who needed a cube root reached for a slide rule, or a table of logarithms, or a department calculator-operator whose full-time job was arithmetic. The act of computing was a *project*. It had a duration. It interrupted the work. It used a tool the engineer had to know how to use, badly, and which produced results that had to be hand-checked, because the engineer knew — *knew* — that he had probably made an error somewhere.

In 1965, the Wang 300 and the Olivette Programma 101 put a four-function calculator on the engineer's desk. Within ten years, every engineering office had them. Within twenty, the slide rule was museum bait. Within thirty, the cube root no longer existed as a *task* the engineer was conscious of performing. It had vanished into the background of design work.

Notice what disappeared: not just the *arithmetic*. The arithmetic had always been background — engineers didn't love doing it. What disappeared was the *cognitive cost of switching into arithmetic mode*. The bookkeeping. The re-orientation. The quiet, soul-grinding paranoia that you'd transposed a digit. The engineer stopped budgeting attention for the calculator because the calculator had become transparent.

That, exactly, is what VaaS has to do for *coordination*.

The captain doesn't love maintaining a heading. The captain loves fishing. The deckhand doesn't love plotting a return-to-course on a moving chart. The deckhand loves baiting hooks. The cook doesn't love tracking fuel burn against tide against crew count against weather windows. The cook loves feeding people. Every single crew member has a job they love, and *layered over the top of it* — like a fog — is a constant, low-grade, attention-stealing burden of *coordination*.

That fog is what VaaS has to evaporate.

---


## The Invisibility Spectrum

Not all invisibility is the same. There are four stages, and they have very different design consequences.

**Stage 1 — Visible tool.** The user knows the system exists and reaches for it. Stage-1 systems are *successful* at one thing: being seen. They earn mindshare. They become "the calculator." People remember to use them. They also interrupt flow, take training, and produce a small but real cognitive load on every use. Most software dies here.

**Stage 2 — Ambient presence.** The user no longer reaches for the system; the system reaches for the user. A notification, a glance, a single tap. Stage-2 systems have already crossed a threshold: the user has stopped budgeting attention for the *act* of using them. What they budget is attention for the *output*. We are at Stage 2 when the captain glances at the helm readout without remembering that VaaS produced it.

**Stage 3 — Background hum.** The system's outputs are part of the texture of the work. The captain doesn't "look at the system"; the captain's environment *contains* the system's outputs. The chart plotter is on. The AIS overlay is on. The depth alarm is set. None of these required a decision *this trip* — they are just *there*, like the deck underfoot. The system has become furniture. Stage 3 is where the magic is. It's also where the danger is, because furniture that fails is invisible failure.

**Stage 4 — Forgotten infrastructure.** The user no longer notices the system's *absence* any more than they would notice the absence of a particular floorboard. The system has fused with the *concept* of the work. "Of course the boat does that. Boats do that." Stage 4 is the electricity at the wall. Stage 4 is the goal.

VaaS has to climb all four.

---

## Designing for Forgetting

Forgetting is not the absence of design. Forgetting is the *result* of design that has succeeded in making itself redundant to attention. You cannot engineer forgetting by removing features. You engineer it by making features that the user never has to *remember* exist.

Three concrete rules follow.

**Rule 1: Predict the next need before the user feels it.** Every time the captain experiences a *moment of mild cognitive friction* — "Did I check the tide?" "What's the fuel state?" "When's slack water?" — VaaS has failed. The system should have answered the question before the captain asked it. Not loudly. Not in a notification. *On a surface the captain was already looking at*. The friction is the bug.

**Rule 2: Make the right action the default action.** The user must never have to choose between "use the system" and "do my job." If those are two separate options, the system is Stage 1. The system must make the *job* easier, with the system invisible inside the easier path. The captain doesn't enable the autopilot; the captain says "take us in" and the boat does it. The captain doesn't enable the fish-finder; the captain looks at the screen.

**Rule 3: Make misuse impossible.** Invisibility means the user is paying less attention to the system than they would to a tool. So the system has to be *safer than the user expects*. Misuse at Stage 4 is catastrophic because the user isn't watching. The safety envelope (Pillar 6) isn't a feature; it's a precondition for forgetting.

---

## The UX Principles of Invisibility

Translated into concrete design rules for the VaaS crew interface:

**No home screen.** The system has no "main menu." The captain never opens VaaS. The captain is *in* VaaS. The interface is the wheel, the chart, the deck, the radio, the conversation. VaaS lives in those things, and nowhere else.

**No status indicator.** "The system is healthy" is not information the captain needs. The system is the chartplotter. The chartplotter doesn't tell you it's healthy. It tells you the chart. If the chart stops updating, *that* is the indicator. Status-as-output is Stage 1. Health-as-side-effect is Stage 4.

**No logs the user has to read.** Logs are for the engineer, not the captain. Logs visible to the captain are an admission that the captain should be auditing the system. The captain should not be auditing the system. The system should be auditing itself, and only escalating to the captain when *the captain can do something about it*.

**No notifications that don't change behavior.** If a notification doesn't ask the captain to act, it is *already wrong*. The captain cannot improve the situation by knowing it. Either the system acts, or the system stays silent.

**Voice over screen where voice fits.** The captain is often hands-busy, eyes-busy, and in noise. The interface should *match the modality of the work*. A spoken "three minutes to slack water" lands in the captain's attention the same way a shouted call from the deckhand would. A chart annotation lands in the eyes. A haptic on the wheel lands in the hands. Different senses for different urgencies. Never the wrong one.

**Latency is a moral choice.** If the captain asks a question and the answer takes three seconds, the captain has been *paused*. Pausing is the opposite of forgetting. The system has one job: answer before the question is fully formed, or admit it can't.

**Glanceability over completeness.** A full chart with every annotation is Stage 1 (a tool to be read). A chart with *only the thing the captain needs right now* highlighted is Stage 3 (furniture). When in doubt, show less.

**The system gets out of the way of the crew.** The crew talks to each other. VaaS should not insert itself into crew conversation unless it can change the outcome. When two deckhands are coiling a line, VaaS is silent. When one is about to slip, VaaS speaks. The ratio is everything.

**Failure must be louder than success.** Forgetting means the user stops monitoring. So when the system fails, it must fail *visibly, immediately, and in the user's modality of the moment*. A failed autopilot on a calm day is a quiet alarm. A failed autopilot near rocks is a klaxon, a wheel shove, and a voice saying "GRAB THE WHEEL." Forgetting is built on trust. Failure must restore trust's payment in full.

---

## What VaaS Must Do (and Not Do) to Be Forgettable

VaaS must *act*, not *advise*. The captain doesn't need another opinion. The captain needs the right thing to have already happened.

VaaS must *learn the captain's patterns*, not impose its own. The captain's habitual heading home at slack water is more important than the system's statistical optimum. The system serves the captain's judgment, not the other way around.

VaaS must *preserve the captain's authority*, never *replace* it. Forgetting requires trust. Trust requires the captain to be the captain. A system that hides information to "reduce cognitive load" is not invisible — it is *deceptive*. Deception is the death of forgetting.

VaaS must *fade over time*. The first month, the captain uses VaaS consciously. By the sixth month, the captain has stopped noticing the helm readout is sourced from VaaS. By the second year, the captain has trouble remembering which features VaaS has, because *they don't reach for features anymore* — they reach for *the work*, and VaaS is in the work.

---


[← Back to README](../README.md) · [→ OpenConstruct Bridge](OPENCONSTRUCT_BRIDGE.md) · [→ Enterprise Model](THE_ENTERPRISE_MODEL.md)
