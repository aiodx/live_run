## Repository and layout

The project is a single self-contained web application. All files live in one flat directory at the root of the repository. No subdirectories are created unless a role can show a specific need; this project is small enough that a flat layout is correct.

```
/
├── index.html        — the single screen; the whole application
├── style.css         — all visual styling
├── main.js           — all application logic
└── assets/           — any image or animation files used for feedback visuals
    └── (sad dog assets, celebration assets)
```

**Rules:**
- `index.html` is the entry point and the only HTML file. No second page is created.
- `style.css` holds all styles. No inline styles are written in HTML except where a style must be set dynamically by JavaScript (for example, toggling visibility or animation state).
- `main.js` holds all logic: number generation, evaluation, feedback triggering. No script tags with inline code are written in `index.html`.
- `assets/` holds only files that cannot be expressed in CSS or JavaScript alone — for example, a GIF or image file for the sad dog. If an asset can be replaced by a CSS animation or a browser-native API, the file is not added.
- Nothing is written outside these files and this folder. No build output, no generated files, no configuration files are committed unless a tool explicitly requires one.

---

## Toolchain

**Runtime:** Standard web browser. No installed software, no server, no build step is required to run the application. Opening `index.html` in a browser is sufficient.

**Languages:**
- HTML5
- CSS3 (including CSS animations and keyframes)
- JavaScript (ES2020 or later; no transpilation required — the target is a current evergreen browser)

**Package manager:** None. No `package.json`, no `node_modules`, no dependency installation. The application has no external dependencies. If a role believes a dependency is necessary, it raises to the Conductor before introducing one.

**How to build:** There is no build step. The application runs directly from the file system.

**How to run:** Open `index.html` in a current version of Chrome, Firefox, Safari, or Edge. The screen appears immediately. No server is needed; `file://` protocol is sufficient.

**How to verify locally:** Open the browser's developer console. There must be no errors on load and no errors on button press. The feedback visuals must play on both a correct and an incorrect answer.

---

## Testing

**Approach:** This project has no automated test runner. Testing is manual and structured. Every Work Package owner runs the checks below before submitting for review.

**Test cases every Work Package must pass:**

1. **Correct answer path** — Enter the correct sum of the two displayed numbers into the answer field. Press the Answer button. Celebration visuals must play. Sad dog visuals must not appear.
2. **Wrong answer path** — Enter an incorrect value into the answer field. Press the Answer button. Sad dog visuals must play. Celebration visuals must not appear.
3. **Empty field** — Press the Answer button with the answer field empty. The application must not crash. It must either treat the empty input as wrong (sad dog plays) or do nothing; it must not produce an error or a blank state.
4. **Repeatability** — After feedback plays on either path, the user must be able to answer again without reloading the page. The screen must reset to a ready state.
5. **No console errors** — The browser developer console must show zero errors at load and after any button press.
6. **No wrong-condition feedback** — Under no input does celebration play for a wrong answer or sad dog play for a correct answer.

**What a passing build means:** All six test cases above pass in at least one current evergreen browser (Chrome or Firefox). The console is clean. The feedback visuals are animated, not static.

**Named test cases:** When a Work Package owner documents their test run, they refer to the cases by the names above (Correct answer path, Wrong answer path, Empty field, Repeatability, No console errors, No wrong-condition feedback).

---

## Coding conventions

**Style:**
- Indentation: 2 spaces. No tabs.
- Strings: single quotes in JavaScript. Double quotes in HTML attributes.
- Semicolons: required in JavaScript.
- Line length: no hard limit, but lines over 100 characters should be broken for readability.
- Blank lines: one blank line between logical blocks; no trailing blank lines at end of file.

**Naming:**
- HTML element IDs and classes: lowercase, hyphen-separated (`answer-field`, `answer-button`, `feedback-layer`).
- JavaScript variables and functions: camelCase (`checkAnswer`, `displayNumbers`, `showCelebration`).
- CSS class names that represent state: prefixed with `is-` (`is-correct`, `is-wrong`, `is-hidden`).
- No single-letter variable names except loop counters.

**JavaScript rules:**
- `const` by default; `let` when reassignment is needed; `var` is never used.
- No global variables except those that must persist across function calls (for example, the current correct answer). These are declared at the top of `main.js` with a comment explaining why they are global.
- Event listeners are attached in `main.js`, not in HTML attributes (`onclick="..."` is not used).
- All DOM queries are performed once at load and stored in named constants; no repeated `document.querySelector` calls inside event handlers.

**Error handling:**
- The application must not throw an uncaught exception under any user input. Input that is not a valid number is handled explicitly (treated as wrong).
- No `try/catch` is used to silently swallow errors; if an error is caught, it is logged to the console with a descriptive message.

**Logging:**
- `console.log` is not used in production code. It may be used during development but must be removed before a Work Package is submitted for review.
- `console.error` is acceptable for genuine error conditions.

**Things a reviewer will reject:**
- Inline event handlers in HTML (`onclick`, `onchange`).
- `var` declarations.
- `console.log` statements left in submitted code.
- Styles written directly in HTML attributes (`style="..."`), except where JavaScript sets them dynamically and there is no CSS-class alternative.
- Any external script, stylesheet, or asset loaded from a URL (no CDN links, no remote resources).
- Code that produces a console error under any of the six named test cases.

---

## Definition of done for a Work Package

A Work Package is ready for its owner's review when every item on this checklist is met. The owner checks each item explicitly; a partial check is not a pass.

- [ ] All six named test cases pass (Correct answer path, Wrong answer path, Empty field, Repeatability, No console errors, No wrong-condition feedback).
- [ ] The browser developer console is clean — zero errors and zero warnings — on load and after button press in both the correct and wrong paths.
- [ ] No `console.log` statements remain in any file.
- [ ] No external dependencies have been introduced (no CDN links, no `npm install`, no remote assets).
- [ ] All files conform to the coding conventions above: indentation, naming, no `var`, no inline event handlers, no inline styles except where dynamically required.
- [ ] The feedback visuals are animated, not static images, unless the Conductor has formally revised the animation assumption.
- [ ] The screen is usable without instruction: a user who has not read any documentation can see the numbers, type an answer, press the button, and understand the response.
- [ ] The experience is repeatable without a page reload.
- [ ] No feature, element, or behaviour is present that is not described in the overview or directly required to deliver what is described. Any addition has been raised to and agreed by the Conductor before it was built.
- [ ] The Work Package owner has read the output against the principles — Feedback is the product; One interaction, no friction; Correct beats clever — and found no conflict.