# Enterprise Architecture — Arithmetic Feedback Screen

---

## Context

### The system and its boundaries

The application is a single-screen arithmetic feedback tool. It runs entirely in a web browser, loaded from the local file system via the `file://` protocol. There is no server, no network dependency, no backend, and no external service of any kind. The browser is the only runtime.

**Actors:**
- **User** — a person (child, teacher, or adult) who reads two numbers on screen, types an answer, and presses the Answer button. The user has no configuration role; the screen is ready on load.
- **Browser** — the execution environment. It renders HTML, applies CSS, runs JavaScript, and provides the DOM, the Web Animations API, and CSS animation support. No browser extension or plugin is assumed or required.

**System boundary:**
Everything inside the boundary is owned by this project: `index.html`, `style.css`, `main.js`, and the `assets/` folder. Everything outside the boundary — the browser engine, the operating system, the file system — is a dependency that is used but not controlled. No part of the application communicates across this boundary at runtime; there are no HTTP requests, no storage writes, no postMessage calls, and no external script loads.

**What is explicitly out of scope:**
- Any server or backend process.
- Any external library, framework, or CDN resource.
- Any form of persistence (localStorage, sessionStorage, cookies, IndexedDB).
- Any second screen, page, or route.
- Any user-configurable arithmetic operation or number entry.

---

## Architecture

### File responsibilities

The application is composed of exactly four artefacts. Each has a single, non-overlapping responsibility.

---

#### `index.html`

**Responsibility:** Structure and wiring only. Declares the DOM elements the user sees and interacts with. Links `style.css` and `main.js`. Contains no logic and no inline styles.

**Elements it must declare, with their required IDs:**

| Element | ID | Role |
|---|---|---|
| Number display — first operand | `operand-one` | Displays the first generated number |
| Number display — second operand | `operand-two` | Displays the second generated number |
| Answer input field | `answer-field` | Accepts the user's typed answer |
| Answer button | `answer-button` | The sole interaction trigger |
| Feedback layer (container) | `feedback-layer` | Wraps both feedback states; hidden by default |
| Celebration visual container | `feedback-celebration` | Child of `feedback-layer`; holds celebration content |
| Sad dog visual container | `feedback-sad` | Child of `feedback-layer`; holds sad dog content |

**Rules:**
- No `onclick`, `onchange`, or any other inline event handler attribute is used.
- No `style="..."` attribute is written in HTML except where JavaScript must set a value dynamically and no CSS-class alternative exists.
- The `<script src="main.js">` tag is placed at the end of `<body>` so the DOM is fully parsed before JavaScript runs.
- The `<link rel="stylesheet" href="style.css">` tag is placed in `<head>`.

---

#### `style.css`

**Responsibility:** All visual presentation. Layout, typography, colour, spacing, and animation keyframes. Defines the CSS class contracts that JavaScript uses to change visual state.

**CSS class contracts (state classes JavaScript may add or remove):**

| Class | Applied to | Meaning |
|---|---|---|
| `is-hidden` | `#feedback-layer`, `#feedback-celebration`, `#feedback-sad` | Element is not visible and takes no space (or is visually hidden); default state for feedback elements |
| `is-correct` | `#feedback-layer` | Correct answer was given; celebration content is shown |
| `is-wrong` | `#feedback-layer` | Wrong answer was given; sad dog content is shown |
| `is-animating` | `#feedback-celebration`, `#feedback-sad` | Animation is actively playing; triggers CSS `@keyframes` |

**Rules:**
- All animation is expressed as CSS `@keyframes` rules triggered by class application. JavaScript does not write animation properties directly.
- The Web Animations API (`element.animate(...)`) is an acceptable alternative to class-toggled keyframes, but the choice must be consistent within a single feedback type — not mixed.
- No style is duplicated between `style.css` and `main.js`. The single source of truth for any visual property is `style.css`.

---

#### `main.js`

**Responsibility:** All application logic. Number generation, DOM wiring, evaluation, feedback triggering, and reset. No presentation logic lives here except the application of CSS state classes.

**Function signatures (the contract every builder works to):**

```
generateNumbers() → void
```
Generates two random integers (range to be specified by Requirements Phase). Writes them to `#operand-one` and `#operand-two`. Stores the correct answer in the module-level variable `correctAnswer`. Called on page load and on reset.

```
checkAnswer() → void
```
Event handler for the Answer button's `click` event. Reads the value of `#answer-field`. Parses it as an integer. Compares it to `correctAnswer`. Calls `showCelebration()` if correct, `showSadDog()` if wrong or empty/invalid.

```
showCelebration() → void
```
Removes `is-hidden` and `is-wrong` from `#feedback-layer`. Adds `is-correct` to `#feedback-layer`. Adds `is-animating` to `#feedback-celebration`. Schedules `resetScreen()` after the animation completes (duration defined in `style.css`; the JavaScript value must match).

