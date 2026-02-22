# Skill Detail: Review a Skill with AI Across Multiple Dimensions

As a developer evaluating skills, I want the app to review a selected skill with AI across several quality dimensions, so that I can decide faster whether to install, keep, or replace it.

## Context

The skill detail panel currently lets the user see metadata and a `SKILL.md Preview` in both `Installed Skills` and `Available Skills`. Product research also highlights "No quality signals or trust" as a key gap.

This story adds an in-app AI review so users do not need to manually judge long skill files line by line.

## Acceptance Criteria

### Start Review

- The skill detail panel for both `Installed Skills` and `Available Skills` includes a `Review with AI` action.
- When the user clicks `Review with AI`, the detail panel shows a visible in-progress state until the review is complete.
- If a review is already running, the user cannot start a second review for the same skill at the same time.

### Multi-Dimension Results

- When the review completes, the panel shows an overall recommendation and a short plain-language summary.
- The review includes these dimensions: `Safety`, `Instruction Efficiency`, `Standards Fit`, and `Maintenance`.
- Each dimension shows a clear rating and a short explanation of why that rating was given.

### Retry and Transparency

- If the review fails, the panel shows a clear error message and the user can retry immediately.
- The user can still read the `SKILL.md Preview` while review results are visible, so the AI output can be compared with the source text.

## Out of Scope

- Community voting, public comments, or install-count based rankings.
- Publishing the review outside the desktop app.
- Auto-installing or auto-disabling a skill based only on review output.
