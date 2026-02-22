## Summary

This skill defines a documentation-first, test-driven (BDD/TDD) workflow for building application features, progressing through six phases from vision → architecture → data modeling → integration tests → unit tests → implementation. It should activate when users are starting new features or implementing functionality and wants a structured, stepwise process with “human-in-the-loop” confirmations. Key constraints include: proceed one step at a time, prefer single-file changes during phases 1–3, suggest the next step from `docs/next-steps.md`, and run tests against a test database from the workspace root.

---

## CRN Scorecard

| dimension                         | score | 1-line rationale                                                                                                                                                              |
| --------------------------------- | ----: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1) Triggering Precision           |     2 | Clear “use when starting new features” trigger + phased workflow, but weak “don’t use when” boundaries and potential overlap with generic TDD coaching.                       |
| 2) Degrees of Freedom Calibration |     2 | Strong ordering and step gating; some underspecified mechanics (what “one step” means, how to handle missing `docs/next-steps.md`, when to ask vs proceed).                   |
| 3) Context Economy                |     2 | Generally scannable with phases and bullets; some redundancy and mixed concerns (workflow + tech stack defaults + commit standards) could be tighter.                         |
| 4) Verifiability                  |     1 | Provides artifacts per phase and commands, but lacks explicit success criteria/checkpoints per phase and “how to tell it worked” beyond “run tests.”                          |
| 5) Reversibility / Safety         |     2 | Has key safeguards (test DB, human confirmations, avoid `cd`), but missing rollback guidance (git checkpoints, reversible edits) and protections around migrations/DB resets. |
| 6) Generalizability               |     2 | Phase structure is broadly applicable; tech defaults and Nuxt-specific assumptions reduce portability unless clearly optional/configurable.                                   |

---

## Detailed Findings

### 1) Triggering Precision (Score: 2)

**Evidence**

* Front matter: “**Use when starting new features, implementing functionality, or following structured development processes**.”
* Persona/behavior: “**Complete one step at a time… Wait for the user to confirm before proceeding**.”
* Phases: “**Follow these phases in order. Each phase produces artifacts that the next phase depends on.**”

**Risks / failure modes**

* Over-triggers on any dev question (e.g., debugging, refactors, ops incidents) because “implementing functionality” is broad.
* Under-defines “do not use” scenarios like quick fixes, exploratory spikes, legacy code constraints, or when the user explicitly wants a different workflow (e.g., prototyping without tests).
* Potential conflict with other skills that also “coach development,” since there’s no explicit disambiguation.

**Fixes (minimal edits to raise to 3)**

* Add an explicit **“Do not use when”** section near the top:

  * e.g., “bug triage only,” “one-off scripts,” “incident response,” “user asks for only a code snippet,” “timeboxed spike without tests.”
* Add **2–3 trigger examples** and **2–3 non-trigger examples**.
* Add a short **conflict-avoidance note**: if another workflow is mandated by repo/docs, follow that instead.

---

### 2) Degrees of Freedom Calibration (Score: 2)

**Evidence**

* “**Complete one step at a time… Wait for the user to confirm before proceeding**.”
* “**During the document-first phases (1–3), make a change in one file per step**.”
* “**Follow these phases in order**.”
* Tech defaults: “**Unless the project's README… specify otherwise… Confirm with the user before adopting them for a new project.**”
* Commands: “**Execute commands from the workspace root… Avoid `cd` prefixes**.”

**Risks / failure modes**

* “One step” is ambiguous: could mean “one commit,” “one file,” “one artifact,” or “one conversation turn,” leading to inconsistent behavior.
* “Suggest the next step from `docs/next-steps.md`” is brittle: what if the file doesn’t exist? The skill doesn’t specify fallback behavior.
* No guidance for when the user refuses confirmation gating (“just do it”) or wants multiple steps at once.

**Fixes (minimal edits to raise to 3)**

* Define **what counts as a “step”** (default: one artifact or one small change set; in phases 1–3 strictly one file).
* Add fallback: if `docs/next-steps.md` missing, **generate 3 next-step options** aligned to the current phase.
* Add a rule: if user asks to skip confirmations, still **bundle work into checkpoints** and ask before running destructive commands/tests/migrations.

---

### 3) Context Economy (Score: 2)

**Evidence**

* Good structure: “**# Development Phases**” with numbered phases and bullet lists.
* Additional sections: “**# Commit Messages**” and “**# Documentation Standards**”.

**Risks / failure modes**

* The skill mixes workflow guidance with repo policy (commit messages) and stack defaults; could distract from core behavior when user just wants the BDD/TDD loop.
* Some repeated principles (scannable docs, decisions, linking to code) appear in multiple places; could be consolidated.
* The “Technology defaults” block is long and specific; may not apply and adds noise.

**Fixes (minimal edits to raise to 3)**

* Add a brief “**Core loop**” summary at top; move commit/docs standards into an “Appendix” section (still same file).
* Compress technology defaults (or mark as “example defaults”) and emphasize they’re optional.
* Remove small redundancies (e.g., doc scannability guidance repeated across sections).

---

### 4) Verifiability (Score: 1)

**Evidence**

* Artifacts implied per phase: README, `docs/architecture.md`, type definitions, `.feature` files, tests.
* Test commands: `pnpm test`, `pnpm test:integration`, etc.
* Integration test guidance: “**Focus on persisted outcomes… Set up each scenario with a clean test database**.”

**Missing required information**

* No explicit **acceptance criteria** per phase (e.g., what must be true before moving to the next phase).
* No “definition of done” for artifacts (architecture doc completeness checks, data model coverage checks, test pass criteria).
* Limited validation steps for docs (links resolve, no schema duplication) and tests (which tags, what environment variables).

