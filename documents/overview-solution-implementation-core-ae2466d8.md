## The solution in one page

A single screen application is being built. It presents two numbers to the user, provides a field for the user to type their answer, and a button labelled "Answer". When the button is pressed, the application evaluates whether the user's input matches the correct arithmetic result. If correct, celebration visuals play on screen. If wrong, sad dog visuals play. The screen is self-contained: no navigation, no setup, no external dependencies visible to the user.

The main parts are:
- **Number display** — two numbers shown clearly on screen, the operands of the arithmetic problem.
- **Answer field** — a text input where the user types their answer.
- **Answer button** — the single action trigger; pressing it initiates evaluation.
- **Feedback layer** — the visual response: celebration visuals on a correct answer, sad dog visuals on a wrong answer. This layer is the product.

These parts sit on one screen. The flow is linear and repeatable: see numbers → type answer → press button → see feedback.

---

## The shape of the work

**Unit 1 — Screen and Interaction**
Build the screen: number display, answer field, and Answer button. This is the structural foundation everything else sits on. It must exist and work before feedback can be attached to it.

**Unit 2 — Feedback Visuals**
Build and integrate the celebration visuals and the sad dog visuals, and wire them to the correct/wrong evaluation result. This unit is second because it depends on the interaction structure Unit 1 produces; the visuals have nothing to attach to until the screen and its evaluation logic exist.

---

## Constraints

- **Screen-based delivery** — the solution must run on a screen. No physical, voice-only, or print form is in scope.
- **Single screen** — the entire experience must be contained on one screen. No routing, no pages, no navigation.
- **Two number inputs only** — the problem is always expressed as exactly two numbers. The arithmetic operation is implied by the design; no operator selection is in scope.
- **Named button** — the action button must be labelled "Answer". This is fixed by the brief.
- **Binary feedback only** — the response is either celebration visuals or sad dog visuals. No partial credit, no "close" state, no neutral state.
- **No persistence** — no scores, history, accounts, or data storage of any kind.

---

## Assumptions

- The arithmetic operation is addition. The brief specifies two number inputs and an answer; it does not name an operation. Addition is the simplest and most natural reading. If the operation is different or user-selectable, the number display and evaluation logic change.
- The two numbers are generated or set by the application, not entered by the user. The brief describes "two number inputs" as things the user sees and responds to, not configures. If users are meant to enter both numbers themselves, the interaction model changes.
- "Celebration visuals" and "sad dog visuals" are animated or motion-based on-screen effects, not static images. If static images are acceptable, the feedback unit's scope reduces.
- The screen must work in a standard web browser. No native app, no installed software. If a different runtime is required, technology choices in Unit 1 change.
- There is no time constraint on how long the user takes to answer. The button is the only trigger; no timer exists.

---

## Glossary

**Answer button** — the on-screen button labelled "Answer"; pressing it is the sole trigger for feedback evaluation.

**Answer field** — the text input into which the user types their response to the arithmetic problem.

**Celebration visuals** — the on-screen animated response shown when the user's answer is correct; the primary reward signal.

**Correct answer** — the arithmetic result the application expects; the value the user's input is compared against.

**Evaluation** — the act of comparing the user's input to the correct answer, performed when the Answer button is pressed.

**Feedback layer** — the part of the screen responsible for displaying either celebration visuals or sad dog visuals after evaluation.

**Number display** — the part of the screen that shows the two operands of the arithmetic problem to the user.

**Sad dog visuals** — the on-screen animated response shown when the user's answer is wrong; the commiseration signal.

**Screen** — the single view the user sees and interacts with; the whole of the application's visible surface.