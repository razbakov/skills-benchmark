## Summary

This SKILL.md is a broad “Upsun CLI management” playbook: it activates when a user wants to deploy, manage environments/projects, handle backups/restores, scale resources, configure domains/variables, or otherwise administer Upsun via the Upsun CLI (v5.6.0+). It provides a quick-start set of common commands and a categorized command map with pointers to reference docs/scripts, plus example permission scopes for `.claude/settings.local.json`. Key constraints include being authenticated before running commands and using permission scoping to restrict CLI operations.

## CRN Scorecard

| dimension                         | score | 1-line rationale                                                                                                                                                 |
| --------------------------------- | ----: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1) Triggering Precision           |     2 | Many triggers and tasks are listed, but “should be used when…” is extremely broad and lacks explicit non-triggers/conflict resolution.                           |
| 2) Degrees of Freedom Calibration |     1 | Commands are shown, but critical decision points (when to ask vs proceed, safe defaults, exact workflows) are mostly deferred to missing references/scripts.     |
| 3) Context Economy                |     2 | Scannable headings and lists, but a lot of marketing/coverage claims (“130+ commands”) and repetitive command lists inflate the file.                            |
| 4) Verifiability                  |     1 | Some “check status” commands exist, but there are no success criteria, validation checkpoints, or “how to confirm it worked” per operation.                      |
| 5) Reversibility / Safety         |     1 | Mentions “safety checks” and permissions, but lacks explicit safeguards for destructive actions (restore/delete/merge/scale) and rollback guidance.              |
| 6) Generalizability               |     2 | Organized by common admin tasks and broadly applicable, but assumes a Claude-specific permissions model and references external files without in-file fallbacks. |

## Detailed Findings

### 1) Triggering Precision — **Score: 2**

**Evidence**

* Frontmatter trigger definition is expansive:
  “*This skill should be used when the user asks to ‘deploy to Upsun’… or mentions Upsun CLI commands… deployment workflows, backup/restore operations, resource scaling, or Upsun project administration.*”
* The body reiterates broad scope: “*Comprehensive skill for managing Upsun… covers… deployment workflows… environment management… backup and restore… resource scaling… database operations… security and access…*”

**Risks / failure modes**

* Over-triggers for adjacent tools (e.g., generic “deploy”, “check logs”, “manage database”) where the user might mean another platform (Kubernetes, Heroku, Platform.sh, AWS, etc.).
* Under-specifies *non-use* cases: reading docs, debugging app code, CI/CD pipeline changes, or tasks that don’t require CLI execution might still trigger.
* Conflicts with other “deployment” skills because it doesn’t define disambiguation questions or signal phrases unique to Upsun (project IDs, env names, `upsun` command prefix).

**Fixes (minimal edits to raise to 3)**

* Add an explicit **“Do not use when”** subsection under Overview or in frontmatter description:

  * Not Upsun / user doesn’t mention Upsun explicitly
  * Requests are about app code changes, CI configuration, or infra not managed by Upsun CLI
* Add a **disambiguation rule**: if user says “deploy” without “Upsun/upsun CLI/project/environment”, ask one short clarifier *or* propose both interpretations.
* Add 3–5 **distinctive trigger examples** and 2–3 **non-trigger examples**.

---

### 2) Degrees of Freedom Calibration — **Score: 1**

**Evidence**

* Many commands use placeholders but don’t define how to obtain them:
  “`upsun environments -p PROJECT_ID`”, “`upsun push -p PROJECT_ID -e ENVIRONMENT_NAME`”
* “*All helper scripts in this skill include automatic authentication checking.*” (but no scripts are included here; it points to `examples/deploy-workflow.sh`, `scripts/resource-audit.sh`)
* Defers specifics: “→ See [references/...]*” throughout.

**Risks / failure modes**

* The agent may run the wrong command variant (`environment:*` vs `upsun push`) or operate on wrong project/environment because the file doesn’t prescribe a mandatory “identify target” step.
* Users may not know required inputs (project ID, env name) or how to safely select them (production vs staging).
* “Permissions” examples are present but not tied to concrete guidance (e.g., “start read-only; escalate only if needed”).

