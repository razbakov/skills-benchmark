# AI-Powered Skill Review

### 1. Problem Statement

Skills in the ecosystem vary wildly in quality — some are well-structured, secure, and broadly useful, while others are vague, overly narrow, or contain risky instructions. Today, users have no way to evaluate a skill's quality before installing it. They must read the raw SKILL.md, mentally assess whether it's well-written, and hope for the best. This is the equivalent of installing npm packages with no download counts, no security audits, and no README quality signals.

Users who manage dozens of skills across Cursor, Claude Code, and Codex waste time on trial-and-error: installing a skill, testing it, finding it unhelpful, and uninstalling it. For skill authors, there's no feedback loop — no way to know what to improve.

The cost of not solving this: low-quality skills erode trust in the entire skill ecosystem, users avoid trying new skills, and skill authors have no incentive to improve.

### 2. Goals

1. **Automated quality signal**: Every skill gets a multi-dimensional review score without requiring human reviewers or community adoption
2. **Informed decisions**: Users can compare skills at a glance and choose the best one for their needs before installing
3. **Author feedback loop**: Skill authors see specific, actionable feedback on how to improve their skills
4. **Trust foundation**: Establish the scoring infrastructure that community ratings and security audits can build on later

### 3. Non-Goals

1. **Community/crowd-sourced reviews** — Requires user accounts, moderation, and critical mass. Separate initiative for later.
2. **Automated skill fixing/rewriting** — Review should diagnose, not prescribe rewrites. Too complex and opinionated for v1.
3. **Blocking installation of low-quality skills** — Users should be informed, not gatekept. Quality scores are advisory.
4. **Cross-skill comparison rankings** — Global leaderboards require normalization across categories. Premature for v1.
5. **Real-time review during skill editing** — IDE integration for live feedback is a separate feature.

### 4. User Stories

**As a skill consumer**, I want to see a quality score for each skill in the list so that I can quickly identify well-crafted skills without reading every SKILL.md.

**As a skill consumer**, I want to see a breakdown of the review across multiple dimensions so that I can understand *why* a skill scored the way it did (e.g., great clarity but poor scope definition).

**As a skill consumer**, I want to see review details for a specific skill so that I can make an informed install/uninstall decision.

**As a skill author**, I want to trigger a review of my skill so that I get actionable feedback on how to improve it.

**As a skill author**, I want to see specific suggestions per dimension so that I know exactly what to fix (not just "this is bad").

**As a power user**, I want to re-run reviews after updating a skill so that I can verify my improvements moved the scores.

**As a user with many skills**, I want reviews to run in the background without blocking the UI so that I can continue browsing and managing skills.

### 5. Requirements

#### Must-Have (P0)

**R1: Review Dimensions**
The AI review must evaluate each skill across these dimensions:

| Dimension | What it measures |
|-----------|-----------------|
| **Clarity** | Is the skill's purpose and behavior unambiguous? Are instructions clear to an AI agent? |
| **Completeness** | Does it cover edge cases, error handling, and expected outputs? Is the frontmatter complete? |
| **Specificity** | Is the scope well-defined? Does it avoid being too vague or too narrow? |
| **Safety** | Does it avoid risky patterns (arbitrary code execution, credential handling, destructive commands)? |
| **Reusability** | Is it useful across projects/contexts, or locked to a specific repo/stack? |

Each dimension produces:
- A score (1–5)
- A one-sentence summary
- 0–3 specific suggestions for improvement

Acceptance criteria:
- [ ] All 5 dimensions are evaluated for every review
- [ ] Each dimension has a numeric score (1–5, integer)
- [ ] Each dimension has a summary sentence (< 120 chars)
- [ ] Suggestions are specific and actionable (reference the skill content)
- [ ] An overall score is computed (average of dimensions, rounded to 1 decimal)

**R2: Review Data Model**

```typescript
interface SkillReview {
  skillId: string           // matches Skill.name + sourceName
  overallScore: number      // 1.0–5.0
  dimensions: ReviewDimension[]
  reviewedAt: string        // ISO timestamp
  skillHash: string         // hash of SKILL.md content at review time
}

interface ReviewDimension {
  name: string              // e.g., "clarity"
  score: number             // 1–5
  summary: string           // one-sentence assessment
  suggestions: string[]     // 0–3 actionable improvements
}
```

