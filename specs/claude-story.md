# Skill Detail: Review Skill Quality with AI

As a developer browsing skills, I want to request an AI-powered quality review of any skill, so that I can understand its strengths and weaknesses before deciding to install or keep it.

## Context

Skills are markdown instruction files written by different authors with varying quality. Currently there is no way to assess whether a skill is well-written, safe, focused, or maintainable — the user must read the entire SKILL.md and judge for themselves. An automated review gives users a quick, structured quality signal across multiple dimensions, helping them make informed decisions about which skills to trust and install.

The review runs on-demand when the user clicks a button on the skill detail panel. Results are stored in the app's local cache alongside other skill metadata.

## Acceptance Criteria

### Triggering a Review

- A "Review" button is visible on the skill detail panel for any skill (installed or available).
- Clicking "Review" sends the full content of the skill's SKILL.md to an AI model for analysis.
- While the review is in progress, the button shows a loading state and the user can continue browsing other skills.
- If a review has already been run for the current version of a skill, the cached result is shown instead of re-running the analysis.

### Review Dimensions

The review evaluates the skill across four dimensions, each producing a short summary and a rating (e.g. good / needs improvement / poor):

1. **Clarity & Completeness** — Are the instructions clear, unambiguous, and detailed enough for an AI agent to follow without guessing?
2. **Security & Safety** — Does the skill contain risky patterns such as running arbitrary commands, accessing sensitive data, or instructions that could be exploited via prompt injection?
3. **Specificity & Scope** — Is the skill focused on a single, well-defined responsibility, or is it too broad and trying to do too many things?
4. **Maintainability** — Is the skill well-structured with proper metadata (name, description), logical organization, and easy to update over time?

### Displaying Results

- Review results appear in a dedicated section on the skill detail panel, below the existing skill preview.
- Each dimension shows its name, rating, and a one-to-two sentence explanation in plain language.
- An overall quality indicator summarizes the four dimension ratings at a glance.

## Out of Scope

- Automatic review on install (may be added later).
- Community ratings, text reviews, or install counts.
- Comparing review scores across skills (e.g. leaderboards or rankings).
- Choosing or configuring which AI model performs the review.