**Fixes (minimal edits to raise to 2)**

* Add a short **“Default workflow”** snippet at top of each destructive category (deploy/merge/restore/delete/scale):

  * 1. `upsun auth:info`
  * 2. list/select project + env
  * 3. preview/status command
  * 4. perform action
  * 5. verify
* Add a **“When to ask vs proceed”** rule:

  * If `PROJECT_ID` / `ENVIRONMENT_NAME` not provided, ask for them or run `upsun projects` + `upsun environments -p …` and present choices.
* Clarify **canonical command names** (there’s inconsistency: Quick Start uses `upsun push`, while later sections use `environment:push` etc.).

---

### 3) Context Economy — **Score: 2**

**Evidence**

* Structured and scannable with headings and categorized lists.
* Some bloat/repetition:

  * “*130+ CLI commands across 30 namespaces*”
  * Multiple sections listing similar command groups (Deploy/Manage Code, Manage Environments, Development Tools) with overlapping items like logs (`upsun logs` vs `environment:logs`).

**Risks / failure modes**

* Readers may miss the few critical rules (auth + safe ops) buried under a lot of catalog content.
* Catalog-style lists without operational guidance can encourage “command roulette”.

**Fixes (minimal edits to raise to 3)**

* Move the “130+ commands / multi-cloud / marketing” sentences to a short note or remove them.
* Deduplicate logs/ssh/tunnel command references; keep one canonical command name per action.
* Add a compact **“Safety & Verification First”** block near the top; keep catalogs in references.

---

### 4) Verifiability — **Score: 1**

**Evidence**

* Has some checks:
  “Check authentication status: `upsun auth:info`” and “Check your version: `upsun --version`”
* Lacks per-operation success criteria; e.g., deploy: no “check activity log shows success” guidance.

**Risks / failure modes**

* The agent/user may assume success after running a command without confirming deployment completion, environment health, or backup integrity.
* Restore/delete/merge/scale could partially fail, leaving system in inconsistent state, without “what to check next.”

**Fixes (minimal edits to raise to 2)**

* Add a **Verification** subsection per major workflow with observable checks, e.g.:

  * Deploy: `activity:list`, `activity:log`, `environment:info`, `environment:url`
  * Backup: `backup:get`, verify timestamp/size, restore test to non-prod
  * Scale: `resources:get` before/after
* Add a simple **“Definition of done”** bullet list for deploy/backup/restore.

---

### 5) Reversibility / Safety — **Score: 1**

**Evidence**

* Mentions “*Backup and restore operations with safety checks*” (but no explicit checks described).
* Provides a permissions model with “Minimal/Standard/Full” examples and says “See … for detailed permission scope information.”

**Risks / failure modes**

* Destructive actions listed without guardrails:

  * `backup:restore`, `backup:delete`, `environment:delete`, `environment:merge`, `resources:set`
* No “dry-run”, “confirm target”, “avoid production”, “roll back/undo” guidance.
* Permissions section could be misused by granting `Bash(upsun *:*)` as a default rather than least privilege.

**Fixes (minimal edits to raise to 2)**

* Add a **Safety rules** section:

  * Always confirm target project/env, especially production
  * Create backup before merge/restore/scale changes
  * Prefer performing restores into a new branch env first
  * Require explicit user confirmation for delete/restore/merge/scale
* Add rollback notes:

  * For deployments: redeploy previous commit (if applicable) or restore backup
  * For scaling: record previous resource settings and revert with `resources:set` to prior values
* Tie permissions to least privilege: start read-only; escalate only when needed.

---

### 6) Generalizability — **Score: 2**

**Evidence**

* Covers broad operational areas and uses placeholders (`PROJECT_ID`, `ENVIRONMENT_NAME`) rather than hard-coded org specifics.
* However, it assumes a Claude-specific permissions file path: “Add Upsun permissions to `.claude/settings.local.json`” and references many local files that aren’t included.

**Risks / failure modes**

