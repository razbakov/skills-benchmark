## Summary

This skill guides Claude in creating visually distinctive, production-grade frontend interfaces (HTML/CSS/JS, React, Vue) that avoid generic "AI slop" aesthetics. It activates when users request web components, pages, dashboards, or UI styling. Key constraints: bold aesthetic direction, no generic fonts/colors, working code output, and creative differentiation per generation.

## CRN Scorecard

| Dimension | Score | Rationale |
|---|---|---|
| Triggering Precision | 2 | Good trigger list in description; weak on exclusions and overlap with other skills |
| Degrees of Freedom Calibration | 1 | Heavy on aesthetic freedom, almost no specification of mandatory steps, defaults, or when to ask user |
| Context Economy | 2 | Reasonably concise and scannable; some redundant motivational prose |
| Verifiability | 0 | No success criteria, validation steps, or observable quality checks |
| Reversibility / Safety | 1 | Low-risk domain but no mention of accessibility validation, performance checks, or fallback handling |
| Generalizability | 2 | Works across frameworks and project types; font/CDN assumptions may not hold in all contexts |

## Detailed Findings

### 1. Triggering Precision (2)

**Evidence**: The `description` field lists explicit triggers: *"web components, pages, artifacts, posters, or applications (examples include websites, landing pages, dashboards, React components, HTML/CSS layouts, or when styling/beautifying any web UI)."*

**Risks**: No explicit "do NOT use when" section. Could over-trigger on requests that are purely functional (e.g., "build a form that submits data") where design flair is unwanted. Could conflict with a general coding skill or a component-library skill.

**Fixes**:
- Add a "Do NOT use" list (e.g., purely backend logic, data-only scripts, terminal UIs, when user explicitly requests plain/unstyled output).
- Add a sentence distinguishing this from generic code-generation skills.

### 2. Degrees of Freedom Calibration (1)

**Evidence**: The skill says *"Pick an extreme: brutally minimal, maximalist chaos…"* and *"Interpret creatively and make unexpected choices"* but never specifies mandatory steps (e.g., must use CSS variables, must include responsive breakpoints). No guidance on when to ask the user vs. proceed autonomously (e.g., framework choice, color palette).

**Risks**: Agent may make wildly inappropriate aesthetic choices for a corporate dashboard because it's told to be "bold." No defaults for framework, viewport, or accessibility level. No decision tree for "user didn't specify a style → do X."

**Fixes**:
- Add a default behavior section: "If the user doesn't specify a style, choose one appropriate to context and state the choice before coding."
- Mark mandatory constraints (responsive design, semantic HTML, CSS variables) vs. flexible ones (font choice, color palette, animation level).
- Add: "Ask the user before choosing a framework if not specified and multiple are viable."

### 3. Context Economy (2)

**Evidence**: File is ~400 words, scannable with bold headers. However, phrases like *"Claude is capable of extraordinary creative work. Don't hold back"* and *"There are so many flavors to choose from"* are motivational filler.

**Risks**: Minor—extra tokens consumed but not severely bloated.

**Fixes**:
- Cut the final paragraph and "There are so many flavors" sentence.
- Compress the NEVER list into a single compact sentence.

### 4. Verifiability (0)

**Evidence**: No success criteria anywhere. No checklist like "the output should render without console errors," "fonts load correctly," "passes color contrast checks," or "is responsive at 320px–1440px."

**Risks**: Agent has no way to self-check output quality. Could produce broken code, inaccessible contrast ratios, or layouts that fail on mobile—with no prompt to verify.

**Fixes**:
- Add a "Quality Checklist" section with observable criteria: renders without errors, responsive at common breakpoints, color contrast ≥ 4.5:1 for text, fonts load from valid CDN, no horizontal scroll on mobile.
- Add: "After generating, mentally walk through the output against this checklist."

### 5. Reversibility / Safety (1)

**Evidence**: No mention of accessibility standards, performance budgets, external dependency risks (CDN fonts failing), or fallback fonts. The domain is low-risk (no destructive filesystem ops), which prevents a 0.

**Risks**: Agent could rely on a CDN that's unavailable, produce seizure-inducing animations, or fail WCAG entirely. No font-stack fallbacks mentioned.

**Fixes**:
- Add: "Always include fallback font stacks."
- Add: "Respect `prefers-reduced-motion` for animations."
- Add: "Avoid external dependencies that could break offline; note any CDN reliance to the user."

