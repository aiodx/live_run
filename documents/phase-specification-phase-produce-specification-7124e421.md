# Phase Specification — Enterprise Architecture

---

## Purpose and scope

This specification defines the technical architecture of the Arithmetic Feedback Screen application. It confirms that the chosen file structure — one HTML file, one CSS file, one JavaScript file, and an assets folder — is sufficient to deliver every stated requirement without structural gaps. It names each file's responsibility, the interfaces between files (DOM element IDs, CSS class contracts, JavaScript function signatures), and the browser-native capabilities that carry the animation requirement. It is the contract from which Stage designs are drawn. Nothing outside this specification's boundary — server infrastructure, build tooling, external libraries, or multi-page routing — is in scope.

---

## Specification

### 1. File structure and responsibilities

The application is four artefacts in a flat directory. No file may take on a responsibility assigned to another.

| File | Responsibility |
|---|---|
| `index.html` | Document structure only. Declares all DOM elements by their specified IDs. Links `style.css` and `main.js`. Contains no inline styles and no inline script. |
| `style.css` | All visual presentation: layout, typography, colour, spacing, and all CSS animations and keyframes for feedback visuals. Defines all state classes. No logic. |
| `main.js` | All application logic: number generation, correct-answer computation, DOM query caching, event listener attachment, evaluation, feedback triggering, and reset sequencing. No presentation decisions except toggling the CSS classes defined in `style.css`. |
| `assets/` | Binary or raster assets that cannot be expressed in CSS or JavaScript alone (e.g. a GIF or image file for the sad dog visual). If an asset can be replaced by a CSS animation, it is not added. |

### 2. DOM element contracts

Every element below must exist in `index.html` with exactly the ID or class specified. No builder may rename these; they are the interface between `index.html`, `style.css`, and `main.js`.

| Element | Type | ID | Purpose |
|---|---|---|---|
| Number display — operand A | `<span>` or `<div>` | `number-a` | Displays the first operand. Content set by `main.js`. |
| Number display — operand B | `<span>` or `<div>` | `number-b` | Displays the second operand. Content set by `main.js`. |
| Answer field | `<input type="text">` | `answer-field` | Accepts the user's typed answer. Value read by `main.js` on button press. |
| Answer button | `<button>` | `answer-button` | Labelled "Answer". Pressing it triggers evaluation. |
| Feedback layer | `<div>` | `feedback-layer` | Contains both feedback sub-layers. Hidden by default via `is-hidden` class. |
| Celebration sub-layer | `<div>` | `celebration-visual` | Shown when the answer is correct. Hidden by default. |
| Sad dog sub-layer | `<div>` | `sad-dog-visual` | Shown when the answer is wrong. Hidden by default. |

**Rules:**
- All IDs are lowercase and hyphen-separated as shown.
- No element may carry an `onclick` or other inline event attribute.
- No element may carry a `style` attribute except where `main.js` sets it dynamically and no CSS-class alternative exists (this exception is expected to be unused).

### 3. CSS class contracts

These classes are the interface between `style.css` and `main.js`. `main.js` adds and removes them; `style.css` defines what they mean visually. Neither file may invent a class the other does not know about.

| Class | Applied to | Meaning |
|---|---|---|
| `is-hidden` | `#feedback-layer`, `#celebration-visual`, `#sad-dog-visual` | Element is not visible and takes no space (or is visually hidden). Default state for all feedback elements. |
| `is-correct` | `#feedback-layer` | The most recent evaluation result was correct. Triggers celebration presentation. |
| `is-wrong` | `#feedback-layer` | The most recent evaluation result was wrong. Triggers sad dog presentation. |

**Rules:**
- `is-hidden` is the default state. `main.js` removes it to reveal a sub-layer and restores it on reset.
- `is-correct` and `is-wrong` are mutually exclusive. `main.js` must never apply both simultaneously.
- CSS animations for celebration and sad dog are triggered by the presence of `is-correct` and `is-wrong` respectively, using CSS selectors (e.g. `#feedback-layer.is-correct #celebration-visual`). No JavaScript animation API is required unless CSS animation is demonstrably insufficient.

### 4. JavaScript function contracts

These are the public-facing functions of `main.js`. Their signatures are fixed; their internal implementation is the Stage designer's domain.

