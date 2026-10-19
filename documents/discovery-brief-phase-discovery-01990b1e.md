# Discovery Brief — Arithmetic Feedback Screen

---

## What exists

There is no prior version of this application. Nothing has been built. The starting point is a single sentence of intent from the project seed: *"A screen based solution which takes two number inputs and a field to write the answer, and a button named Answer, when pressed if the answer is correct have celebration visuals if wrong have sad dog visuals."*

From that seed, the project has produced a vision, an overview, a set of principles, and a set of instructions. These four documents are the entirety of what exists. No code, no design, no prototype, no prior iteration is present. Discovery works from documents alone.

---

## Who is involved

**The user** — a child doing homework, a teacher running a quick classroom exercise, or a curious adult. They need: two numbers they can see clearly, a place to type their answer, a button they can press, and an immediate, emotionally legible response. They need nothing else. They must not be required to learn, configure, or navigate before they can answer.

**The Conductor** — the authority who resolves conflicts between founding documents, confirms or revises assumptions, and approves any change to scope. Discovery raises to the Conductor when an assumption cannot be confirmed from the documents alone. The Conductor is the only party who can settle the number-range question or any other assumption that the documents leave open.

**The builders** — the roles who will build Unit 1 (Screen and Interaction) and Unit 2 (Feedback Visuals). They need a confirmed assumption register so they do not build on a foundation that may be wrong. They are not involved in Discovery; they receive its output.

**No other party is involved.** There is no external system, no data source, no third-party service, and no organisational stakeholder beyond those named above.

---

## Problems and opportunities

### Assumptions tested against the brief and the documents

**Assumption 1 — The arithmetic operation is addition.**
*Source:* Overview, Assumptions section. *Brief evidence:* The brief says "two number inputs and a field to write the answer." It names no operation. Addition is the overview's reading — "the simplest and most natural." The vision says "basic arithmetic." Neither the brief nor the vision names addition explicitly.
*Finding:* **Confirmed by the Conductor.** The Conductor has confirmed the arithmetic operation is addition. This is settled. The evaluation logic will compare the user's input against the sum of the two displayed numbers. No role may re-raise this question.

**Assumption 2 — The two numbers are generated or set by the application, not entered by the user.**
*Source:* Overview, Assumptions section. *Brief evidence:* The brief says "takes two number inputs." The phrase "number inputs" is ambiguous: it could mean two numbers displayed as inputs to the problem (i.e., operands the user sees), or two fields the user fills in. The overview reads it as the former — the application sets the numbers, the user sees them. The vision says "two numbers are shown, the user types their answer," which supports the overview's reading. The instructions describe a "number display" as one of the four main parts, not a number entry field.
*Finding:* **Confirmed by the vision and the instructions.** The vision's language ("two numbers are shown") and the instructions' four-part structure (number display, answer field, Answer button, feedback layer) together confirm that the application generates or sets the numbers and displays them. The user does not enter the operands.

**Assumption 3 — Feedback visuals are animated, not static.**
*Source:* Overview, Assumptions section. *Brief evidence:* The brief says "celebration visuals" and "sad dog visuals." It does not say animated. The instructions state "The feedback visuals are animated, not static images" and list this as a definition-of-done requirement. The principles say "Correct beats clever" — reliability over richness — but the instructions treat animation as a firm requirement, not a preference.
*Finding:* **Confirmed by the instructions.** The instructions make animation a hard requirement in the definition of done. The assumption is not in doubt from the documents. The Initiation Brief names this as a delivery risk (Risk 1): browser-native animation may not be sufficient. Discovery notes this risk but does not raise it — the instructions already require animation, and the question of whether browser-native capability is sufficient is an Enterprise Architecture and builder question, not an assumption question. The assumption itself — that animation is required — is confirmed.

**Assumption 4 — The screen runs in a standard web browser.**
*Source:* Overview, Assumptions section. *Brief evidence:* The brief says "screen based solution." The instructions name Chrome, Firefox, Safari, and Edge as the target browsers and state that opening `index.html` in a browser is sufficient. The toolchain section confirms no server, no build step, no installed software.
*Finding:* **Confirmed by the instructions.** The browser runtime is not an assumption; it is a stated constraint in the instructions. No conflict exists.

**Assumption 5 — There is no time constraint; the button is the only trigger.**
*Source:* Overview, Assumptions section. *Brief evidence:* The brief names one trigger: the Answer button. No timer is mentioned anywhere in the brief, vision, overview, or instructions.
*Finding:* **Confirmed.** The button is the sole trigger. No timer is in scope.

---

### Gaps between the brief and the overview

