# Data Architecture — Arithmetic Feedback Screen

---

## Domain model

The application holds no persistent entities and no relational structure. At runtime it operates on four values, each of which exists only for the duration of one interaction cycle. These values are:

**Operand A** — the first number shown to the user. It is one half of the arithmetic problem. It has no identity beyond the current cycle; when the cycle resets, the value is replaced.

**Operand B** — the second number shown to the user. It is the other half of the arithmetic problem. Same lifecycle as Operand A.

**Correct Answer** — the arithmetic result the application expects. It is derived from Operand A and Operand B by addition (per the overview assumption) at the moment the two operands are set. It is not entered by the user; it is computed and held by the application. It exists so that evaluation can be performed without recomputing at button-press time.

**User Input** — the value the user types into the answer field. It is a string at the point of capture and is compared to the Correct Answer after numeric coercion. It has no meaning outside the evaluation event; it is not stored after evaluation completes.

These four values are the complete data universe of the application. There are no other entities. There are no relationships between cycles; each cycle is independent.

---

## Storage

| Value | Storage location | Form at rest | Lifecycle | Reset behaviour |
|---|---|---|---|---|
| Operand A | JavaScript `const` variable (`operandA`) declared at module scope in `main.js`; also written to the DOM element with id `operand-a` for display | Number (integer) in JS; string in DOM | Set when a new cycle begins; lives until the next cycle begins | Overwritten with a new generated value; DOM element text is replaced |
| Operand B | JavaScript `const` variable (`operandB`) declared at module scope in `main.js`; also written to the DOM element with id `operand-b` for display | Number (integer) in JS; string in DOM | Set when a new cycle begins; lives until the next cycle begins | Overwritten with a new generated value; DOM element text is replaced |
| Correct Answer | JavaScript `let` variable (`correctAnswer`) declared at module scope in `main.js` | Number (integer) | Computed from Operand A + Operand B when a new cycle begins; lives until the next cycle begins | Overwritten with the sum of the new operands |
| User Input | DOM element value of the answer field (`answer-field`); read once at evaluation time into a local constant inside the evaluation function | String (DOM); Number (coerced in JS, local scope only) | Exists in the DOM from the moment the user types; the local coerced copy exists only for the duration of the evaluation function call | DOM element value is cleared (set to empty string) when the cycle resets |

**No value is written to `localStorage`, `sessionStorage`, `IndexedDB`, cookies, or any other browser storage mechanism.** No value is sent over a network. No value survives a page reload. The application has no backend and no data layer beneath the browser's JavaScript runtime.

The module-scope variables (`operandA`, `operandB`, `correctAnswer`) are the only values that persist across function calls within a single session. They persist because evaluation must be able to compare the user's input to the correct answer without regenerating the problem. They are declared at the top of `main.js` with a comment stating this reason, per the coding conventions.

---

## Integrity and legality

**Rule 1 — Operands are set by the application, never by the user.**
The user has no mechanism to write to `operandA`, `operandB`, or `correctAnswer`. These variables are set only by the number-generation function. No input element, event handler, or URL parameter may alter them. This rule holds because the overview states that the application generates the numbers; user-entered operands are explicitly out of scope.

**Rule 2 — Correct Answer is always the sum of the current operands.**
`correctAnswer` must equal `operandA + operandB` at all times during an active cycle. It is set immediately after the operands are set, in the same function call, with no intervening state. No other function may write to `correctAnswer`. This rule holds because evaluation correctness depends on it; a stale or independently set `correctAnswer` would produce wrong-condition feedback, which the principles and quality standards name as never acceptable.

**Rule 3 — User Input is coerced to a number before comparison.**
The answer field yields a string. Before comparison to `correctAnswer`, the value is coerced to a number using `Number()` or `parseInt()` (to be decided by the builder, but the coercion must be explicit and consistent). An empty string coerces to `0` or `NaN`; both cases must be handled explicitly. `NaN` is not equal to any number and must be treated as a wrong answer, not as an error. This rule holds because the empty-field test case requires the application not to crash and not to produce a blank state.

**Rule 4 — The reset cycle must leave all four values in a defined state.**
After feedback plays and the cycle resets, the state must be: new `operandA` and `operandB` set, new `correctAnswer` computed, `answer-field` value cleared. No value may be left in an intermediate or undefined state. The feedback layer must be returned to its hidden state. This rule holds because the Repeatability test case requires the screen to be ready for the next interaction without a page reload.

**Rule 5 — No value persists beyond the current session.**
No write to any browser storage API is legal. This rule holds because the overview and principles name persistence of any kind as never acceptable, and the brief contains no requirement for scores, history, or continuity.

**Who may change what:**
- The number-generation function is the only writer to `operandA`, `operandB`, and `correctAnswer`.
- The user is the only writer to the `answer-field` DOM value, and only through the keyboard.
- The evaluation function is the only reader of `correctAnswer` and `answer-field` value for comparison purposes.
- The reset function is the only function that clears `answer-field` and triggers a new number-generation cycle.

---

## Tenancy and access

This application has a single user and a single session. There is no authentication, no authorisation, no multi-tenancy, and no concept of a user account. All data is local to the browser tab in which the application runs.

**Visibility:** All four runtime values are visible to anyone who opens the browser's developer tools. `correctAnswer` is held in a JavaScript variable and is inspectable via the console. This is accepted: the application is a learning aid, not a secure assessment. There is no requirement to conceal the correct answer from a determined user, and obfuscating it would add complexity without serving the brief.

**Isolation:** Each browser tab running the application is fully isolated from every other. There is no shared state, no shared storage, and no communication between tabs or sessions. This isolation is a consequence of the no-persistence rule and requires no enforcement mechanism beyond the absence of any cross-tab storage write.

---

## Platform

**Runtime:** Browser JavaScript engine (V8 in Chrome, SpiderMonkey in Firefox, JavaScriptCore in Safari, V8 in Edge). No specific version constraint beyond ES2020 compatibility, per the instructions.

**Storage platform:** None. The application uses no database, no file system write, no browser storage API. The only storage is the JavaScript heap (for module-scope variables) and the DOM (for displayed and input values). Both are managed by the browser and are discarded when the tab is closed or the page is reloaded.

**Data services:** None. No API calls, no network requests, no service workers, no caches.

**Environments:** One environment — the user's browser, opened directly from the file system via the `file://` protocol. There is no staging environment, no production server, and no environment-specific configuration.

---

## Migration and seed

There is no schema, no database, and no migration path. This section is complete as stated: the application has no persistent data layer and therefore no migration or seed concern.

The initial state of the application on load is produced by the number-generation function running once at page load. This is not a seed in the database sense; it is the application's startup behaviour and is owned by `main.js`. The data consequence is that `operandA`, `operandB`, and `correctAnswer` are defined before the user can interact with the screen, and `answer-field` is empty. This is the only "initial data" the application requires, and it is generated, not loaded.