### 6. Generalizability (2)

**Evidence**: Mentions HTML/CSS/JS, React, Vue—framework-agnostic. Aesthetic guidelines are broadly applicable. However, the font guidance (*"Avoid Inter, Roboto, Arial"*) may conflict with projects using an existing design system where Inter is standard.

**Risks**: Overriding a user's established design system because the skill says to avoid common fonts.

**Fixes**:
- Add: "If the user specifies or implies an existing design system or brand guidelines, follow those over these aesthetic defaults."
- Make the font blacklist a preference, not a hard rule.

## Top 5 Issues

1. **No verifiability at all (Verifiability: 0)** — No success criteria, self-checks, or observable quality gates. Highest severity because the agent cannot detect its own failures.
2. **No guidance on when to ask vs. proceed (DoF: 1)** — Agent may make bold choices (e.g., brutalist style for a medical app) without confirming with the user, leading to costly rework.
3. **No accessibility or fallback safeguards (Safety: 1)** — Missing `prefers-reduced-motion`, contrast checks, fallback fonts. Could produce inaccessible or broken output.
4. **No "do not trigger" criteria (Triggering: 2)** — Risk of over-triggering on purely functional frontend requests where creative styling is unwanted.
5. **Aesthetic rules override user/brand context (Generalizability: 2)** — Hard "NEVER use Inter/Roboto" conflicts with legitimate design systems.

## Patch Plan

1. **Add a "Quality Checklist" section** after implementation guidance — renders without errors, responsive, contrast ratio, font fallbacks, reduced-motion.
2. **Add "When NOT to use this skill"** to the description or a dedicated section.
3. **Add default behavior rules**: "If user doesn't specify style → choose contextually appropriate style and state it. If framework not specified → ask or default to HTML/CSS."
4. **Add "Respect existing design systems"** clause — brand guidelines override aesthetic defaults.
5. **Add accessibility baselines**: `prefers-reduced-motion`, semantic HTML, ARIA where needed, contrast ≥ 4.5:1.
6. **Add fallback font stacks** as a mandatory practice.
7. **Mark mandatory vs. flexible** constraints (responsive = mandatory; animation style = flexible).
8. **Trim motivational filler** (final paragraph, "so many flavors" line).
9. **Add "state your design direction"** step — agent should briefly explain its aesthetic choice before coding.
10. **Soften font blacklist** from NEVER to "avoid unless the project's design system specifies otherwise."

## Targeted Rewrite

### Rewritten: Quality Checklist (NEW section — addresses Issue #1)

```markdown
## Quality Checklist

Before delivering, verify the output meets these criteria:

1. **Renders clean**: No console errors, no broken imports, no missing assets.
2. **Responsive**: Layouts work at 320px, 768px, and 1440px without horizontal scroll.
3. **Accessible**: Text contrast ≥ 4.5:1 (AA). Animations respect `prefers-reduced-motion`. Semantic HTML elements used. Interactive elements are keyboard-navigable.
4. **Fonts load**: All custom fonts reference valid CDN URLs with appropriate fallback stacks (e.g., `font-family: 'Clash Display', 'Helvetica Neue', sans-serif`).
5. **Design direction stated**: Before code, briefly state the chosen aesthetic and why it fits the context.

If any check fails, fix it before presenting the output.
```

### Rewritten: Design Thinking (addresses Issues #2, #4, #5)

```markdown
## Design Thinking

Before coding, understand the context and commit to a clear aesthetic direction:

- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Choose an aesthetic that fits the context — brutally minimal, maximalist, retro-futuristic, organic, luxury, playful, editorial, brutalist, art deco, industrial, etc. If the user hasn't specified, pick one appropriate to the audience and state your choice before coding.
- **Constraints**: Technical requirements (framework, performance, accessibility). If the user specifies a design system or brand, follow it — override the aesthetic defaults in this skill.
- **Differentiation**: What makes this memorable? Identify one signature element.

**Defaults when the user underspecifies:**
- No framework stated → use HTML/CSS/JS unless the request implies React or another framework.
- No style stated → choose a direction fitting the content, and explain your choice in one sentence before the code.
- No accessibility requirements stated → still target WCAG AA as a baseline.

Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work — the key is intentionality, not intensity.
```