**Gap 1 — The range and source of the generated numbers is unspecified.**
The overview confirms the application sets the numbers, but neither the overview nor the brief states what values are valid. Are the numbers single-digit? Two-digit? Can they be zero? Can the correct answer exceed a certain value? The instructions do not specify. This is not an assumption the overview names, but it is a decision the builder of Unit 1 will have to make. If left to the builder, two Work Packages may produce incompatible number ranges, or a builder may choose a range that makes the problem trivially easy or unexpectedly hard for the intended audience (a child doing homework).
*Recommendation:* Requirements Phase must specify the number range explicitly. If the range cannot be derived from the documents, Requirements raises to the Conductor.

**Gap 2 — The reset trigger is unspecified.**
The overview says the experience is repeatable. The instructions name Repeatability as a test case. But neither document states what triggers the reset: does the screen reset automatically after feedback plays? Does the user press a button? Does pressing the Answer button again reset the screen? The brief is silent. This gap is named in the Initiation Brief (Risk 5) as a risk.
*Recommendation:* Requirements Phase must specify the reset trigger and the reset sequence. Discovery recommends that the reset be automatic or triggered by a single, obvious action — consistent with the principle of one interaction, no friction.

**Gap 3 — The empty field case is underspecified in the brief.**
The brief does not address what happens when the Answer button is pressed with no value in the answer field. The instructions name this as a test case (Empty field) and state the application must either treat it as wrong or do nothing, but must not crash. The overview does not address it.
*Recommendation:* Requirements Phase must specify the empty field behaviour explicitly — either "treat as wrong (sad dog plays)" or "do nothing (no feedback, no error)" — and make it testable.

**Gap 4 — The brief implies "two number inputs" could mean two user-entered operands.**
This is addressed under Assumption 2 above and confirmed as resolved. Noted here for completeness: the brief's phrasing is genuinely ambiguous, and the resolution rests on the vision's language rather than the brief itself. Requirements Phase should state the number display behaviour in terms that leave no room for a builder to re-read the brief and reach the opposite conclusion.

---

## Constraints found

The following constraints are confirmed from the documents and cannot change without a Conductor decision:

1. **Single HTML file, one CSS file, one JavaScript file, one assets folder.** The file structure is fixed by the instructions. No additional files, no subdirectories beyond `assets/`, no build output.
2. **No external dependencies.** No CDN links, no npm packages, no remote assets. The application is self-contained.
3. **Browser runtime only.** The application runs from the file system in a current evergreen browser. No server, no native app.
4. **Button labelled "Answer".** The label is fixed by the brief. It cannot be changed.
5. **Binary feedback only.** Celebration or sad dog. No partial credit, no neutral state, no "close" state.
6. **No persistence.** No scores, no history, no stored state between sessions.
7. **Single screen.** No routing, no navigation, no second page.
8. **Animation required.** The definition of done requires animated feedback. Static images are not acceptable unless the Conductor formally revises this.

---

## Recommendations

The following items are passed to Requirements Phase, in priority order:

**1. Specify the number range.**
Requirements must state the minimum and maximum values for each operand, and whether zero is a valid operand. The range should be appropriate for the stated audience (a child doing homework). If the Conductor does not specify a range, Requirements raises before deciding alone.

**2. Specify the reset trigger and sequence.**
Requirements must state what triggers the transition from post-feedback state to ready state, and what "ready state" means for each element: new numbers displayed, answer field cleared, feedback layer hidden. The reset must be consistent with one interaction, no friction — it must not require the user to navigate or reload.

**3. Specify the empty field behaviour.**
Requirements must choose one of the two acceptable behaviours (treat as wrong, or do nothing) and state it as a testable requirement. The choice should be consistent with the principle that the button press must never produce no response — which favours treating empty as wrong (sad dog plays).

**4. State the number display behaviour unambiguously.**
Requirements must make explicit that the two numbers are displayed by the application, not entered by the user, and that the user's only input is the answer field. This closes the brief's ambiguity and prevents a builder from re-reading the brief differently.

**5. Confirm the feedback trigger conditions in testable form.**
Requirements must state the exact condition under which each feedback type plays — not "if correct" but "if the integer value of the answer field equals the sum of the two displayed numbers" — so that the wrong-condition feedback test case (No wrong-condition feedback) has a precise, testable definition.

---

## Assumption register — confirmed

All five assumptions are now confirmed. No raise is outstanding. Discovery is complete.

| # | Assumption | Status | Source of confirmation |
|---|---|---|---|
| 1 | The arithmetic operation is addition | **Confirmed — settled** | Conductor |
| 2 | The application sets the numbers; the user does not enter them | **Confirmed** | Vision, Instructions |
| 3 | Feedback visuals are animated, not static | **Confirmed** | Instructions (definition of done) |
| 4 | The screen runs in a standard web browser | **Confirmed** | Instructions (toolchain) |
| 5 | The button is the only trigger; no timer exists | **Confirmed** | Brief, Vision, Overview, Instructions |

Requirements Phase may open. It works from this confirmed assumption register and the gaps and recommendations above.