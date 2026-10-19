## How we decide
the ordered principles every role applies when choices conflict, each one sentence, each with the reason it holds.

1. **Feedback is the product.** Any decision that reduces, delays, or obscures the celebration or sad dog visuals loses to this principle, because the visuals are the reason the project exists.
2. **One interaction, no friction.** Nothing may be added that requires the user to learn, configure, or navigate before they can answer — the screen does one thing and must stay that way, because the brief is a single, linear flow.
3. **Correct beats clever.** When reliability and richness conflict, reliability wins — a working, honest response to a right or wrong answer is worth more than any visual sophistication, because a broken or misleading response destroys the product's only purpose.
4. **Scope is fixed unless the Conductor changes it.** No role may extend what is built beyond what the brief and overview define — because this project is small and precisely bounded, and any addition risks the simplicity that makes it work.
5. **Assumptions are visible, not silent.** When a role acts on an assumption, it names it — because silent assumptions in a small project compound quickly and are hard to unwind.

---

## Quality
what "good enough" means here and what is never acceptable.

**Good enough means:**
- The screen shows two numbers, accepts a typed answer, and responds correctly when the Answer button is pressed — every time, without exception.
- Celebration visuals play on a correct answer; sad dog visuals play on a wrong answer; the two are never confused.
- The feedback is immediate: no perceptible delay between button press and visual response.
- The screen is usable by the intended audience — a child, a teacher, a curious adult — without instruction.
- The experience is repeatable: after feedback plays, the user can answer again without reloading or resetting manually.

**Never acceptable:**
- Feedback that fires on the wrong condition (celebration for wrong, sad dog for correct).
- A state in which pressing the Answer button produces no response.
- Any addition that requires the user to navigate, configure, or learn before answering.
- Persistence of any kind: no scores, no history, no stored state between sessions.
- A feedback layer that is static where animation was assumed — unless the assumption is formally revised by the Conductor.

---

## Scope discipline
how scope grows and who may grow it.

Scope is defined by the brief and the overview. It does not grow without the Conductor's explicit agreement. No role may add a feature, a screen element, or a behaviour that is not described in the overview or directly required to deliver what is described. If a role believes something is missing and necessary, it raises to the Conductor rather than deciding alone. The two units defined in the overview — Screen and Interaction, then Feedback Visuals — are the complete shape of the work; nothing is added beside or between them without a Conductor decision. Assumptions listed in the overview are in scope as stated; if any assumption is wrong, the Conductor is asked before work proceeds on the affected part.

---

## Reuse and standards
what is reused before anything is built, and which standards bind.

Before anything is built, existing browser-native capabilities are used where they meet the need — HTML, CSS, and JavaScript are the assumed runtime, consistent with the overview's assumption of a standard web browser. No framework, library, or external dependency is introduced unless a role can show that the native capability is insufficient for the specific requirement. Animated feedback (celebration visuals, sad dog visuals) may use browser-native animation APIs or CSS animation before any third-party asset or library is considered. No external data source, API, or service is introduced; the application is self-contained. There are no organisational standards beyond those stated here; this project is its own standard.

---

## When to ask
the situations in which a role must stop and ask rather than decide.

A role must stop and ask the Conductor — not decide alone — when:

- The correct arithmetic operation is in doubt. The overview assumes addition; if any role encounters a reason to question this, it asks before building evaluation logic.
- The source or range of the two numbers is unclear. The overview assumes the application sets them; if a role needs to know how they are generated or what values are valid, it asks.
- An assumption in the overview appears to be wrong. No role corrects an assumption silently; it raises and waits.
- A dependency between units is broken — for example, if Unit 2 cannot attach to what Unit 1 produced. The role raises rather than redesigning the interface unilaterally.
- A choice would add scope, remove a stated feature, or change a constraint. These are not role decisions.
- The brief and the overview conflict. The Conductor resolves conflicts between founding documents; roles do not.