* In environments where `.claude/settings.local.json` isn’t used (different agent runtime), this guidance is irrelevant or confusing.
* Missing reference files make the skill less portable; users can’t follow links in contexts where those files don’t exist.

**Fixes (minimal edits to raise to 3)**

* Add a note: “If your agent runtime doesn’t use `.claude/settings.local.json`, adapt permission scoping to your environment.”
* Provide minimal in-file fallbacks for the most critical workflows (deploy, backup/restore, scale) so the skill works even without external references.

---

## Top 5 Issues (ranked)

1. **No explicit safety/confirmation gates** for destructive operations (restore/delete/merge/scale).
2. **Weak verifiability**: lacks “how to confirm success” per workflow, encouraging silent failure.
3. **Over-broad triggering**: likely to activate for generic “deploy/logs/database” requests not specific to Upsun.
4. **Inconsistent command taxonomy** (`upsun push` vs `environment:push` etc.), risking wrong commands.
5. **Critical details deferred to missing references/scripts**, leaving the core skill underspecified.

## Patch Plan (≤10 items)

1. Add a **“Do not use when / Disambiguation”** block under Overview.
2. Add a **“Safety rules (hard requirements)”** block: confirmation + backup-before-change + avoid prod defaults.
3. Add **explicit confirmation requirement** for: restore, delete, merge, resources:set/autoscaling:set.
4. Add **verification steps** for deploy/backup/restore/scale with concrete commands.
5. Add a **default workflow**: identify target → preview → execute → verify.
6. Normalize to **one canonical command name** per action; mention aliases once.
7. Add “how to get PROJECT_ID / ENVIRONMENT_NAME” guidance (list projects/envs and choose).
8. Rework permissions section to emphasize **least privilege** and escalation path.
9. Replace “safety checks” vague wording with 2–3 explicit checks.
10. Trim marketing/repetition (keep the file focused on operational guidance).

## Targeted Rewrite (most critical 1–2 sections only)

### Rewrite 1: Frontmatter `description` (more precise triggering + non-triggers)

```yaml
description: Use this skill ONLY when the user explicitly mentions Upsun/Upsun CLI (e.g., `upsun ...`) or asks for Upsun project/environment operations (deploy, branch/merge/sync envs, backups/restores, scaling/resources, domains/routes/variables, logs/SSH/tunnels) that are performed via the Upsun CLI.
  Do NOT use for generic “deploy/logs/database” requests unless Upsun is confirmed; in that case, ask a brief clarifying question (“Is this for Upsun, and which project/environment?”).
  For any destructive action (restore/delete/merge/scale), require explicit confirmation of target project + environment before running commands.
```

### Rewrite 2: Add/replace a short “Safety & Verification” section (insert after Quick Start)

```md
## Safety & Verification (Required)

**Always identify the target first** (if not provided):
- List projects: `upsun projects`
- List environments: `upsun environments -p PROJECT_ID`
- Confirm the intended environment (especially production) before proceeding.

**Require explicit confirmation before destructive actions**:
- `backup:restore`, `backup:delete`, `environment:delete`, `environment:merge`, `resources:set`, `autoscaling:set`

**Default safe workflow**:
1) Confirm auth: `upsun auth:info`
2) Inspect current state (project/env info): `upsun environment:info -p PROJECT_ID -e ENVIRONMENT_NAME`
3) Take a backup before major changes: `upsun backup:create -p PROJECT_ID -e ENVIRONMENT_NAME`
4) Execute the change
5) Verify success:
   - Deploy/merge: `upsun activity:list -p PROJECT_ID -e ENVIRONMENT_NAME` then `upsun activity:log ACTIVITY_ID`
   - Backup/restore: `upsun backup:get -p PROJECT_ID -e ENVIRONMENT_NAME BACKUP_ID`
   - Scaling: `upsun resources:get -p PROJECT_ID -e ENVIRONMENT_NAME` (compare before/after)
```

If you want, I can also propose a compact “Deploy workflow” section that resolves the `upsun push` vs `environment:push` naming inconsistency while keeping edits minimal.
