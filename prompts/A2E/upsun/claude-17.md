## Summary

This skill enables Claude to manage Upsun PaaS projects via the Upsun CLI (v5.6.0+), covering 130+ commands across deployments, environments, backups, scaling, databases, and access control. It activates when users mention Upsun-related operations or CLI commands. It explicitly does NOT execute commands without authentication, and delegates deep details to ~10 reference files. The skill is essentially a well-organized routing/discovery layer with minimal inline execution logic.

## Scorecard

| # | Dimension | Score | Rationale |
|---|-----------|-------|-----------|
| 1 | Metadata Precision | 2 | Name and description are specific; description is overlong but functional as a trigger surface. |
| 2 | Triggering Precision & Distinctiveness | 2 | Good trigger phrases; no "Do NOT use when" boundaries; low conflict risk (Upsun is niche). |
| 3 | Scope Fit & Responsibility Size | 2 | Covers a huge surface (130+ commands) but uses routing to reference files effectively. No explicit non-goals. |
| 4 | Context Economy | 2 | Good progressive disclosure via reference links; some redundancy between "What Do You Want to Do?" and "Command Discovery" table. |
| 5 | Degrees of Freedom Calibration | 1 | No defaults, no "ask vs assume" rules, no guidance on when to confirm before executing destructive commands. |
| 6 | Workflow Clarity & Progressive Disclosure | 2 | Happy path (Quick Start) comes first; decision tree structure is clear; but no step-by-step workflow for complex multi-step operations inline. |
| 7 | Verifiability & Feedback Loops | 1 | No success criteria, no validation steps, no "verify → fix → re-verify" loops defined. References scripts but doesn't describe expected outputs. |
| 8 | Operational Hygiene | 2 | Version pinned (v5.6.0+), auth check mentioned, references install guide. Missing: cleanup guidance, idempotency notes, resource usage constraints. |
| 9 | Safety & Security | 1 | Auth check is good. But: no confirmation gates for destructive ops (backup:restore, environment:delete, db:sql), no dry-run guidance, no secrets handling rules, no input validation. |
| 10 | Spec Compliance & Maintainability | 2 | Has valid frontmatter (name, description, version), consistent structure, clear references. Missing: author, tags, or changelog. |

## Detailed Findings

### 1. Metadata Precision (2)

**Evidence:** Frontmatter `name: upsun`, `description: "This skill should be used when the user asks to 'deploy to Upsun'..."` — a long enumeration of trigger phrases.

**Failure modes:** The description is a flat keyword list rather than a semantic statement of purpose. An LLM routing system might match on substrings ("merge branch") that aren't Upsun-specific.

**Minimal fixes:**
- Restructure description as: "Manage Upsun PaaS projects via the Upsun CLI — deployments, environments, backups, scaling, databases, and access control. Use when the user mentions Upsun by name or references Upsun CLI commands."
- Add `tags: [upsun, paas, deployment, cli]` for discoverability.

### 2. Triggering Precision & Distinctiveness (2)

**Evidence:** Description lists ~12 specific trigger phrases. No "Do NOT use when" section exists.

**Failure modes:** "merge branch" or "check logs" could collide with git skills or generic logging skills. No disambiguation guidance.

**Minimal fixes:**
- Add: "Do NOT use when: the user is asking about Platform.sh (legacy), generic git operations without Upsun context, or cloud provider consoles (AWS/GCP/Azure)."
- Add disambiguation: "If unclear whether Upsun is intended, ask the user."

### 3. Scope Fit & Responsibility Size (2)

**Evidence:** 130+ commands routed across 10 reference files. "What Do You Want to Do?" section acts as a decision tree.

**Failure modes:** No explicit non-goals or escalation paths. If a user needs something the CLI can't do (e.g., billing, plan changes via web console), no guidance exists.

**Minimal fixes:**
- Add "Non-goals" section: "This skill does not cover Upsun web console operations, billing/plan management, or Platform.sh migration."
- Add escalation: "For operations not available via CLI, direct users to the Upsun web console or `upsun docs`."

### 4. Context Economy (2)

**Evidence:** Main file is a routing layer; deep content lives in reference files. Good use of links and tables.

**Failure modes:** The "What Do You Want to Do?" section and "Command Discovery" table partially duplicate each other. The overview bullet list repeats what the ToC already conveys.

**Minimal fixes:**
- Merge the command discovery table into the "What Do You Want to Do?" section or remove the duplication.
- Trim the overview bullets to 3–4 key differentiators.

### 5. Degrees of Freedom Calibration (1)

**Evidence:** No defaults specified anywhere. No "ask vs assume" rules. No guidance on whether to auto-select project/environment or prompt the user.

**Failure modes:** Agent might execute `backup:restore` on the wrong environment without confirming. Agent might assume `-e main` when user meant a feature branch. No fallback behavior defined.

