## Summary

This skill defines a lightweight “brand styling” post-processing layer: apply Anthropic’s official color palette and typography rules (Poppins for headings, Lora for body with fallbacks) to artifacts that benefit from consistent brand look-and-feel. It should activate when the user asks for brand colors, visual formatting, design standards, or “make it look like Anthropic,” and it implicitly targets slide/doc-like artifacts with text and shapes. Key constraints include using the specified hex colors, applying font thresholds (headings ≥24pt), and falling back safely when custom fonts aren’t installed.

## CRN Scorecard


| dimension                         | score | 1-line rationale                                                                                                                                        |
| --------------------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1) Triggering Precision           | 1     | Has general “use it when branding applies,” but lacks explicit non-triggers, artifact scope, and conflict boundaries.                                   |
| 2) Degrees of Freedom Calibration | 1     | Some rules are specific (colors, 24pt threshold), but many choices are underspecified (what counts as heading, cycling order, exceptions, when to ask). |
| 3) Context Economy                | 2     | Short and scannable; mild redundancy between “Features” and “Technical Details.”                                                                        |
| 4) Verifiability                  | 1     | No explicit success criteria/checklist or “how to tell it worked,” and tool assumptions (e.g., python-pptx) aren’t tied to observable outputs.          |
| 5) Reversibility / Safety         | 0     | No rollback guidance, no “don’t overwrite originals,” and no safeguards for destructive formatting changes.                                             |
| 6) Generalizability               | 2     | Palette/typography are reusable across artifacts, but it over-implies PPT/python-pptx and doesn’t generalize rules for non-slide contexts.              |


## Detailed Findings

### 1) Triggering Precision (Score: 1)

**Evidence (excerpts)**

- Frontmatter: “Applies Anthropic's official brand colors and typography to any sort of artifact…”
- Description: “Use it when brand colors or style guidelines, visual formatting, or company design standards apply.”
- Overview: “To access Anthropic's official brand identity and style resources, use this skill.”

**Risks / failure modes**

- Over-triggers on any “styling” request (e.g., user wants *any* clean design, not specifically Anthropic).
- Conflicts with other skills that also handle formatting (e.g., “make a deck look professional,” “apply Google style,” “export to PDF”), because “any sort of artifact” is broad.
- Under-triggers: if user asks “make it match our brand” without saying Anthropic, it’s unclear whether to apply this anyway.

**Fixes (minimal edits to raise to 2)**

- Add a **“When to use / When NOT to use”** section with 3–5 bullets each.
- Add 2–3 **trigger examples** (e.g., “Apply Anthropic branding to this deck,” “Use official Anthropic palette”).
- Add explicit **non-triggers** (e.g., “Do not apply if user requests a different brand, or requests neutral styling without brand reference.”)

---

### 2) Degrees of Freedom Calibration (Score: 1)

**Evidence (excerpts)**

- “Applies Poppins font to headings (24pt and larger)”
- “Non-text shapes use accent colors”
- “Cycles through orange, blue, and green accents”
- “Smart color selection based on background”
- “Fonts should be pre-installed… fall back to Arial/Georgia”

**Risks / failure modes**

- “Headings (24pt+)” is a decent rule, but many docs/decks don’t use consistent point sizes; could misclassify callouts or captions as headings.
- “Smart color selection based on background” is not specified (contrast targets? what if background is image/gradient?).
- “Cycles through” doesn’t define whether cycling is per-slide, per-shape, or per-theme section; could produce noisy visuals.
- Lacks “ask vs proceed” guidance: if fonts aren’t installed, should the agent proceed with fallbacks or ask user to install? (It implies proceed, but not explicit.)

**Fixes (minimal edits to raise to 2)**

- Define **deterministic defaults**:
  - Heading detection order: style name → size threshold → bold → position.
  - Accent cycling unit: per-slide or per-section; define the order explicitly (Orange → Blue → Green).
- Specify contrast/legibility rule of thumb (e.g., ensure text meets basic contrast; if unsure, use Dark text on Light background).
- Add a short “If ambiguous, do X” rule: proceed with fallbacks unless user explicitly requires exact fonts.

---

### 3) Context Economy (Score: 2)

**Evidence (excerpts)**

- Clear headings and compact sections: “Colors,” “Typography,” “Features,” “Technical Details.”
- Some repetition:
  - “Applies Poppins font to headings…” appears under both “Smart Font Application” and “Text Styling.”
  - Font management and best results advice repeated.

**Risks / failure modes**

- Minor: duplication can lead to drift (e.g., if a threshold changes, one section might not get updated).
- “Technical Details” mentions python-pptx specifically, which may distract or mislead if the skill is intended for broader artifacts.

**Fixes (minimal edits to raise to 3)**

- Merge duplicate font rules into a single canonical bullet list (and reference it from other sections).
- Move python-pptx mention into an “Implementation notes (optional)” subsection or make it tool-agnostic.

---

### 4) Verifiability (Score: 1)

**Evidence (excerpts)**

- “Uses RGB color values for precise brand matching”
- “Maintains color fidelity…”
- No checklist, acceptance criteria, or “done when…”

**Risks / failure modes**

- Agent may “apply” styling inconsistently without being able to self-check.
- Users/reviewers can’t quickly verify compliance (e.g., did every heading become Poppins? did shapes use accent colors only?).
- “Maintains color fidelity across different systems” is a claim without a validation method.

**Fixes (minimal edits to raise to 2)**

