## Summary

This SKILL.md is a **Cloudflare “router” skill**: it helps pick the right Cloudflare product (Workers, Pages, KV/D1/R2, AI, networking, security, IaC, etc.) via quick decision trees and then points to a corresponding `references/<product>/` folder. It should activate when the user is doing **Cloudflare-specific design/build/deploy/debug** work, but the current file implies it should trigger for *any* Cloudflare development task and does not define exclusions, validation, or safety guardrails.

## CRN Scorecard

| dimension                         | score | 1-line rationale                                                                                                           |
| --------------------------------- | ----: | -------------------------------------------------------------------------------------------------------------------------- |
| 1) Triggering Precision           |     1 | Very broad trigger (“Use for any Cloudflare development task”) with no “don’t use” cases or conflict avoidance.            |
| 2) Degrees of Freedom Calibration |     1 | Trees map needs→products, but ambiguous branches have no tie-breakers, and “load detailed references” is underspecified.   |
| 3) Context Economy                |     2 | Generally scannable (trees + index), but duplicated listings and very wide surface area increase maintenance noise.        |
| 4) Verifiability                  |     0 | No success criteria, checkpoints, or “how to tell it worked” guidance.                                                     |
| 5) Reversibility / Safety         |     0 | No safeguards for destructive changes, secrets, or rollback patterns—especially risky given IaC/security/networking scope. |
| 6) Generalizability               |     2 | Broad Cloudflare coverage and routing approach generalize, but assumptions (accounts, auth, environments) are unstated.    |

## Detailed Findings

### 1) Triggering Precision

**Evidence**

* Frontmatter: `description: ... Use for any Cloudflare development task.`
* Intro: “Consolidated skill for building on the Cloudflare platform.”

**Risks / failure modes**

* Over-triggers on generic web/backend questions (Node/React/SQL) even when Cloudflare isn’t central.
* Conflicts with more specific skills (e.g., Terraform-only, networking-only, security-only) because this skill claims everything.
* User intent mismatch: user asks “how do I build a REST API” → skill routes to Workers/Pages prematurely.

**Fixes (minimal edits to raise score to 2)**

* Replace “Use for any Cloudflare development task” with **explicit activation criteria** (Cloudflare chosen/evaluated; Cloudflare product mentioned; deployment/debug on Cloudflare).
* Add a short **“Do NOT use when…”** list (generic JS/TS, non-Cloudflare providers, purely conceptual networking/security not tied to Cloudflare).
* Add 2–3 trigger examples + 1–2 non-trigger examples.

---

### 2) Degrees of Freedom Calibration

**Evidence**

* “Use decision trees below to find the right product, then load detailed references.”
* Ambiguous branch: `Relational SQL → d1/ (SQLite) or hyperdrive/ (existing Postgres/MySQL)`
* Another ambiguity: `Realtime video/audio → realtimekit/ or realtime-sfu/`
* The file references many folders (e.g., `references/queues/`, `references/hyperdrive/`) but frontmatter `references:` lists only `workers, pages, d1, durable-objects, workers-ai`.

**Risks / failure modes**

* Model “chooses” between D1 and Hyperdrive incorrectly without asking about existing DB, latency, migrations, or connection pooling.
* “Load detailed references” isn’t operationalized (what to read first, what outputs to produce, when to ask clarifying questions vs proceed).
* **Broken routing / dead references** risk: index points to references that may not exist or aren’t declared in `references:`.

**Fixes (minimal edits to raise score to 2)**

* Add tie-breaker bullets under the *ambiguous* trees (e.g., D1 vs Hyperdrive; RealtimeKit vs SFU; Workers vs Pages).
* Add a “Default + ask” rule: **proceed with a default only when constraints are obvious; otherwise ask 1–3 targeted questions**.
* Align the frontmatter `references:` list with the product folders used **or** explicitly state that the index includes optional modules and references may be missing (and what to do then).

---

### 3) Context Economy

**Evidence**

* Both “Quick Decision Trees” and a large “Product Index” repeat the same surface area.
* Many sections list products that may not be needed for most tasks.

**Risks / failure modes**

* Higher maintenance burden: product additions/renames require edits in multiple places.
* Cognitive overload: users/models may scan the index instead of using the decision trees, reducing routing quality.

**Fixes (minimal edits to raise score to 3)**

* Add a one-line instruction: “Prefer decision trees first; use index only for lookup.”
* Reduce duplication by making the Product Index **only categories + links**, or remove items already covered in trees.
* Consider collapsing seldom-used categories behind a short “Other services” list.

---

### 4) Verifiability

**Evidence**

* No explicit success criteria, validation steps, or checkpoints anywhere in the file.
* No “done when…” definition.

**Risks / failure modes**

* Output can be plausible but wrong (wrong product choice, missing config, insecure defaults).
* No guidance to validate deployments, connectivity, auth, migrations, or performance.

**Missing required information**

* Success criteria per task type (deploy succeeds, route works, storage reads/writes, auth enforced, errors monitored).
* Checkpoints (local test, staging deploy, logs/metrics, rollback readiness).

**Fixes (minimal edits to raise score to 1)**