| Function | Signature | Responsibility |
|---|---|---|
| `generateNumbers` | `generateNumbers() → void` | Generates two random integers (operands), stores them in module-level constants, writes them to `#number-a` and `#number-b`, and computes and stores the correct answer. |
| `checkAnswer` | `checkAnswer() → void` | Reads the value of `#answer-field`. Parses it as an integer. Compares it to the stored correct answer. Calls `showFeedback('correct')` or `showFeedback('wrong')` accordingly. Treats an empty or non-numeric input as wrong. |
| `showFeedback` | `showFeedback(result: 'correct' \| 'wrong') → void` | Removes `is-hidden` from `#feedback-layer` and the appropriate sub-layer. Applies `is-correct` or `is-wrong` to `#feedback-layer`. Ensures the other sub-layer remains hidden. |
| `resetScreen` | `resetScreen() → void` | Clears `#answer-field`. Removes `is-correct`, `is-wrong` from `#feedback-layer`. Restores `is-hidden` to `#feedback-layer` and both sub-layers. Calls `generateNumbers()` to present a new problem. |

**Rules:**
- All DOM queries (`document.getElementById` etc.) are performed once at load and stored in named `const` variables at the top of `main.js`. No repeated DOM queries inside event handlers or the functions above.
- The event listener for `#answer-button` calls `checkAnswer`. It is attached in `main.js` at load, not in HTML.
- The trigger for `resetScreen` is defined by the Stage design. Acceptable triggers include: a dedicated reset button (if added within scope), automatic reset after a delay following feedback, or user interaction with the feedback layer. The trigger mechanism must be specified at Stage level and must not require a page reload.
- `var` is never used. `const` is default; `let` when reassignment is required.

### 5. Module-level state

The following variables must exist at module scope in `main.js`. They are the only permitted global state.

| Variable | Type | Purpose | Lifecycle |
|---|---|---|---|
| `correctAnswer` | `number` | The sum of the two current operands. Set by `generateNumbers`. Read by `checkAnswer`. | Lives for one interaction cycle. Overwritten by the next call to `generateNumbers`. |
| DOM element references | `const` (one per element) | Cached references to each DOM element listed in §2. Set once at load. | Persist for the page lifetime. Never reassigned. |

No other module-level state is permitted. No value is written to `localStorage`, `sessionStorage`, cookies, or any other persistence mechanism.

### 6. Number generation

- Both operands are integers.
- The range of generated numbers is not fixed by this specification; it is a Stage-level design decision. The range must be stated explicitly in the Stage design so that both Units build against the same expectation.
- The correct answer is always the arithmetic sum of the two operands (addition, confirmed). It is computed once per interaction cycle by `generateNumbers` and stored in `correctAnswer`.
- The application generates the numbers; the user does not enter them.

### 7. Evaluation logic

- Evaluation is triggered solely by pressing `#answer-button`.
- The user's input is read from `#answer-field` as a string and parsed to an integer using `parseInt` (base 10) or equivalent.
- If the parsed value equals `correctAnswer` (strict integer equality), the result is `'correct'`.
- If the parsed value does not equal `correctAnswer`, or if the input is empty, or if parsing yields `NaN`, the result is `'wrong'`.
- No partial credit, no "close" state, no tolerance band.

### 8. Feedback visuals

- Celebration visuals are animated, not static. The animation is delivered by CSS keyframes or the Web Animations API. No external library or CDN asset is used.
- Sad dog visuals are animated or may use an image asset from `assets/` if animation alone is insufficient to represent a sad dog. If an image is used, it must be a local file in `assets/`; no remote URL is permitted.
- Both feedback types are mutually exclusive: only one is visible at any time.
- The feedback layer is hidden by default and revealed only after evaluation.
- The feedback layer returns to hidden on reset.

### 9. Reset behaviour

- After feedback plays, the screen must return to a ready state without a page reload.
- Ready state is defined as: `#answer-field` is empty; `#feedback-layer` carries `is-hidden` and neither `is-correct` nor `is-wrong`; `#celebration-visual` and `#sad-dog-visual` both carry `is-hidden`; `#number-a` and `#number-b` display a new pair of operands; `correctAnswer` holds the sum of the new operands.
- The exact trigger for reset (delay, button, interaction) is a Stage-level design decision and must be specified there.

### 10. Browser compatibility and runtime

- The application runs directly from the file system via the `file://` protocol.
- No server, no build step, no installed software is required.
- Target browsers: current evergreen versions of Chrome, Firefox, Safari, and Edge.
- No external scripts, stylesheets, or assets are loaded from a URL. All resources are local.
- The application must produce zero console errors and zero console warnings on load and after any button press under all six named test cases.