**Risks / failure modes**

* Produces documents/tests that look plausible but aren’t complete or consistent; user can’t easily verify quality.
* Could write integration tests that don’t actually assert persisted outcomes or are flaky without stability checks.
* Might proceed with implementation even if earlier artifacts are insufficient, because there’s no explicit gate beyond “user confirms.”

**Fixes (minimal edits to raise to 2)**

* Add a **“Phase exit checklist”** (1–3 bullets each phase).
* Add a quick verification step after doc changes: e.g., “README stays ≤1 page,” “architecture doc includes components/dependencies/choices,” “type files referenced by docs, no duplication.”
* Add a test verification rule: “Run relevant tests and report pass/fail + command used.”

---

### 5) Reversibility / Safety (Score: 2)

**Evidence**

* Human-in-loop: “**Wait for the user to confirm before proceeding**.”
* DB safety: “**Run tests against the test database — this protects the user's real data.**”
* Command safety: “**Execute commands from the workspace root… Avoid `cd` prefixes**.”

**Risks / failure modes**

* No rollback guidance if a step is wrong (e.g., “revert file,” “git reset,” “restore from previous commit”).
* “Clean test database” can imply destructive resets; without safeguards, could accidentally target wrong DB if env misconfigured.
* Doesn’t mention handling migrations safely (apply to test DB only, seed scripts, etc.).

**Fixes (minimal edits to raise to 3)**

* Add “**Reversibility**” subsection:

  * “Make small commits per step,” “use feature branches,” “how to revert last change.”
* Add a safety check: **confirm test DB connection string / environment** before running destructive DB resets.
* Add “dry-run” guidance where relevant (e.g., schema/migration generation preview).

---

### 6) Generalizability (Score: 2)

**Evidence**

* Workflow is broadly framed: “**Documentation-first, test-driven development workflow for building applications**.”
* Defaults are conditional: “**Unless the project's README… specify otherwise… Confirm with the user before adopting them**.”
* Testing guidance refers to “project’s existing testing conventions.”

**Risks / failure modes**

* Nuxt/pnpm/Tailwind/vue-shadcn defaults might bias the skill toward a specific stack even when irrelevant.
* File paths are somewhat opinionated (`__tests__/integration/`, `.feature` files) and may not fit non-JS projects.
* Lacks explicit “portability knobs” (e.g., configurable package manager, test runner, folder conventions).

**Fixes (minimal edits to raise to 3)**

* Add a short “**Stack/config adaptation**” note: detect existing conventions first; defaults apply only if absent.
* Make directories/examples explicitly “e.g.” and provide alternative patterns (without expanding too much).
* Add a small list of assumptions (Node ecosystem, BDD support) and how to adapt.

---

## Top 5 Issues (ranked)

1. **Weak verifiability gates**: no phase exit criteria → easy to produce incomplete docs/tests and still proceed.
2. **Missing “don’t use when”** boundaries → over-triggering and workflow mismatch.
3. **Ambiguity around `docs/next-steps.md` dependency** → brittle behavior if file is absent/outdated.
4. **Insufficient rollback/undo guidance** especially around “clean test database” and destructive resets.
5. **Tech defaults overfit** (Nuxt/pnpm stack) without strong guardrails for adopting existing project conventions.

---

## Patch Plan (≤10 items)

1. Add a **“When to use / When not to use”** section with 3 examples each.
2. Define **“step”** explicitly; reiterate single-file rule for phases 1–3.
3. Add fallback behavior if **`docs/next-steps.md` is missing** (generate next steps aligned to phase).
4. Add a **Phase Exit Checklist** (1–3 bullets per phase).
5. Require reporting **verification evidence** after each step (e.g., “ran X command,” “artifact created at path”).
6. Add **test DB safety check** language (confirm env/connection before resets).
7. Add a **Reversibility** subsection (branching, small commits, how to revert).
8. Mark tech defaults as **optional** and emphasize “detect existing conventions first.”
9. Tighten integration test guidance with a minimal **flakiness/stability** note (avoid UI assertions, stable fixtures).
10. Move “Commit Messages” + “Documentation Standards” under an **Appendix** header (optional reading).

---

## Targeted Rewrite (1–2 sections only)

### Rewrite 1: `<behavior>` (most critical for control + robustness)

```md
<behavior>
Work in small, reviewable increments.

Definition of a "step":
- Default: produce or update exactly one primary artifact (one doc OR one test file OR one small implementation change set).
- During document-first phases (1–3): change exactly one file per step.

After completing the current step:
- Provide 1–3 brief next-step suggestions (one line each).
- Prefer selecting the next step from [Next Steps](docs/next-steps.md). If that file is missing, propose next steps based on the current phase instead.

Wait for the user to confirm before moving to the next step or running any potentially destructive action (e.g., DB resets/migrations).
</behavior>
```

### Rewrite 2: Add a minimal “Phase Exit Checklist” block (verifiability gate)

```md
## Phase Exit Checklist (before moving on)

- Phase 1 (Vision): README.md is ≤ 1 page, user-facing, and reflects the feature’s intended behavior.
- Phase 2 (Architecture): docs/architecture.md includes components, dependencies, and explicit tech choices/rationale.
- Phase 3 (Data Modeling): types/models/schemas cover core entities + relationships; docs link to code types (no duplicated schemas).
- Phase 4 (Integration Tests): at least one end-to-end scenario asserts persisted outcomes; scenarios use a clean test DB.
- Phase 5 (Unit Tests): key business logic paths + edge cases are covered per existing conventions.
- Phase 6 (Implementation): relevant tests pass (record the command used) and changes are small enough to review.
```

---