* Add a short **“Validation checklist”** section (3–8 bullets) that applies across products:

  * “Can you run a local/dev test?”
  * “Is there a staging deploy?”
  * “Are logs/metrics checked?”
  * “Do you have a rollback plan?”

---

### 5) Reversibility / Safety

**Evidence**

* IaC section: `Need IaC? → pulumi/ ... terraform/ ... api/` with no caution.
* Security/networking sections include WAF/DDoS/Tunnel/Spectrum—high-impact changes—without guardrails.
* No mention of secrets handling despite “Secrets Store”.

**Risks / failure modes**

* Potentially destructive guidance: WAF rules, routing, DNS, Tunnel exposure, Terraform apply—without “plan/dry-run/confirm”.
* Secret leakage: copying API tokens into code/logs.
* Irreversible data changes: D1 migrations without backups.

**Missing required information**

* “Safe defaults” and “confirm before destructive action”
* Rollback/undo patterns (versioned deployments, revert commits, Terraform state/plan, backup/restore for data)

**Fixes (minimal edits to raise score to 1)**

* Add a **Safety & rollback** mini-section:

  * Prefer read-only inspection first (plan/diff/logs).
  * Require explicit confirmation before destructive actions (delete, apply, purge, rule blocks).
  * Secrets handling rule (never paste secrets; use bindings/secret stores).
  * Always provide rollback steps (revert deployment/version; undo rule; restore backup).

---

### 6) Generalizability

**Evidence**

* Broad product coverage: Workers, Pages, KV/D1/R2, AI, networking, security, IaC.
* Uses need-based decision trees rather than project-specific instructions.

**Risks / failure modes**

* Hidden assumptions: auth already configured, account/zone exists, environments (dev/stage/prod), wrangler/CI usage.
* “Use for any Cloudflare dev task” is too broad; reduces portability across teams that split responsibilities (platform vs app).

**Fixes (minimal edits to raise score to 3)**

* Add a short “Assumptions to confirm” list (account/zone, environment, CI/CD vs manual, data residency, budget/limits).
* Add a “Project-independent defaults” rule (use least privilege, staging first, minimal required products).

---

## Top 5 Issues (ranked)

1. **No Safety/Reversibility guidance (score 0):** high-risk given WAF/DNS/Tunnel/IaC scope.
2. **No Verifiability guidance (score 0):** nothing defines success or validation; easy to ship broken configs.
3. **Over-broad triggering:** “Use for any Cloudflare development task” will over-trigger and conflict with other skills.
4. **Underspecified degrees of freedom on ambiguous choices:** D1 vs Hyperdrive, Workers vs Pages, RealtimeKit vs SFU, etc.
5. **Reference inconsistency risk:** many `references/<x>/` are listed, but frontmatter `references:` declares only a few.

## Patch Plan (≤10 items)

1. Update frontmatter **description** to narrow activation scope + add explicit non-goals.
2. Add a short **“When to use / When not to use”** section under the title.
3. Add a compact **“Safety & rollback”** checklist (confirm before destructive actions; secrets rule; rollback steps).
4. Add a compact **“Validation checklist”** (local test/staging/logs/metrics/basic smoke tests).
5. Add tie-breakers for the 3–5 most ambiguous branches (Workers vs Pages, D1 vs Hyperdrive, DO vs KV, RealtimeKit vs SFU, Vectorize vs KV).
6. Add a “Ask vs proceed” rule (ask 1–3 questions when constraints missing; otherwise pick default).
7. Reconcile frontmatter `references:` with the folders listed in decision trees/index **or** add a note explaining optional/undeclared modules and fallback behavior.
8. Add “Prefer decision trees first; index is lookup” to reduce scanning ambiguity.

## Targeted Rewrite (1–2 sections only)

### 1) Frontmatter (description + references note)

```yaml
name: cloudflare
description: Router skill for Cloudflare-specific build/deploy/debug tasks. Use when the user mentions Cloudflare products or explicitly wants to implement on Cloudflare; avoid for generic web/dev or non-Cloudflare infra questions.
references:
  - workers
  - pages
  - d1
  - durable-objects
  - workers-ai
  # NOTE: Keep this list aligned with any `references/<product>/` paths used below (or explicitly mark optional modules).
```

### 2) Intro section (replace the first two paragraphs under the title)

```md
Consolidated skill for building on the Cloudflare platform.

### When to use
- The user is building, deploying, migrating, debugging, or operating something **on Cloudflare** (or explicitly evaluating Cloudflare services).
- The user mentions specific Cloudflare products (Workers, Pages, KV/D1/R2, Tunnel, WAF, etc.).

### When NOT to use
- The question is generic (e.g., “how do I write an API in Node/Express?”) and not tied to Cloudflare.
- The user is committed to a different provider or only wants vendor-neutral guidance.

### How to use
1) Pick the product via the decision trees below (ask 1–3 clarifying questions if multiple branches fit).
2) Open the corresponding `references/<product>/` doc(s) and follow their constraints.
3) Include a quick validation + rollback note in the final answer (how to verify it works; how to undo).
```
