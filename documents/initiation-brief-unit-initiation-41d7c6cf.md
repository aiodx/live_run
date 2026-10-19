# Initiation Brief — Arithmetic Feedback Screen

---

## Purpose of Initiation

Initiation exists to establish everything that must be true before delivery can be cut into Units and built. It does not build anything. It answers five questions — one per Phase — that, together, confirm the project is coherent, the assumptions are sound, the requirements are complete, and the specification is ready to be handed to builders. Until these five questions are answered, no Work Package should be opened.

The five questions are:
1. What is the technical shape of the thing being built, and does it hold together? *(Enterprise Architecture)*
2. What data does the application create, hold, and discard, and is that safe and sufficient? *(Data Architecture)*
3. Are the assumptions in the overview correct, and is anything missing from the brief? *(Discovery)*
4. What must the application do, stated precisely enough to test? *(Requirements)*
5. Is the specification complete, consistent, and ready to cut into Units? *(Specification)*

---

## Inputs

- **Initial brief (project seed):** "A screen based solution which takes two number inputs and a field to write the answer, and a button named Answer, when pressed if the answer is correct have celebration visuals if wrong have sad dog visuals."
- **Vision:** The project vision document, which names the purpose, the audience, what done looks like, what the project is not, and the three overriding principles.
- **Overview:** The solution overview, which defines the two Units, the four main parts of the screen, the constraints, the assumptions, and the glossary.
- **Principles:** The five ordered principles governing every role decision.
- **Instructions:** The repository layout, toolchain, testing approach, coding conventions, and definition of done.
- **Conductor confirmation:** The arithmetic operation is addition.

---

## Each Phase, in one paragraph

**Enterprise Architecture**
Enterprise Architecture must confirm that a single HTML file, one CSS file, one JavaScript file, and an assets folder — all running directly in a browser from the file system — are sufficient to deliver the full interaction: number display, answer field, Answer button, evaluation logic, and both feedback visuals. It answers the question: *does the chosen technical shape support every stated requirement without contradiction, and are there no structural gaps between the parts?* Its deliverable is a confirmed architecture that names each file's responsibility, the interfaces between them (DOM IDs, JavaScript function signatures, CSS class contracts), and the browser-native capabilities that will carry the animation requirement — so that no builder has to invent an interface that another builder assumed.

**Data Architecture**
Data Architecture must define every value the application holds at runtime — the two operands, the correct answer, and the user's input — stating where each is stored (JavaScript variable, DOM element value), how long it lives (for the duration of one interaction cycle), and what happens to it when the screen resets. It answers the question: *is there any data the application needs that has not been accounted for, and does any of it persist in a way the brief forbids?* Its deliverable is a complete runtime data map confirming that no value is written to storage, no value crosses a session boundary, and the reset cycle leaves the application in a clean state ready for the next interaction.

**Discovery**
Discovery must test every assumption named in the overview against the brief, the vision, and the Conductor's confirmed answers, and surface any gap between what the brief implies and what the overview has decided. The critical assumptions are: that the arithmetic operation is addition (confirmed by the Conductor); that the application generates the two numbers (the user does not enter them); that feedback visuals are animated rather than static; and that the screen runs in a standard web browser. It answers the question: *are the assumptions the overview rests on correct, and is there anything the brief requires that the overview has not addressed?* Its deliverable is a confirmed assumption register. The arithmetic operation assumption is now settled: addition is confirmed. Discovery may close on this point and proceed to confirm the remaining assumptions. Discovery is currently held because it raised; it reworks against this amended brief.

**Requirements**
Requirements must state, in testable form, every behaviour the application must exhibit — covering the correct answer path, the wrong answer path, the empty field case, repeatability, the absence of console errors, and the guarantee that feedback never fires on the wrong condition. The evaluation logic compares the user's input against the sum of the two displayed numbers (addition, confirmed). It answers the question: *what must the application do, stated precisely enough that a builder knows when they are done and a reviewer knows what to check?* Its deliverable is a numbered requirements list in which every item is independently testable, maps to at least one of the six named test cases in the instructions, and contains no ambiguity about pass or fail. Requirements that cannot be tested are rewritten or raised.