```
showSadDog() → void
```
Removes `is-hidden` and `is-correct` from `#feedback-layer`. Adds `is-wrong` to `#feedback-layer`. Adds `is-animating` to `#feedback-sad`. Schedules `resetScreen()` after the animation completes.

```
resetScreen() → void
```
Clears `#answer-field`. Removes `is-correct`, `is-wrong`, `is-animating` from all feedback elements. Adds `is-hidden` to `#feedback-layer`. Calls `generateNumbers()` to produce a new problem. Returns the screen to its initial ready state.

**Module-level variables (the only globals permitted):**

| Variable | Type | Purpose |
|---|---|---|
| `correctAnswer` | `number` | The arithmetic result for the current problem; persists across the event handler call; reset by `generateNumbers()` |

All DOM element references are queried once at load and stored as `const` at the top of `main.js`. No `document.querySelector` call is made inside an event handler or inside any of the functions above.

**Event binding:**
- One `click` event listener is attached to `#answer-button`, bound to `checkAnswer`.
- No other event listeners are required by this architecture. Requirements Phase may add (for example, a keyboard `Enter` handler on `#answer-field`), but must do so within `main.js` and must not alter the function signatures above.

---

#### `assets/`

**Responsibility:** Binary or media files that cannot be expressed in CSS or JavaScript alone.

**Rules:**
- If the celebration visual and sad dog visual can be fully expressed as CSS animations (keyframe-driven shape, colour, or motion effects), the `assets/` folder remains empty and is not committed.
- If a sad dog image (GIF, PNG, SVG) is required and cannot be replicated in CSS, it is placed here. The file is referenced by a relative path from `index.html` or `style.css`; no absolute path or URL is used.
- No file in `assets/` is loaded from a remote URL. All assets are local.
- The decision of whether assets are needed is made by the Unit 2 builder and confirmed in the Unit 2 Stage design. This architecture does not mandate an asset; it provides the folder as the correct location if one is needed.

---

### Interfaces between parts

The three files communicate through two interfaces only. No other coupling exists.

**Interface 1 — DOM (HTML ↔ CSS ↔ JavaScript)**

The DOM is the shared data structure. `index.html` declares elements with stable IDs. `style.css` targets those IDs and the state classes. `main.js` queries those IDs once and applies or removes state classes. The contract is: **the IDs and class names listed in this document are fixed**. No builder may rename them without a change to this architecture.

**Interface 2 — Evaluation result (Unit 1 → Unit 2)**

The evaluation result is carried as a CSS class on `#feedback-layer`. When `checkAnswer()` determines the answer is correct, it adds `is-correct` to `#feedback-layer`. When wrong, it adds `is-wrong`. Unit 2's feedback visuals are triggered by the presence of these classes (either via CSS selectors or via JavaScript reading the class list). This is the handoff point between Unit 1 and Unit 2. Unit 2 must not re-implement evaluation logic; it consumes the class state that Unit 1 produces.

---

### Browser-native capabilities used

| Capability | Used for | Standard |
|---|---|---|
| DOM API (`getElementById`, `classList`, `value`) | Element access and state management | Living Standard |
| CSS `@keyframes` and `animation` | Feedback animation | CSS Animations Level 1 |
| Web Animations API (`element.animate`) | Optional alternative for feedback animation | Web Animations |
| `parseInt` / `Number` | Parsing user input | ES2020 |
| `Math.random`, `Math.floor` | Number generation | ES2020 |
| `setTimeout` or `animationend` event | Scheduling reset after animation | DOM Events |

No capability outside this list is introduced without a raise to the Conductor.

---

## Qualities

### Performance

**Target:** The feedback visual must begin playing with no perceptible delay after the Answer button is pressed. "No perceptible delay" means the visual change is initiated within the same event loop tick as the button press — no asynchronous fetch, no deferred load, no layout recalculation that blocks the response.

**How the architecture meets it:** All DOM references are resolved at load time. Class application is synchronous. CSS animations begin on the next paint frame after class application, which is browser-native and not subject to JavaScript scheduling delays. No asset is loaded on demand; if assets are used, they are present in the DOM or pre-loaded at page load.

### Security

**Target:** The application must not execute untrusted code, make network requests, or write to any storage.

**How the architecture meets it:** There is no `eval`, no `innerHTML` assignment from user input, no dynamic script loading, and no `fetch` or `XMLHttpRequest`. User input is read as a string and parsed as an integer; it is never inserted into the DOM as HTML. The `file://` protocol provides no origin for cross-origin requests. There is nothing to attack and no attack surface to protect.

### Availability

**Target:** The application must be available whenever the browser can open a local file. There is no uptime requirement beyond "the file exists on disk."

**How the architecture meets it:** No network dependency means no network failure mode. The application is fully available offline by definition.

### Scalability

**Target:** Not applicable. The application serves one user at one screen. There is no concurrency, no load, and no scaling dimension.

### Accessibility (minimum bar)

The architecture does not specify an accessibility standard, but the structure must not actively prevent basic use. `#answer-field` must be a native `<input type="number">` or `<input type="text">` element (keyboard-focusable by default). `#answer-button` must be a native `<button>` element (keyboard-activatable by default). No ARIA roles are required by this architecture; Requirements Phase may add them if needed.

