## CRN Review: cloudflare

### Summary

This is the gold-standard example of progressive disclosure architecture — a lean routing layer covering 60+ products via decision trees that direct agents to the right reference files. Its biggest strength is Context Economy; its most critical weakness is the description field, which is too long and lacks negative trigger boundaries, risking both over-triggering and context waste when loaded unnecessarily.

### Scores


| Dimension              | Score     | Key Finding                                                                                                                                                                  |
| ---------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Triggering Precision   | 3/5       | Description is a product catalog dump rather than a trigger-optimized selector; no negative boundaries to prevent false activation.                                          |
| Degrees of Freedom     | 4/5       | Decision trees correctly constrain product selection (low-DoF routing) while leaving implementation open to reference files.                                                 |
| Context Economy        | 5/5       | Textbook progressive disclosure — SKILL.md is a pure router (~180 lines), all implementation detail deferred to reference directories.                                       |
| Verifiability          | 2/5       | No success criteria, no validation steps, no examples of expected input/output for any decision path.                                                                        |
| Reversibility / Safety | 3/5       | No destructive operations in the routing layer itself, but no guidance on safety patterns to follow when loading referenced skills (e.g., D1 migrations, Terraform applies). |
| Generalizability       | 4/5       | Platform-agnostic routing, no time-sensitive content, consistent terminology. Minor: `references` YAML field lists only 5 of 60+ products.                                   |
| **Composite**          | **21/30** |                                                                                                                                                                              |


### Detailed Findings

**Triggering Precision (3/5)**

- Problem: The description is 223 characters and reads like marketing copy: `"Comprehensive Cloudflare platform skill covering Workers, Pages, storage (KV, D1, R2), AI (Workers AI, Vectorize, Agents SDK), networking (Tunnel, Spectrum), security (WAF, DDoS), and infrastructure-as-code (Terraform, Pulumi). Use for any Cloudflare development task."` It enumerates products but doesn't specify negative triggers — when should an agent *not* load this skill?
- Impact: In a pool with a generic "deploy web app" or "database" skill, the phrase "Use for any Cloudflare development task" provides no disambiguation signal. An agent might load this for any mention of "edge functions" or "KV store" even when the user means a different platform.
- Fix: Rewrite description to include trigger signals AND exclusion signals: `"Use when the user explicitly mentions Cloudflare, Wrangler, Workers, Pages, D1, R2, KV, or any Cloudflare-specific product. Do not use for generic cloud/serverless questions unless Cloudflare is specified."` Keep under 1024 chars but make every word a selection signal.

**Verifiability (2/5)**

- Problem: The skill is pure routing with no way to verify correct routing occurred. There are no examples like "User says X → load `references/workers/`" and no validation that the referenced directory actually exists or contains a valid SKILL.md.
- Impact: If a reference directory is missing or renamed, the agent silently fails. There's no way to write an eval for this skill without reverse-engineering the decision trees into test cases.
- Fix: Add 3–5 canonical routing examples as input/output pairs at the bottom of the file (e.g., `"I want to deploy a Next.js app with git" → pages/`). Optionally add a validation note: "Before loading a reference, verify the directory exists."

**Reversibility / Safety (3/5)**

- Problem: The routing layer itself is safe (read-only decisions), but it makes no mention of which downstream references involve destructive operations. An agent following the tree to `d1/` or `terraform/` has no advance warning.
- Impact: The agent treats all branches equally, with no signal to apply extra caution for D1 schema migrations, Terraform destroys, or R2 bucket deletions.
- Fix: Annotate destructive branches in the decision trees with a marker (e.g., `→ d1/ ⚠️ destructive ops`) or add a brief "Safety Notes" section listing which references contain irreversible operations.

### Top 3 Fixes (Priority Order)

1. **Rewrite the description with explicit trigger and exclusion signals.** This is the highest-leverage change — the skill is useless if it doesn't activate correctly or activates when it shouldn't. Add "Use when user mentions Cloudflare by name or a Cloudflare-specific product" and "Do not use for generic cloud questions."
2. **Add 5 canonical routing examples as input→output pairs.** This makes the skill evaluable, helps agents pattern-match ambiguous queries, and serves as a regression test if decision trees are updated.
3. **Annotate destructive branches in decision trees.** A lightweight `⚠️` marker on paths leading to D1 migrations, Terraform applies, R2 deletions, and similar operations gives the agent a safety signal before it loads the reference.