- Add a **“Validation checklist”** (5–8 items) with observable checks:
  - Confirm all text colors are from the palette (or justified exceptions).
  - Confirm heading font rule applied (≥24pt → Poppins/Arial).
  - Confirm body font rule applied.
  - Confirm non-text shapes use accent colors only.
  - Confirm backgrounds are Light/Dark/Light Gray as specified.
- Add “report what changed” guidance: e.g., list font fallbacks used and any elements skipped.

---

### 5) Reversibility / Safety (Score: 0)

**Evidence (excerpts)**

- No mention of preserving originals, backups, dry runs, or undo steps.
- No mention of limiting scope (e.g., only apply to selected slides) or logging changes.

**Risks / failure modes**

- Destructive overwrites: user could lose prior styling that they wanted to keep (especially if this is applied “to any artifact”).
- Inability to revert quickly if the outcome is wrong (e.g., accidental recoloring of charts, brand-unsafe use on regulated materials).
- If applied to documents with semantic meaning encoded in colors (e.g., risk levels), automatic recoloring can introduce miscommunication.

**Fixes (minimal edits to raise to 1)**

- Add a **Safety / Rollback** section:
  - “Duplicate the file / work on a copy first.”
  - “Limit changes to theme-level styles when possible.”
  - “Do not recolor data-encoding chart series unless requested.”
  - “Provide a change summary so user can manually revert.”
- Add “opt-out” rules: skip elements marked “do not change,” skip brand-critical logos, etc.

---

### 6) Generalizability (Score: 2)

**Evidence (excerpts)**

- “any sort of artifact that may benefit” + general palette and font rules.
- But “Applied via python-pptx’s RGBColor class” ties it to PowerPoint tooling.

**Risks / failure modes**

- If used on docs/web pages/spreadsheets, rules may not translate (point sizes, shape concepts, RGBColor).
- Some artifacts won’t have Poppins/Lora installed; the skill is okay with fallbacks but doesn’t describe equivalents for web fonts or embedding.

**Fixes (minimal edits to raise to 3)**

- Make implementation tool-agnostic: describe outcomes (colors/fonts) and provide “mapping guidance” per artifact type (slides/docs/web) in 2–3 bullets.
- Add configurable assumptions: default background (Light) unless user requests Dark; allow “accent off” mode for conservative documents.

---

## Top 5 Issues (ranked)

1. **No reversibility/rollback guidance** (highest risk of destructive, hard-to-undo changes).
2. **Triggering is too broad** (“any sort of artifact”) with no non-triggers → over-application.
3. **Underspecified “smart” behaviors** (background contrast, heading detection, accent cycling unit) → inconsistent results.
4. **No verifiable success criteria** (no checklist, no “report changes”) → hard to audit.
5. **Tooling assumption leak (python-pptx)** reduces portability and may confuse intended usage.

## Patch Plan (≤10 items)

1. Add **When to use / When NOT to use** with concrete examples.
2. Add **Safety / Rollback** section: work on copy, summarize changes, skip data-encoding colors, reversible scope.
3. Define **heading detection heuristic** (ordered rules) and specify what to do when ambiguous.
4. Specify **accent cycling rule** (order + scope: per-slide/per-section) and default.
5. Define **contrast rule** (default text color choice; avoid low-contrast combos).
6. Add **Validation checklist** with observable checks.
7. Add **Change summary output** expectation (fonts used, fallbacks, skipped items).
8. Remove redundancy by consolidating duplicated font rules into one canonical section.
9. Make “Technical Details” tool-agnostic; move python-pptx mention to optional implementation notes.

## Targeted Rewrite (1–2 sections only)

### Rewritten section 1: Overview (critical for triggering precision)

## Overview

Use this skill to apply **Anthropic’s official** color palette and typography to an existing artifact (e.g., slides, docs, or visuals) **only when the user explicitly requests Anthropic branding** or you are working on an Anthropic-branded deliverable.

**Use when**

- The user asks to “apply Anthropic branding,” “use Anthropic brand colors,” or “match Anthropic style.”
- You are polishing a deliverable that must follow Anthropic design standards (palette + typography).

**Do not use when**

- The user requests a different company’s brand, a neutral/non-branded style, or “make it pretty” without Anthropic context.
- Color carries **semantic meaning** (e.g., chart series, risk levels) unless the user asks to recolor those elements.

---

### Rewritten section 2: Technical Details (critical for safety + verifiability + degrees of freedom)

## Technical Details

### Application Rules (defaults)

- **Text fonts**
  - Headings: apply **Poppins** to text that is **≥24pt** (fallback: **Arial**).
  - Body: apply **Lora** to remaining text (fallback: **Georgia**).
  - If font availability is unknown, proceed with fallbacks and report which fonts were used.
- **Colors**
  - Use only the specified hex values for brand colors and accents.
  - Choose text color for legibility: default to **Dark text on Light/Light Gray backgrounds** and **Light text on Dark backgrounds**.
- **Shapes**
  - Non-text decorative shapes use accent colors in this order: **Orange → Blue → Green**, cycling **per slide** by default.

### Safety / Rollback

- Apply changes on a **copy** of the artifact when possible, or preserve the original theme/styles for manual rollback.
- **Do not recolor chart/data-encoding elements** (series colors, heatmaps) unless the user explicitly requests it.
- Provide a brief **change summary** (fonts applied/fallbacks used, any elements skipped) so the user can verify or revert specific changes.

