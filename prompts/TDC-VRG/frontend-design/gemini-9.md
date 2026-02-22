## Summary

The `frontend-design` skill directs the agent to move beyond "AI-generic" UI by committing to bold, intentional aesthetic directions (e.g., maximalism, brutalism, retro-futurism). It activates when a user requests web components, layouts, or "beautification" of existing UI. The key constraint is the strict prohibition of "safe" defaults like Inter/system fonts and purple-on-white gradients in favor of high-impact, production-grade code.

---

## CRN Scorecard

| Dimension | Score | Rationale |
| --- | --- | --- |
| **Triggering Precision** | 2 | Good inclusion/exclusion examples, but lacks technical boundaries (e.g., backend tasks). |
| **Degrees of Freedom** | 1 | High creative freedom, but lacks "must-ask" checkpoints or framework defaults. |
| **Context Economy** | 3 | Lean, punchy, and highly scannable without unnecessary fluff. |
| **Verifiability** | 0 | **Missing required information.** No success criteria, tests, or validation steps. |
| **Reversibility / Safety** | 1 | No mention of accessibility, performance impacts, or destructive UI overwrites. |
| **Generalizability** | 2 | Highly adaptable across styles, though slightly biased toward "visual flair." |

---

## Detailed Findings

### 1. Triggering Precision

* **Evidence**: "Use this skill when the user asks to build web components... HTML/CSS layouts, or when styling/beautifying any web UI."
* **Risks/failure modes**: May trigger on pure logic/data-heavy requests (e.g., "build a sorting algorithm for a table") and prioritize "flair" over functional performance.
* **Fixes**:
* Add a "When NOT to use" section (e.g., purely functional backend logic or accessibility-critical low-bandwidth apps).
* Explicitly mention that it should not override existing brand guidelines unless requested.



### 2. Degrees of Freedom Calibration

* **Evidence**: "Pick an extreme... Use these for inspiration... Implement working code (HTML/CSS/JS, React, Vue, etc.)"
* **Risks/failure modes**: The agent might guess the framework (e.g., React) when the user needs Vanilla JS, or choose a "maximalist" style for a professional medical dashboard where it's inappropriate.
* **Fixes**:
* Add a instruction to confirm technical stack (Framework/Library) before generating code.
* Instruct the agent to present 2-3 style "keywords" for user approval if the prompt is ambiguous.



### 3. Context Economy

* **Evidence**: Use of bolding and bullet points; the "Design Thinking" section is concise.
* **Risks/failure modes**: Low risk. The file is efficient.
* **Fixes**: N/A.

### 4. Verifiability

* **Evidence**: **Missing required information.**
* **Risks/failure modes**: No way to verify if the code actually runs, is responsive, or meets the "production-grade" claim. Agent may generate "pretty" code that is broken.
* **Fixes**:
* Add a "Validation" section: (1) Check for syntax errors, (2) Verify responsiveness, (3) Ensure CSS variables are defined.
* Require a "Success Checklist" at the end of the output.



### 5. Reversibility / Safety

* **Evidence**: No mention of safety or constraints.
* **Risks/failure modes**: "Bold" designs often break accessibility (low contrast, motion sickness from animations). No mention of "dry-run" or modular implementation to prevent breaking existing user code.
* **Fixes**:
* Add a mandatory "Accessibility Check" (WCAG contrast).
* Include a "Safe Implementation" rule: "When editing existing files, use comments to mark changes and provide a rollback path."



### 6. Generalizability

* **Evidence**: "Vary between light and dark themes... No design should be the same."
* **Risks/failure modes**: Might over-index on "visual impact" for internal tools where density and speed matter more than "unforgettable" aesthetics.
* **Fixes**:
* Add a "Utility-First" exception for dashboard/data-dense requests.



---

## Top 5 Issues

1. **Zero Verifiability**: No instructions for the agent to test or validate code output.
2. **Safety/Accessibility Neglect**: "Bold" choices could lead to unusable, non-compliant UI.
3. **Missing Framework Check**: Potential to generate code in the wrong library (React vs. Vue).
4. **No "Ask vs. Do" Boundary**: Agent may execute a "maximalist chaos" design without user consent on a professional project.
5. **Scope Creep**: Skill may trigger on non-UI logic tasks within a frontend context.

---

## Patch Plan

1. [ ] **Add Verifiability**: Insert a "Validation & Testing" section.
2. [ ] **Add Safety Rails**: Include a mandate for WCAG color contrast and `prefers-reduced-motion`.
3. [ ] **Refine Triggering**: Explicitly state "Do not use for backend logic or CLI tools."
4. [ ] **Establish Checkpoints**: Require the agent to state the chosen "Aesthetic Direction" and "Tech Stack" before the code block.
5. [ ] **Performance Guardrails**: Add a note to optimize assets (e.g., image placeholders, CSS minification intent).

---

## Targeted Rewrite (Critical Sections)

### New: Validation & Safety (Missing Section)

> ## Validation & Safety
> 
> 
> Before delivering code, verify against these criteria:
> * **Syntax**: Ensure all tags/brackets are closed and imports are valid.
> * **Accessibility**: Contrast ratios must meet WCAG AA standards. Ensure `aria-labels` are present on interactive elements.
> * **Responsiveness**: UI must remain functional from 375px to 1440px width.
> * **Performance**: Prefer CSS for animations; avoid heavy third-party libraries unless requested.
> * **Idempotency**: Ensure styles are scoped (e.g., CSS Modules, Tailwind, or unique IDs) to avoid leaking into the user's global environment.
> 
> 

### Revised: Design Thinking (Calibration Edit)

> ## Design Thinking
> 
> 
> 1. **Identify Stack**: Confirm the framework (e.g., React, Tailwind, Plain HTML/CSS). If unknown, default to Tailwind/Vite but **ask** for complex apps.
> 2. **Establish Tone**: Pick a direction (e.g., Brutalist, Minimal). **If the user's intent is ambiguous, briefly state your chosen aesthetic before the code block.**
> 3. **Constraint Check**: Does this require high data density or high visual impact? Adjust complexity accordingly.
> 
> 

---

**Would you like me to generate a complete, updated version of the SKILL.md incorporating all these fixes?**