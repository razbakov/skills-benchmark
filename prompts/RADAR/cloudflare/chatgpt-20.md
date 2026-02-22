## CRN Review: cloudflare

### Summary

This is a strong “router” skill: it quickly maps user intent (“run code”, “store data”, “need AI”) to a specific Cloudflare product reference, which is exactly what helps agents stay reliable at scale. The most critical weakness is **Triggering Precision**: the description (“Use for any Cloudflare development task”) is so broad that it will both over-trigger and conflict with more specific Cloudflare sub-skills (e.g., “wrangler”, “workers”, “terraform”), reducing discovery reliability.

### Scores


| Dimension              | Score     | Key Finding                                                                                                                                                                                                            |
| ---------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Triggering Precision   | 3/5       | Description is accurate but overly broad (“any Cloudflare development task”), likely to conflict/over-trigger in a large skill pool.                                                                                   |
| Degrees of Freedom     | 4/5       | Constraint level is appropriate for a routing/index skill, but some referenced nodes lack corresponding frontmatter `references` entries, creating ambiguous execution paths.                                          |
| Context Economy        | 4/5       | Good progressive disclosure via decision trees + index, but the SKILL.md repeats the product list twice (trees + index) and includes many products not declared in `references`.                                       |
| Verifiability          | 2/5       | No success criteria, validation/checkpoints, or examples that an agent can use to confirm correct routing or reference loading.                                                                                        |
| Reversibility / Safety | 4/5       | No destructive operations or credential handling in this file; minimal injection surface. Main risk is misrouting to the wrong reference rather than unsafe actions.                                                   |
| Generalizability       | 3/5       | Paths are POSIX-style and terminology is mostly consistent, but YAML `references` list is incomplete versus the doc’s own routing/index, which will break portability across harnesses that enforce reference loading. |
| **Composite**          | **20/30** |                                                                                                                                                                                                                        |


### Detailed Findings

**Triggering Precision (3/5)**

- Problem: Frontmatter description ends with: **“Use for any Cloudflare development task.”**
- Impact: In a pool of 100+ skills, this will:
  - **Over-trigger** on any mention of Cloudflare (even when a more specific skill should be used, e.g., “Terraform Cloudflare provider”, “Wrangler config”, “D1 schema migration”).
  - **Under-trigger** when users ask indirectly (e.g., “edge functions”, “S3 compatible object storage”) because the trigger terms are not explicitly in the description.
- Fix: Rewrite the description to be a *routing/index* skill with explicit triggers and boundaries, e.g.:
  - “Routes Cloudflare platform requests to the correct product reference (Workers/Pages/D1/R2/Vectorize/WAF/Tunnel/IaC) when the user is building or debugging on Cloudflare; not for generic web dev.”

**Verifiability (2/5)**

- Problem: No testable outputs or checkpoints. The skill says: “Use decision trees below to find the right product, then load detailed references.” but provides no mechanism to verify the chosen route is correct.
- Impact: Agents can confidently pick the wrong subtree (e.g., D1 vs Hyperdrive vs Durable Objects storage) and there’s no built-in loop to detect/repair the mistake, hurting production reliability.
- Fix:
  - Add a lightweight “routing validation” step: after choosing a product, the agent should restate the inferred requirement in 1 sentence + list the chosen product + 1 alternative with why it’s not chosen.
  - Add 4–6 example prompts with expected routing outcome (input → selected reference), which can become eval cases.

**Generalizability (3/5)**

- Problem: YAML frontmatter `references` includes only:
  - `workers`, `pages`, `d1`, `durable-objects`, `workers-ai`
  But the decision trees/index point to many more directories (e.g., `workflows/`, `containers/`, `wrangler/`, `kv/`, `r2/`, `vectorize/`, `terraform/`, etc.).
- Impact: In harnesses that only allow loading declared references (common pattern), agents will attempt to load non-declared references and fail, causing brittle behavior across runtimes.
- Fix: Either:
  1. Expand frontmatter `references` to include every referenced folder used by the decision trees/index, **or**
  2. Declare a single umbrella reference (e.g., `references/`) if the framework supports it, and keep the router pointing inside it, **or**
  3. Reduce the index to only what’s actually bundled/declared.

### Top 3 Fixes (Priority Order)

1. **Tighten the `description` to a routing/index role with explicit triggers and boundaries** (reduces over-triggering/conflicts; improves discovery reliability the most).
2. **Make `references` consistent with the decision trees/index** (either add missing references or trim the index) so loading works across harnesses.
3. **Add verifiable routing examples + a simple self-check loop** (input→route pairs and a “why this product, not X” checkpoint) to enable evals and reduce silent misroutes.

