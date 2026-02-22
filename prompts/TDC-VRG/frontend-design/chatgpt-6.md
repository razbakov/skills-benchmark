## Summary

This skill is a design-and-implementation guide for generating distinctive, production-grade frontend UI code (React/Vue/HTML/CSS/JS) with a strong aesthetic point of view and a mandate to avoid “generic AI” design patterns. It should activate when users ask for web UI components/pages/apps or want styling/beautification of a web interface. Key constraints are: pick a bold conceptual direction before coding, execute with meticulous aesthetic detail, and explicitly avoid common fonts/palettes/layout tropes associated with “AI slop.”

## CRN Scorecard

| dimension                         | score | 1-line rationale                                                                                                                                                                          |
| --------------------------------- | ----: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1) Triggering Precision           |     2 | Clear “when to use” list exists, but lacks “when NOT to use,” conflict boundaries, and disambiguation vs nearby skills.                                                                   |
| 2) Degrees of Freedom Calibration |     1 | High-level guidance is strong, but hard requirements (tech stack, libraries, accessibility expectations, delivery format) are under-specified and “when to ask vs proceed” isn’t defined. |
| 3) Context Economy                |     2 | Mostly scannable sections and bullets; some motivational/redundant prose and oversized aesthetic lists could be tightened.                                                                |
| 4) Verifiability                  |     0 | No explicit success criteria, acceptance checks, test steps, or “how to tell it worked” beyond subjective aesthetic goals.                                                                |
| 5) Reversibility / Safety         |     0 | No safety guardrails for code generation (e.g., avoiding insecure patterns), no rollback/undo guidance, no checks for risky behaviors.                                                    |
| 6) Generalizability               |     1 | Applies broadly to “frontend,” but overfits to “bold aesthetics” and includes model-specific/odd phrasing; lacks configurable assumptions and environment portability guidance.           |

## Detailed Findings

### 1) Triggering Precision (Score: 2)

**Evidence**

* YAML description: “Use this skill when the user asks to build web components, pages, artifacts, posters, or applications…”
* “The user provides frontend requirements: a component, page, application, or interface to build.”

**Risks / failure modes**

* Over-triggers on non-frontend “artifacts” (e.g., posters) where a design-only or image-generation workflow might be more appropriate.
* Under-specifies “do NOT use” cases (e.g., backend tasks, data pipelines, non-UI bug fixes), increasing conflicts with other skills (e.g., generic coding skill, branding/design brief skill).
* Could mis-activate on “beautify” requests that are actually about content strategy or copywriting rather than UI.

**Fixes (minimal edits to raise to 3)**

* Add a short **“Do NOT use when…”** block:

  * Not for backend/APIs, infra, data analysis, or non-UI code refactors.
  * Not for pure graphic design deliverables unless they are *implemented as web UI*.
* Add **disambiguation**: “If the user asks for a static poster/image, prefer image generation; if they want an HTML/CSS poster page, use this skill.”
* Add 2–3 trigger examples + 2 non-trigger examples.

---

### 2) Degrees of Freedom Calibration (Score: 1)

**Evidence**

* “Before coding, understand the context and commit to a BOLD aesthetic direction…”
* “Then implement working code (HTML/CSS/JS, React, Vue, etc.)…”

**Missing required information**

* No default stack or decision rule for choosing between React/Vue/HTML.
* No explicit output format requirements (single file vs multi-file, how to structure components, whether to include instructions to run).
* No rule for when to ask clarifying questions vs proceed with assumptions.
* No accessibility/performance/security defaults (beyond mentioning “Constraints” as a concept).

**Risks / failure modes**

* Inconsistent outputs (sometimes React, sometimes HTML) causing user friction.
* Excess complexity (heavy animations) when user only wanted a simple component.
* Accessibility regressions if animation and color choices ignore contrast/motion preferences.

**Fixes (minimal edits to raise to 2)**

* Add a **Defaults** subsection:

  * Default to React + Tailwind (or plain HTML/CSS) unless user specifies otherwise.
  * Default to “single self-contained component/file” unless user requests a project scaffold.
* Add an **Assumptions & Questions** rule:

  * If framework/constraints are missing, proceed with defaults and state assumptions in 2–3 bullets.
  * Ask only if missing info blocks correctness (e.g., required framework/version, must-match brand guidelines, deployment constraints).
* Add a small **Complexity dial**:

  * “Simple / Medium / Maximal” with default = Medium.

---

### 3) Context Economy (Score: 2)

**Evidence**

* Clear headings and bullets: “Design Thinking,” “Frontend Aesthetics Guidelines.”
* Some repeated emphasis: “BOLD aesthetic direction,” “NEVER use generic…,” repeated lists of styles/fonts.

**Risks / failure modes**

* Long lists increase scanning cost and may distract from actionable steps.
* Motivational text (“Remember: Claude…”) is irrelevant to execution and can confuse downstream reviewers/maintainers.
* Over-prescriptive bans on common fonts could be counterproductive in enterprise contexts.

**Fixes (minimal edits to raise to 3)**

* Trim or move “Tone” list to a short set + “etc.” (keep 6–8 exemplars, not ~12+).
* Remove model-referential sentence (“Remember: Claude…”) and replace with an execution-oriented reminder.
* Convert repeated “NEVER” blocks into one consolidated “Avoid” checklist.

---

### 4) Verifiability (Score: 0)

**Evidence**

* No sections describing acceptance criteria, tests, or observable outcomes.
* Requirements are subjective: “Visually striking and memorable,” “meticulously refined.”

**Risks / failure modes**

* Hard to evaluate success beyond taste; inconsistent results across runs.
* “Production-grade” claim is untestable without checks (linting, responsiveness, a11y, performance).
* Users may receive code that doesn’t run or lacks integration instructions.