---

## Decisions

### Decision 1 — Single HTML file, no routing

**Options considered:**
1. Single `index.html` with all states managed in-place by CSS class toggling.
2. Multiple HTML files with navigation between states.

**Decision:** Option 1.

**Reason:** The brief specifies a single screen. The overview's constraint is explicit: "No routing, no pages, no navigation." A single file with class-toggled state is the direct implementation of that constraint. Multiple files would introduce navigation, which the brief forbids and the principles penalise (One interaction, no friction).

---

### Decision 2 — CSS class toggling as the state mechanism

**Options considered:**
1. JavaScript directly sets `style` properties (e.g., `element.style.display = 'none'`).
2. JavaScript adds and removes CSS classes; `style.css` defines what those classes mean.
3. JavaScript uses the Web Animations API exclusively, with no CSS class involvement.

**Decision:** Option 2, with Option 3 permitted as an implementation detail within `showCelebration()` and `showSadDog()` only.

**Reason:** CSS classes keep the single source of truth for visual properties in `style.css`. This means a builder working on Unit 2's visuals does not need to read `main.js` to understand what the screen looks like in each state. The interface between logic and presentation is the class name, which is stable and named in this document. Option 1 scatters presentation into JavaScript, violating the file responsibility boundary. Option 3 alone would require JavaScript to own animation timing and easing, which belongs in CSS.

---

### Decision 3 — DOM queries performed once at load

**Options considered:**
1. Query the DOM once at load; store references as `const` at module level.
2. Query the DOM inside each function that needs an element.

**Decision:** Option 1.

**Reason:** Repeated `document.querySelector` calls inside event handlers are a performance anti-pattern and a source of subtle bugs (if an element is renamed, the failure is silent until the handler fires). Querying once at load fails immediately and visibly if an element is missing. This is consistent with the coding conventions in the instructions.

---

### Decision 4 — Reset triggered by animation completion, not by a separate user action

**Options considered:**
1. A "Play again" button appears after feedback; the user presses it to reset.
2. The screen resets automatically after the feedback animation completes.
3. The screen resets when the user clicks anywhere after feedback plays.

**Decision:** Option 2.

**Reason:** The brief and overview describe a linear, repeatable flow with no additional user action required between interactions. A "Play again" button adds a navigation step (One interaction, no friction). Clicking anywhere is ambiguous and not described in the brief. Automatic reset after animation is the simplest implementation of "repeatable without reloading." The reset delay is the animation duration, which is defined in `style.css` and matched in `main.js` — this coupling is named explicitly so both Unit builders know it exists.

---

### Decision 5 — No external dependencies

**Options considered:**
1. Use a CSS animation library (e.g., Animate.css) for feedback effects.
2. Use a JavaScript animation library (e.g., GSAP) for richer motion.
3. Use only browser-native CSS animations and the Web Animations API.

**Decision:** Option 3.

**Reason:** The principles and instructions are explicit: no external dependency is introduced unless a role can show native capability is insufficient. Browser-native CSS `@keyframes` and the Web Animations API are sufficient for celebration and sad dog animations at the quality level this project requires. The instructions state: "If an asset can be replaced by a CSS animation or a browser-native API, the file is not added." The same logic applies to libraries.

---

## Open questions

The following questions must be settled by Initiation's later Phases before any builder opens a Work Package. Each is a gap this architecture cannot close alone.

**OQ-1 (for Discovery):** Is the arithmetic operation confirmed as addition? This architecture assumes addition in the `checkAnswer()` function signature and the `correctAnswer` variable. If the operation is different, the evaluation logic and the number display label change. Discovery must confirm or raise this before Requirements closes.

**OQ-2 (for Requirements):** What is the valid range for the two generated numbers? `generateNumbers()` must produce numbers within a defined range. The architecture does not specify the range; Requirements must state it as a testable constraint (e.g., integers between 1 and 10 inclusive). Without this, builders will choose independently and produce inconsistent behaviour.

**OQ-3 (for Requirements):** What is the exact behaviour when `#answer-field` is empty on button press? The instructions permit either "treat as wrong" or "do nothing." The architecture's `checkAnswer()` function must implement one behaviour consistently. Requirements must specify which.

**OQ-4 (for Requirements):** What is the animation duration for each feedback type, and does it differ between celebration and sad dog? The reset timing in `main.js` must match the animation duration in `style.css`. Requirements or Specification must name a duration (in milliseconds) so both Unit builders use the same value.

**OQ-5 (for Discovery):** Can the celebration visual and sad dog visual be fully expressed in CSS animations, or is at least one of them dependent on an image asset (GIF, PNG, SVG)? If an asset is required, the `assets/` folder is non-empty and Unit 2's scope includes sourcing or creating that asset. Discovery must confirm whether browser-native animation is sufficient for both feedback types, as named in the Initiation Brief's Risk 1.
