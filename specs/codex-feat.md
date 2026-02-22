# AI Skill Review: Multi-Dimensional Evaluation

## 1. Problem Statement

Teams can install skills quickly, but they still lack a dependable way to evaluate quality
before trusting those skills in production workflows. Existing project research flags this
as a recurring gap (`P14: No quality signals`), with related risk from prompt injection
(`P13`) and poor skill reliability. Without a review layer, users select skills based on
name or recency, leading to wrong choices, wasted setup time, and avoidable safety issues.

## 2. Goals

- Provide an AI-generated review for at least 80% of installed or discovered skills within
  60 seconds of trigger.
- Give each reviewed skill a clear multi-dimensional scorecard so users can compare options
  without opening raw `SKILL.md` files.
- Reduce post-install rollback or uninstall events caused by poor fit/quality by 25% within
  60 days of launch.
- Increase user confidence in skill selection, measured by a 20% improvement in
  "I can tell which skill to trust" survey responses.
- Create a reusable review artifact (scores + findings + model metadata) that other features
  can consume (search ranking, recommendations, trust views).

## 3. Non-Goals

- Running skills against live production repositories to prove runtime correctness in v1.
  Why out of scope: requires sandbox orchestration and scenario fixtures.
- Replacing dedicated security scanners with AI-only judgments.
  Why out of scope: scanner integration remains a separate control.
- Community reviews, ratings, or social moderation in v1.
  Why out of scope: requires identity, abuse controls, and ranking policy.
- Auto-blocking installs solely from AI scores in v1.
  Why out of scope: false positives must be understood before hard enforcement.
- Auto-rewriting and publishing corrected skills.
  Why out of scope: high risk of unwanted edits and ownership ambiguity.

## 4. User Stories

- As a developer evaluating a skill, I want a per-dimension review score so that I can pick
  the safest and most effective option faster.
- As a security-conscious user, I want explicit risk findings and evidence lines so that I
  can understand why a skill was flagged.
- As a skill author, I want actionable improvement suggestions so that I can raise quality
  before sharing or reinstalling.
- As a team lead, I want consistent review criteria across skills so that adoption decisions
  are less subjective.
- As a user in an offline or rate-limited state, I want graceful fallback messaging so that
  skill management continues even when review cannot run.
- As a user handling malformed skill files, I want deterministic review errors so that I can
  fix format issues without guesswork.

## 5. Requirements

### Must-Have (P0)

#### R1. Multi-Dimensional Review Contract

System generates a review with an overall score plus required dimensions:

- Spec compliance (format and metadata quality)
- Safety and prompt-injection risk
- Clarity and maintainability
- Task fit and instruction quality
- Operational hygiene (dependency and side-effect signals)

Acceptance criteria:
- Given a valid skill input, when review completes, then output includes all required
  dimensions with numeric score (0-100), confidence, and severity label.
- Given a valid skill input, when review completes, then output includes one overall score
  and a short summary rationale.

Technical considerations:
- Use structured JSON schema with strict validation.
- Dimension names and scoring rubric must be versioned.
- Dependency: shared type definitions in core domain models.

#### R2. Triggering and Re-Review Behavior

Review can be triggered on demand and automatically after install/import/skill update.

Acceptance criteria:
- Given a newly installed skill, when install succeeds, then review job is enqueued.
- Given unchanged skill content hash, when auto-review trigger fires, then system reuses the
  existing latest review unless force-refresh is requested.
- Given manual user request, when "Review skill" is clicked, then a new review is created.

Technical considerations:
- Queue processing should avoid duplicate in-flight jobs for the same skill hash.
- Dependency: scanner hash/index output.

#### R3. Explainability and Evidence

Each finding includes evidence references so users can inspect context quickly.

Acceptance criteria:
- Given a flagged risk or recommendation, when details are opened, then user sees referenced
  line spans or sections from `SKILL.md`.
- Given low-confidence output, when rendered, then UI visibly marks confidence and caveats.

Technical considerations:
- Persist lightweight evidence references, not full duplicated file content.
- Dependency: parser that can map normalized text back to source offsets.

#### R4. UI Integration in Skill Detail

Skill detail shows review status, overall score, per-dimension breakdown, and top actions.