**Fixes (minimal edits to raise to 1)**

* Add a **“Definition of Done”** checklist (observable):

  * Runs without errors; responsive at common breakpoints; keyboard navigable; basic contrast; reduced-motion support; no console errors.
* Add a **“Self-check”** step:

  * “Validate: no missing imports, animations respect prefers-reduced-motion, color contrast not obviously failing, layout doesn’t overflow.”
* Add a **“Deliverables”** bullet list:

  * Code + short run instructions + notes on assumptions.

---

### 5) Reversibility / Safety (Score: 0)

**Evidence**

* No mention of safe coding patterns, avoiding secrets, avoiding insecure JS, or rollbacks.
* Encourages “elaborate code with extensive animations and effects” without guardrails.

**Risks / failure modes**

* Introduces security issues (unsafe `dangerouslySetInnerHTML`, untrusted URL injection, inline scripts).
* Performance pitfalls (heavy animations, large assets) without opt-outs.
* No guidance on how to revert aesthetic changes or implement incrementally.

**Fixes (minimal edits to raise to 1)**

* Add a **Safety & Constraints** subsection:

  * Avoid `eval`, inline script injection, `dangerouslySetInnerHTML` unless explicitly requested with sanitization.
  * Don’t include secrets/keys; use placeholders.
  * Prefer incremental changes; keep changes isolated to styling/layout unless requested otherwise.
* Add a **Rollback note**:

  * “When refactoring styling, keep a minimal diff path: separate styling changes from logic changes; provide a ‘minimal theme’ toggle or CSS variables to revert quickly.”

---

### 6) Generalizability (Score: 1)

**Evidence**

* Broad applicability: “web components… pages… dashboards… React components, HTML/CSS layouts…”
* Strong aesthetic mandates: “Commit to a BOLD aesthetic direction,” “NEVER converge on common choices…”

**Risks / failure modes**

* Overfit to novelty; may conflict with corporate design systems where “distinctive” is constrained.
* Font bans are too absolute; sometimes Inter/system fonts are mandated for performance/localization.
* The “Claude” mention reduces portability and professionalism of the skill file.

**Fixes (minimal edits to raise to 2)**

* Add an **Assumptions** note:

  * “If a brand/design system is provided, it overrides the novelty mandate.”
* Replace absolute font bans with conditional guidance:

  * “Avoid defaulting to X unless the user’s constraints require it.”
* Add a short **Portability** note:

  * “Prefer standards-based CSS, progressive enhancement, and minimal dependencies unless specified.”

---

## Top 5 Issues (ranked)

1. **No verifiability / acceptance criteria** → “production-grade” is ungrounded; hard to test success.
2. **No safety guardrails** → risk of insecure/perf-hostile code patterns creeping in.
3. **Uncalibrated degrees of freedom** → inconsistent stack/output/complexity; unclear when to ask vs assume.
4. **Missing “do not use” boundaries** → over-triggering on non-frontend artifacts (posters/images) and conflict with other skills.
5. **Overly absolute aesthetic bans + model reference** → reduces generalizability and professionalism.

## Patch Plan (≤10 items)

1. Add **“Do NOT use when…”** + conflict resolution (static image vs web UI).
2. Add **Defaults** (stack, file structure, complexity level).
3. Add **Assumptions vs Questions** rule (when to ask).
4. Add **Deliverables** spec (code + run instructions + assumption notes).
5. Add **Definition of Done** checklist (responsive/a11y/no errors).
6. Add **Self-check** validation steps (imports, console errors, reduced motion, overflow).
7. Add **Safety & Security** guardrails (no secrets, avoid `eval`, sanitize HTML).
8. Add **Rollback/Reversibility** guidance (CSS variables, isolated diffs, theming).
9. Trim/condense long aesthetic lists; consolidate repeated “NEVER” guidance.
10. Remove/replace “Claude…” line with a neutral execution reminder.

## Targeted Rewrite (1–2 sections only)

### Rewrite 1: Add/replace a critical “When to Use / When Not to Use” section (near top)

```md
## When to Use

Use this skill when the user requests **implemented web UI** (code) such as:
- Web pages, landing pages, dashboards, apps
- UI components (React/Vue/Web Components), layouts, styling/beautification
- Interactive or animated interfaces that must run in the browser

**Do NOT use** this skill when:
- The request is primarily backend/API/infra/data work with no UI deliverable
- The user wants a **static image/poster** (no HTML/CSS implementation). If they want a poster *as a web page*, proceed with HTML/CSS.
- The user provides an existing design system/brand kit that must be followed and novelty is not desired (in that case, follow the system first).

If framework/stack is not specified, proceed with defaults (see below) and state assumptions briefly.
```

### Rewrite 2: Add a “Definition of Done + Safety” section (place after Aesthetics Guidelines)

```md
## Definition of Done (Verifiable)

A response is complete when:
- Code runs without errors (no missing imports / undefined variables) and has no obvious console errors
- Layout is responsive (mobile + desktop) without overflow issues
- Basic accessibility is respected: semantic HTML, keyboard focus visibility, and reasonable contrast
- Animations respect `prefers-reduced-motion` (provide reduced/disabled motion fallback)
- Styling uses variables/tokens where practical (easy to tweak/revert)

## Safety / Reversibility

- Do not include secrets or real API keys; use placeholders.
- Avoid unsafe patterns (`eval`, injecting untrusted HTML, `dangerouslySetInnerHTML`) unless explicitly required and paired with sanitization guidance.
- Keep logic changes separate from styling changes when “beautifying” existing UI. Prefer CSS variables/theme tokens so the user can revert quickly.
```

If you want, I can also propose a tiny “Defaults” subsection (stack + complexity dial) that fits the file’s current tone without expanding it much.