**Minimal fixes:**
- Add a "Defaults & Assumptions" section: "Always confirm project ID and environment name before executing write operations. For read-only commands, use the current directory's linked project if available."
- Add: "When multiple environments exist, list them and ask the user to choose rather than assuming."

### 6. Workflow Clarity & Progressive Disclosure (2)

**Evidence:** Quick Start → categorized sections → reference files. Good layering.

**Failure modes:** No inline multi-step workflow (e.g., "to safely deploy: 1. backup, 2. push, 3. verify, 4. rollback if needed"). References `examples/deploy-workflow.sh` but the agent must read a separate file to understand the flow.

**Minimal fixes:**
- Add a 4–5 step "Safe Deployment Checklist" inline in the Quick Start or Deploy section.
- Add a "Backup Before Destructive Operations" reminder at the top of relevant sections.

### 7. Verifiability & Feedback Loops (1)

**Evidence:** No success criteria defined. No validation commands after operations. `activity:list` and `activity:log` are mentioned but not positioned as verification steps.

**Failure modes:** Agent deploys but doesn't verify. Backup created but never confirmed. Restore succeeds silently without health check.

**Minimal fixes:**
- After each major operation category, add a "Verify" step: e.g., after deploy → `activity:log` to confirm success; after backup:create → `backup:list` to confirm it appears.
- Add: "After any write operation, verify the result before reporting success to the user."

### 8. Operational Hygiene (2)

**Evidence:** Version pinned to v5.6.0+, auth check with `auth:info`, install link provided. Permission configuration section is thorough.

**Failure modes:** No cleanup guidance (e.g., deleting stale feature environments). No idempotency notes (e.g., running `backup:create` twice). No resource usage warnings (e.g., tunnel left open).

**Minimal fixes:**
- Add: "Close tunnels after use with `tunnel:close`. Deactivate test environments when done."
- Add idempotency notes: "Most create operations are not idempotent — check if the resource exists before creating."

### 9. Safety & Security (1)

**Evidence:** Auth check exists. Permission tiers (minimal/standard/full) are well-structured. But no confirmation gates for destructive operations.

**Failure modes:** Agent could run `backup:restore` overwriting production data without confirmation. `environment:delete` could destroy an active environment. `db:sql` with arbitrary SQL could cause data loss. API tokens could be logged in conversation.

**Minimal fixes:**
- Add a "Destructive Operations" section: "ALWAYS confirm with the user before executing: `backup:restore`, `environment:delete`, `environment:merge` (to main), `db:sql` (write queries), `resources:set`, `user:delete`."
- Add: "Never display or log API tokens. Use `--no-interaction` only for read-only commands."
- Add: "For `db:sql`, prefer read-only queries. Require explicit user confirmation for any INSERT/UPDATE/DELETE/DROP."

### 10. Spec Compliance & Maintainability (2)

**Evidence:** Valid YAML frontmatter with name, description, version. Consistent markdown structure with headers, tables, and code blocks. Reference file organization is clean.

**Failure modes:** No changelog or last-updated date. No author field. If reference files change, the main skill could become stale.

**Minimal fixes:**
- Add `author` and `updated` fields to frontmatter.
- Add a brief changelog section or version note at the bottom.

## Blocker Verdict

**BLOCKED — Safety/Ops Gate**

The skill uses CLI tools with significant side effects (environment deletion, backup restore, database SQL execution, resource scaling) but **#9 Safety & Security = 1** (required ≥ 2). The absence of confirmation gates for destructive operations and lack of input validation for SQL commands create unacceptable risk for production environments.

**Required to unblock:** Raise #9 to ≥ 2 by adding destructive operation guards, confirmation requirements, and secrets handling guidance.

## Top 7 Issues