### 11. Interface between Unit 1 and Unit 2

Unit 1 (Screen and Interaction) produces the stable foundation Unit 2 (Feedback Visuals) builds on. The handoff contract is:

- All DOM elements in §2 exist with the specified IDs.
- All CSS classes in §3 are defined in `style.css` with their specified semantics.
- `showFeedback('correct')` and `showFeedback('wrong')` are implemented and callable.
- `resetScreen()` is implemented and callable.
- The `#feedback-layer`, `#celebration-visual`, and `#sad-dog-visual` elements are present and carry `is-hidden` by default.

Unit 2 does not modify the evaluation logic, the number generation, or the DOM structure of Unit 1. Unit 2 adds visual content inside `#celebration-visual` and `#sad-dog-visual`, and defines the CSS animations triggered by `is-correct` and `is-wrong`. Unit 2 may add assets to `assets/`. Unit 2 does not introduce new DOM IDs or CSS state classes beyond those defined in §2 and §3 without a raise.

---

## Acceptance criteria

1. A single `index.html` file exists and is the sole entry point. Opening it in a current evergreen browser presents the complete application with no server and no build step.
2. `index.html` contains elements with IDs `number-a`, `number-b`, `answer-field`, `answer-button`, `feedback-layer`, `celebration-visual`, and `sad-dog-visual`, each of the type specified in §2.
3. `index.html` contains no inline styles and no inline event handlers.
4. `style.css` defines the classes `is-hidden`, `is-correct`, and `is-wrong` with the semantics specified in §3.
5. `main.js` implements `generateNumbers`, `checkAnswer`, `showFeedback`, and `resetScreen` with the signatures and responsibilities specified in §4.
6. All DOM queries in `main.js` are performed once at load and stored in named constants; no DOM query appears inside an event handler or the four named functions.
7. No module-level variable exists in `main.js` beyond `correctAnswer` and the cached DOM element references.
8. No value is written to `localStorage`, `sessionStorage`, cookies, or any persistence mechanism.
9. `is-correct` and `is-wrong` are never simultaneously present on `#feedback-layer`.
10. An empty or non-numeric input to `#answer-field` is treated as wrong; no exception is thrown and no console error is produced.
11. The feedback layer is hidden by default and hidden again after reset; it is visible only between evaluation and reset.
12. Celebration visuals are animated (not static) and delivered without an external library or remote asset.
13. The screen returns to ready state after feedback without a page reload.
14. The browser console is clean — zero errors, zero warnings — on load and after button press under all six named test cases.
15. No external script, stylesheet, or asset is loaded from a URL.
16. No `var` declaration, no `console.log`, and no inline event handler appears in any submitted file.

---

## Stages, proposed

**Stage 1 — Structural and logic design**
Design the complete HTML skeleton (all elements, IDs, and structural relationships), the CSS layout and state-class definitions (excluding feedback animation detail), and the full JavaScript module design (variable declarations, function bodies, event wiring, reset trigger mechanism, and number generation range). This Stage produces the detailed design Unit 1 builds from and the stable DOM and class contract Unit 2 depends on.

**Stage 2 — Feedback visual design**
Design the celebration visual and sad dog visual in full: the animation approach for each (CSS keyframes, Web Animations API, or asset-backed), the asset list for `assets/` if any, the CSS selectors and keyframe definitions that activate on `is-correct` and `is-wrong`, and the timing and sequencing of each animation. This Stage produces the detailed design Unit 2 builds from.

---

## Dependencies

- **Data Architecture Phase** — this specification assumes that no value persists beyond the current interaction cycle and that no value is written to any storage mechanism. The Data Architecture Phase must confirm this is complete and correct. If Data Architecture identifies a runtime value not covered by §5 of this specification, this specification is amended before Stage designs begin.
- **Discovery Phase** — this specification assumes that browser-native CSS animation or the Web Animations API is sufficient for both feedback visuals without an external library. Discovery Phase must confirm this assumption. If Discovery finds it cannot be confirmed, the feedback visual design in Stage 2 is held until the Conductor resolves it.
- **Requirements Phase** — the six named test cases and the evaluation logic (§7) in this specification are drawn from the instructions and the brief. Requirements Phase must confirm that no testable requirement is absent from or contradicted by this specification. Any gap found by Requirements Phase is resolved before Stage designs begin.
- No dependency on any external system, service, or API exists. The application is self-contained.