Acceptance criteria:
- Given a reviewed skill, when detail view loads, then user sees overall score and sorted
  dimension cards.
- Given no review yet, when detail view loads, then user sees clear "Not reviewed" state and
  trigger action.
- Given review failure, when detail view loads, then user sees retry action with error reason.

Technical considerations:
- Reuse existing status/action feedback patterns from interaction design.
- Dependency: desktop UI view model updates.

#### R5. Reliability, Cost, and Fallback

Review pipeline must fail safely and keep skill operations unblocked.

Acceptance criteria:
- Given AI provider failure/time-out, when review attempt ends, then skill install/import is
  not blocked and status becomes "Review unavailable".
- Given configurable monthly budget threshold exceeded, when new review starts, then system
  applies configured policy (skip/defer/manual only) and logs reason.
- Given retries exhausted, when job fails, then status remains recoverable and retryable.

Technical considerations:
- Add provider abstraction for future model switching.
- Capture token/cost telemetry per review.

#### R6. Persistence and Auditability

Store review artifacts for traceability and future ranking/recommendation features.

Acceptance criteria:
- Given completed review, when persisted, then artifact stores skill hash, model id,
  prompt version, rubric version, timestamp, and result payload.
- Given multiple reviews over time, when querying, then latest is shown by default and
  history is available.

Technical considerations:
- Keep schema compatible with future quality-trend visualizations.

### Nice-to-Have (P1)

#### R7. Diff-Aware Re-Review

Only changed sections are highlighted between review versions, with impact summary.

#### R8. Alternative Comparison View

For skills with overlapping purpose/name, show side-by-side dimension comparison.

#### R9. Shareable Review Export

Allow exporting review summary (Markdown/JSON) for team decisions.

### Future Considerations (P2)

- Scenario-based task evaluation harness (run skill on benchmark tasks).
- Policy-driven enforcement (block/warn/install gates by org thresholds).
- Community feedback blending with automated scores.
- Continuous rescoring when model/rubric versions change.

## 6. Success Metrics

### Leading Indicators (first 30 days)

- Review coverage: % of installed/discovered skills with a completed review.
  Target: 80% success, 90% stretch.
- Review latency (p95): time from trigger to completed review.
  Target: <= 60s success, <= 30s stretch.
- Review interaction rate: % of detail views where users expand review insights.
  Target: 40% success, 55% stretch.
- Actionability rate: % of reviewed skills where a user takes a follow-up action
  (install, skip, update, disable) within same session.
  Target: 35% success, 50% stretch.
- Failed review rate.
  Target: < 5% success, < 2% stretch.

### Lagging Indicators (60-90 days)

- Post-install rollback/uninstall due to poor quality.
  Target: 25% reduction.
- Reported "wrong skill choice" support issues.
  Target: 30% reduction.
- Trust sentiment from in-product pulse survey.
  Target: +20% improvement.

Measurement notes:
- Source: app telemetry + local event logs + support tags.
- Evaluate at 30, 60, and 90 days after launch cohort.

## 7. Open Questions

- Blocking (engineering): Which model/provider combination meets latency and cost targets
  for local-desktop usage?
- Blocking (data): What minimum event schema is required to attribute "wrong skill choice"
  reduction confidently?
- Blocking (legal/security): Are there policy constraints for sending skill content to a
  remote AI provider, and do we need local-only mode by default?
- Non-blocking (design): Should per-dimension score be numeric-first or label-first for
  faster scanning?
- Non-blocking (product): Should low-confidence reviews default to warning badge color or
  neutral styling?

## 8. Timeline Considerations

- Phase 0 (1 week): rubric finalization, schema definition, provider spike, telemetry plan.
- Phase 1 (2 weeks): backend review pipeline, persistence, manual trigger, base UI state.
- Phase 2 (1-2 weeks): auto-trigger on install/update, failure handling, confidence/evidence
  display, instrumentation.
- Phase 3 (1 week): polish, rollout guardrails, docs, and success-metric dashboard setup.

Dependencies and risks:
- Depends on stable skill hashing/index outputs and UI detail view integration.
- External provider outages or rate limits can delay rollout unless fallback is in place.
- Security/legal review may change default provider mode (remote vs. local inference).