1. **No destructive operation guards** (#9) — `backup:restore`, `environment:delete`, `db:sql` can be executed without user confirmation, risking production data loss.
2. **No confirmation/ask-vs-assume rules** (#5) — Agent may auto-select wrong project or environment for write operations.
3. **No verification steps after operations** (#7) — Success is never validated; failures may go undetected.
4. **No secrets handling rules** (#9) — API tokens could be echoed or logged in conversation.
5. **No inline safe deployment workflow** (#6) — The critical multi-step deploy flow is hidden in a script file rather than presented inline.
6. **No non-goals or escalation paths** (#3) — No guidance for operations outside CLI scope.
7. **No "Do NOT use when" triggers** (#2) — Ambiguous triggers like "merge branch" or "check logs" could misfire.

## Patch Plan

1. **Add "Destructive Operations" safety section** — List all destructive commands, require user confirmation before execution, mandate backup-before-restore.
2. **Add "Defaults & Assumptions" section** — Define ask-vs-assume rules for project/environment selection on write vs. read operations.
3. **Add verification steps** — After each major operation category, specify the command to confirm success.
4. **Add secrets handling rule** — "Never display API tokens. Never pass secrets as CLI arguments visible in process lists."
5. **Add "Do NOT use when" boundary** — Disambiguate from git-only, Platform.sh, and cloud console tasks.
6. **Add inline Safe Deployment Checklist** — 5-step checklist: backup → push → monitor activity → verify → rollback path.
7. **Add non-goals and escalation section** — Billing, web console ops, Platform.sh migration.
8. **Add cleanup/idempotency guidance** — Close tunnels, check-before-create patterns.
9. **Trim description to semantic statement** — Replace keyword list with concise purpose + "Use when user mentions Upsun."
10. **Merge duplicate command discovery sections** — Combine "What Do You Want to Do?" routing and the command table.
11. **Add `author`, `updated` to frontmatter** — Improve maintainability.
12. **Add `db:sql` input validation rule** — Require read-only default; explicit confirmation for writes.

## Targeted Rewrite

### Rewrite 1: New "Safety & Destructive Operations" Section (insert after "Prerequisites")

```markdown
## Safety Rules

### Destructive Operations — Always Confirm

Before executing any of the following commands, **always explain what will happen and get explicit user confirmation**:

| Command | Risk | Required Safeguard |
|---------|------|--------------------|
| `backup:restore` | Overwrites environment data | Create a fresh backup first; confirm target environment |
| `environment:delete` | Destroys environment and data | Confirm environment name; verify it's not production |
| `environment:merge` (to main/production) | Modifies production | Create backup; confirm merge source and target |
| `db:sql` (write queries) | Modifies database directly | Show the SQL to the user; require explicit approval for INSERT/UPDATE/DELETE/DROP |
| `resources:set` | Changes resource allocation | Show current vs. proposed; confirm cost implications |
| `user:delete` | Removes user access | Confirm username and project |

### Secrets & Credentials

- **Never display API tokens** in conversation output. If `auth:api-token-login` is needed, instruct the user to enter it directly.
- **Never pass secrets as inline CLI arguments** where they may appear in process lists or logs. Use environment variables or stdin where supported.
- Prefer the minimal permission scope from the Permission Configuration section.

### Defaults & Assumptions

- **Read-only commands**: Use the current directory's linked project/environment if available. Fall back to asking the user.
- **Write commands**: Always confirm the target project ID and environment name before executing — never assume.
- **When multiple environments exist**: List them and ask the user to choose.
- **Default to `--no-interaction` only for read-only operations.**

### Verification After Operations

After any write operation, verify the result before reporting success:

- After deploy → `upsun activity:log` to confirm completion
- After backup:create → `upsun backup:list` to confirm backup appears
- After environment:merge → check target environment status with `environment:info`
- After resources:set → `upsun resources:get` to confirm new allocation
- After user:add/delete → `upsun user:list` to confirm change
```

### Rewrite 2: Revised Frontmatter Description

```yaml
---
name: upsun
description: >
  Manage Upsun PaaS projects via the Upsun CLI (v5.6.0+) — deployments,
  environments, backups, resource scaling, databases, and access control.
  Use when the user mentions Upsun by name, references Upsun CLI commands,
  or asks to deploy/manage environments on Upsun. Do NOT use for generic
  git operations without Upsun context, Platform.sh (legacy) commands,
  cloud provider consoles (AWS/GCP/Azure), or Upsun billing/plan management
  (direct users to the Upsun web console for those).
version: 1.0.1
author: [maintainer]
updated: 2025-02-22
tags: [upsun, paas, deployment, cli, cloud]
---
```

### Rewrite 3: Inline Safe Deployment Checklist (replace the Deploy & Manage Code subsection)

```markdown
### Deploy & Manage Code

#### Safe Deployment Checklist

1. **Backup current state**: `upsun backup:create -p PROJECT_ID -e ENVIRONMENT_NAME`
2. **Push changes**: `upsun push -p PROJECT_ID -e ENVIRONMENT_NAME`
3. **Monitor deployment**: `upsun activity:log -p PROJECT_ID -e ENVIRONMENT_NAME` — wait for "complete" status
4. **Verify**: Check the environment URL (`upsun environment:url`) and confirm the application works
5. **Rollback if needed**: `upsun backup:restore -p PROJECT_ID -e ENVIRONMENT_NAME` (confirm with user first)

#### Commands

- **Deploy changes**: `environment:push`, `environment:deploy`, `environment:redeploy`
- **Create test environment**: `environment:branch`
- **Merge changes**: `environment:merge` ⚠️ confirm target before merging to production
- **Monitor deployments**: `activity:list`, `activity:log`

→ See [references/deployments.md](references/deployments.md) for advanced deployment patterns
→ Use `examples/deploy-workflow.sh` for scripted deployment workflow
```