**Specification**
Specification must take the confirmed architecture, the data map, the validated assumptions, and the requirements list, and produce a single document that a builder can work from without needing to consult any other source. It answers the question: *is the description of the thing to be built complete, consistent, and free of gaps that would force a builder to decide something Initiation should have decided?* Its deliverable is a specification that covers the screen layout, the evaluation logic (addition), the reset behaviour, the feedback trigger conditions, the file and naming conventions each builder must follow, and the acceptance criteria each Unit must meet. The specification is the input the two delivery Units are cut from.

---

## How the delivery Units will be cut

At the close of Initiation, two Units will be proposed. The criteria for cutting them are:

**By dependency:** Unit 1 (Screen and Interaction) must exist before Unit 2 (Feedback Visuals) can be built. The feedback layer has nothing to attach to until the screen structure, the evaluation logic, and the DOM contracts are in place. This dependency is the primary cut line.

**By domain:** Unit 1 owns structure and logic — the HTML skeleton, the number display, the answer field, the Answer button, and the evaluation function that compares the user's input to the sum of the two displayed numbers (addition). Unit 2 owns response — the celebration visuals, the sad dog visuals, and the wiring that connects evaluation outcome to visual trigger. These are distinct domains with a clean interface between them: the evaluation result (correct / wrong) is the handoff point.

**By risk:** The higher-risk work is in Unit 2, because the feedback visuals are the product and their animated nature is an assumption that has not yet been validated by a builder. Unit 1's risk is lower — HTML, CSS layout, and an addition comparison function are well-understood. Sequencing Unit 1 first means Unit 2 can be built against a known, tested foundation rather than a moving one.

No further Units are proposed. The two Units defined in the overview are the complete shape of the delivery.

---

## Risks to the whole

**Risk 1 — The animation assumption is wrong or undeliverable.**
The overview assumes feedback visuals are animated, not static. If the builder assigned to Unit 2 finds that browser-native CSS animation or the Web Animations API cannot produce a satisfactory celebration or sad dog effect without an external library or asset, the assumption breaks. *What Initiation does:* Discovery Phase confirms whether browser-native animation is sufficient for both feedback types before any builder opens Unit 2. If it cannot be confirmed, the Conductor is asked to revise the assumption or approve an alternative approach.

**Risk 2 — The arithmetic operation assumption. RESOLVED.**
The Conductor has confirmed the arithmetic operation is addition. The evaluation logic will compare the user's input against the sum of the two displayed numbers. This risk is closed. Discovery Phase may confirm this assumption and proceed.

**Risk 3 — The interface between Unit 1 and Unit 2 is undefined at handoff.**
Unit 2 depends on Unit 1 producing a stable DOM structure and a clear evaluation signal. If Unit 1 does not name and document the DOM IDs and the JavaScript interface that Unit 2 will consume, Unit 2 builders will invent their own assumptions and the integration will break. *What Initiation does:* Enterprise Architecture Phase defines the interface contract explicitly — the DOM element IDs, the CSS class names for feedback states, and the JavaScript function or event that carries the evaluation result — so that both Units build to the same contract.

**Risk 4 — Scope creep from well-intentioned builders.**
The project is small and the temptation to add — a score counter, an operator selector, a difficulty setting — is real. Any addition risks the simplicity that makes the one interaction work. *What Initiation does:* The Specification Phase embeds the scope boundary explicitly in the specification each Unit works from, and the definition of done requires every Work Package owner to confirm no out-of-scope element is present before submission. The principles (Scope is fixed unless the Conductor changes it) are restated in the specification so no builder can claim they were not visible.

**Risk 5 — The reset behaviour is underspecified.**
Repeatability is a named test case and a quality requirement, but the brief does not describe what "reset" means in detail — whether new numbers are generated, whether the answer field is cleared, whether the feedback layer is hidden. If this is left to builders to decide, two Work Packages may produce incompatible reset behaviours. *What Initiation does:* Requirements Phase specifies the reset sequence explicitly: what state each element is in at the start of a new interaction cycle, and what triggers the transition from post-feedback to ready state.

---

## Note on Discovery

Discovery is held because it raised. It reworks against this amended brief. No content in this brief changes the substance of what Discovery must confirm; the raise is settled by this rework run, and Discovery proceeds against the brief as it now stands.