Acceptance criteria:
- [ ] Reviews are persisted to `~/.config/skills-manager/reviews.json`
- [ ] Each review is keyed by skill identifier (name + source)
- [ ] Stale reviews are detectable via `skillHash` comparison
- [ ] Review data loads on app startup without blocking the UI

**R3: Trigger Review from UI**

Users can trigger a review for a single skill from the skill detail view.

Acceptance criteria:
- [ ] A "Review" button appears on the skill detail panel
- [ ] Clicking it starts the AI review (shows loading state)
- [ ] On completion, dimension scores and suggestions display inline
- [ ] Errors (e.g., AI provider unavailable) show a clear message
- [ ] Re-reviewing a skill overwrites the previous review

**R4: Display Review Summary in Skill List**

The skill list shows the overall score as a badge/indicator next to each skill.

Acceptance criteria:
- [ ] Skills with a review show the overall score (e.g., "4.2" or a star/dot indicator)
- [ ] Skills without a review show no score (no "0" or "unrated")
- [ ] Score badge uses color coding: green (4–5), yellow (3–3.9), red (1–2.9)
- [ ] Tapping the score navigates to/expands the review detail

**R5: AI Review Engine (Backend)**

The review engine reads a skill's SKILL.md content, sends it to a local AI agent (Claude Code CLI or similar), and parses the structured response.

Acceptance criteria:
- [ ] Engine accepts a skill path, reads SKILL.md, and constructs a review prompt
- [ ] Prompt instructs the AI to evaluate all 5 dimensions with scores and suggestions
- [ ] Response is parsed into the `SkillReview` data model
- [ ] Engine handles malformed AI responses gracefully (retry once, then error)
- [ ] Engine computes `skillHash` from SKILL.md content for staleness detection

#### Nice-to-Have (P1)

**R6: Batch Review All Skills**
A "Review All" action that queues reviews for all unreviewed (or stale) skills, processing them sequentially to avoid rate limits.

**R7: Staleness Indicator**
If a skill's content has changed since its last review, show a "stale" badge and offer to re-review.

**R8: Sort/Filter by Score**
Allow sorting the skill list by overall review score, or filtering to show only skills above a threshold.

**R9: Dimension Drill-Down View**
A dedicated panel showing all dimension scores as a radar chart or bar chart with expandable suggestion lists.

#### Future Considerations (P2)

**R10: Custom Dimensions** — Let users define their own review dimensions (e.g., "TypeScript relevance" for their stack).

**R11: Review History** — Track score changes over time to show improvement trends.

**R12: Community Review Aggregation** — Combine AI reviews with human ratings when community features launch.

**R13: Comparative Review** — "Compare these 3 skills" side-by-side across dimensions.

### 6. Success Metrics

| Metric | Target | Stretch | Measurement |
|--------|--------|---------|-------------|
| Review adoption | 60% of users trigger at least 1 review in first week | 80% | Track review events in app |
| Coverage | 50% of installed skills have reviews within 2 weeks | 80% | reviews.json vs. installed count |
| Author action | 30% of skill authors update a skill after seeing review feedback | 50% | Staleness detection + re-review |
| Score correlation | Users install high-scored skills 2x more than low-scored | 3x | Cross-reference reviews with install actions |

Evaluate at: 2 weeks and 6 weeks post-launch.

### 7. Open Questions

| # | Question | Owner | Blocking? |
|---|----------|-------|-----------|
| 1 | Which AI provider/model should the review engine use? Claude Code CLI, direct API, or configurable? | Engineering | Yes |
| 2 | Should reviews be shareable/exportable so skill authors in other repos can see them? | Product | No |
| 3 | What's the right UX for "Review All" — background queue with progress, or blocking modal? | Design | No |
| 4 | Should we cache reviews in the scan-cache.json or a separate file? | Engineering | No |
| 5 | Is 5 dimensions the right number, or should we start with fewer (e.g., 3) for v1? | Product | No |

### 8. Timeline Considerations

- **No hard deadlines.** This is an ecosystem quality improvement, not a contractual commitment.
- **Dependency**: Requires a working AI provider integration. The recommendation engine already uses `child_process` to spawn CLI agents — this can be reused.
- **Suggested phasing**:
  - **Phase 1**: R1–R5 (core review engine + single-skill UI) — ship and learn
  - **Phase 2**: R6–R9 (batch review, staleness, sorting) — based on Phase 1 feedback
