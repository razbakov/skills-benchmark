## Skill Review: cloudflare

### Summary

The earliest weakness is **Phase 1 (Discovery)**: the description is accurate but **over-broad** (“any Cloudflare development task”), which will cause **false positives** and conflicts with adjacent skills (e.g., generic web dev, Terraform, security/WAF, AI agents). The primary strength is **Context Economy**: the decision-tree routing pattern is strong, keeps the top-level doc lean, and points to references for progressive disclosure.

### Scores


| #   | Phase        | Dimension                | Score     | Key Finding                                                                                                                                                                             |
| --- | ------------ | ------------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Discovery    | Triggering Precision     | 3/5       | Good scope labeling, but description is too universal (“any Cloudflare development task”), raising conflict/false-trigger risk; also YAML `references` list is incomplete vs the index. |
| 2   | Context Load | Context Economy          | 4/5       | Strong routing + progressive disclosure; however, the Product Index is large and partially redundant with decision trees.                                                               |
| 3   | Context Load | Degrees of Freedom       | 3/5       | Appropriate high-level routing, but lacks “defaults + escape hatches” (e.g., D1 vs Hyperdrive vs DO storage) and doesn’t lock down fragile ops.                                         |
| 4   | Execution    | Instruction Clarity      | 3/5       | Clear decision trees, but unclear operational instruction for loading references and missing referenced directories (e.g., `workflows/`, `containers/`) in YAML.                        |
| 5   | Execution    | Actionability & Workflow | 2/5       | No plan→validate→execute loops, checklists, or examples; it routes but doesn’t provide executable workflows.                                                                            |
| 6   | Trust        | Safety & Reversibility   | 2/5       | No guardrails for destructive actions (Terraform applies, data migration, WAF/rules changes) or human-confirmation requirements.                                                        |
| 7   | Lifecycle    | Verifiability            | 1/5       | No success criteria, test fixtures, validation steps, or evaluation prompts; cannot measure correctness over time from this doc alone.                                                  |
| 8   | Lifecycle    | Operational Hygiene      | 3/5       | Frontmatter is present and mostly compliant; however, reference inventory is inconsistent (frontmatter vs index), and no dependency/runtime assumptions are documented.                 |
|     |              | **Composite**            | **21/40** |                                                                                                                                                                                         |


### Phase Gate Assessment

> ⚠️ **Phase gate failure: Execution**. Scores in later phases are unreliable until this is resolved. **Actionability & Workflow Design** scored **2/5** — the skill primarily routes to product folders but doesn’t give an agent an executable workflow with checkpoints, so real tasks will stall or become improvisational.

### Detailed Findings

**1. Triggering Precision (Discovery) — 3/5**

- **Evidence:** Frontmatter description ends with: “**Use for any Cloudflare development task.**”
- **Impact:** In a multi-skill pool, this will **over-trigger** on generic web dev (“deploy my site”), generic IaC (“write Terraform”), generic security (“configure WAF”), or generic AI agent work—stealing routing from more specialized skills and degrading tool selection.
- **Fix:** Rewrite description to be more selective and phrase-triggered, e.g.:
  - **Replace with:** “Cloudflare platform routing + build guidance for Workers/Pages/D1/R2/Vectorize/WAF/Tunnel and related products. Use when the user mentions *Cloudflare* or any of: Workers, Pages, Wrangler, Durable Objects, D1, R2, KV, Vectorize, Turnstile, WAF, Argo/Tunnel/Spectrum, Hyperdrive, Workers AI, or when deploying to the Cloudflare edge.”
  Also ensure the YAML `references` list reflects the real reference tree (see OH).

**2. Context Economy (Context Load) — 4/5**

- **Evidence:** “**Use decision trees below to find the right product, then load detailed references.**” and extensive use of `→ product/` routing.
- **Impact:** This is close to the Cloudflare-style “router skill” ideal. The main bloat risk is the **large Product Index** duplicating what the decision trees already cover, increasing tokens on every invocation.
- **Fix:** Keep decision trees + add a *minimal* index (or collapse the index into a short “Top products” list). Move the full index to a reference file (e.g., `references/index.md`) loaded only when asked “what products are supported?”

**3. Degrees of Freedom Calibration (Context Load) — 3/5**

- **Evidence:** Storage tree includes: “**Relational SQL → d1/ (SQLite) or hyperdrive/ (existing Postgres/MySQL)**” and “**Strongly-consistent per-entity state → durable-objects/**” without a default decision rubric.
- **Impact:** Agents will frequently choose the wrong product in ambiguous cases (e.g., using D1 when DO storage is better for per-entity consistency, or using KV for data that needs queryability). Also, there are no “low-freedom” exact sequences for fragile ops (migrations, Terraform apply, security rule rollout).
- **Fix:** Add small “default rules” next to ambiguous branches (one line each), e.g.:
  - “Default to **D1** for simple relational needs; use **Hyperdrive** when you already have Postgres/MySQL; use **Durable Objects** when you need per-entity strong consistency + coordination.”
  - Add a “Fragile operations” section that mandates exact sequences (plan → review → apply) and confirmation steps.

**4. Instruction Clarity (Execution) — 3/5**

- **Evidence:** Frontmatter `references:` lists only `workers`, `pages`, `d1`, `durable-objects`, `workers-ai`, but decision trees/index reference many more (`workflows/`, `containers/`, `kv/`, `r2/`, `vectorize/`, `tunnel/`, etc.).
- **Impact:** Depending on the loader behavior, this mismatch can cause **broken reference loading** or incomplete context—agent routes to folders that aren’t declared (or don’t exist), leading to “can’t find file” failures.
- **Fix:** Make the reference inventory consistent:
  - Either (A) include **all** supported references in YAML (if required by your skill system), or
  - (B) remove `references:` from frontmatter and rely on the in-doc routing index (if discovery ignores that field), or
  - (C) split into multiple router skills by domain (compute/storage/security/AI) to reduce reference sprawl.

**5. Actionability & Workflow Design (Execution) — 2/5**

- **Evidence:** The skill provides routing (“Need storage? → kv/… d1/…”) but no “how to do the thing” workflow (no steps for deploying, debugging, configuring, validating).
- **Impact:** Real user tasks (“set up D1 migrations”, “deploy Workers with env vars”, “configure WAF rules safely”) will force the agent to improvise, increasing failure rate and inconsistency.
- **Fix:** Add 2–3 canonical workflows at the router level (short, copy/paste checklists), e.g.:
  - “Workers deploy workflow: scaffold → configure wrangler → run locally → deploy → verify routes/logs”
  - “Storage selection + schema workflow: choose store → define data model → access pattern → migration strategy → validation”
  - Each workflow should end in a checkpoint (“deployment succeeded if …”).

**6. Safety & Reversibility (Trust) — 2/5**

- **Evidence:** IaC section: “**Need IaC? → pulumi/ … terraform/ …**” with no mention of plan/approve, rollback, or confirmation. Security section includes WAF/DDoS/Bot without rollout safety.
- **Impact:** An agent following this skill could recommend or execute destructive actions (Terraform apply, rule changes) without gating, causing outages or irreversible data loss.
- **Fix:** Add explicit guardrails at the router level:
  - Require **human confirmation** before any “apply/push/enable” actions affecting production.
  - Require backups/checkpoints before migrations and config changes.
  - Prohibit unsafe flags (`--force`, `--skip-validation`) unless justified and paired with rollback steps.

**7. Verifiability (Lifecycle) — 1/5**

- **Evidence:** No success criteria, validators, or examples anywhere—only routing and index tables.
- **Impact:** You can’t evaluate whether the skill “worked.” There’s no way to create regression tests or measure improvements; you’ll rely on subjective outcomes.
- **Fix:** Add an “Evaluation & acceptance checks” section with 3–5 concrete test prompts and pass/fail criteria, e.g.:
  - “Given a Worker + KV session store, output must include wrangler config + binding declaration + local test steps.”
  - “For Terraform changes, output must include `terraform plan` summary + explicit user confirmation request before apply.”
  - If you have scripts in references, specify validators by name and expected output.

**8. Operational Hygiene (Lifecycle) — 3/5**

- **Evidence:** Frontmatter is present with `name` and `description` and the name `cloudflare` is well-formed; but `references:` is **incomplete** relative to the doc’s stated reference paths.
- **Impact:** Incomplete reference declarations can cause runtime loading drift, documentation rot, and silent failures when new references are added but not registered.
- **Fix:** Introduce a single source of truth:
  - Generate the Product Index from the reference directory automatically (or maintain a short “supported references” list in YAML and derive the index).
  - Add a “Supported runtime assumptions” note (e.g., Node/Python versions, wrangler availability) in the appropriate reference folders, not in the router.

### Top 3 Fixes (Priority Order)

1. **Actionability & Workflow Design**: Add 2–3 router-level canonical workflows with checkpoints (deploy, storage selection/migrations, IaC change process), using plan→validate→execute loops.
2. **Verifiability**: Add an “Eval / acceptance criteria” section with at least 3 concrete test prompts + pass/fail checks (especially for Terraform/WAF/production changes).
3. **Triggering Precision / Operational Hygiene**: Rewrite the description to avoid “any Cloudflare task” and align YAML `references` with the actual reference set (or remove/replace the field with a single source of truth).

