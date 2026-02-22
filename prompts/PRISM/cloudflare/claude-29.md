## Skill Review: cloudflare

### Summary

This is an exemplary routing-layer skill that serves as the reference architecture for how complex, multi-product domains should be structured. Its primary strength is Context Economy — the decision-tree routing pattern with progressive disclosure to 60+ reference directories is exactly what the schema prescribes. The weakest area is Triggering Precision: the description, while comprehensive, is long and could conflict with product-specific skills in a multi-skill environment.

### Scores


| #   | Phase        | Dimension                | Score     | Key Finding                                                                                                                                                 |
| --- | ------------ | ------------------------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Discovery    | Triggering Precision     | 3/5       | Description is comprehensive but reads as a product catalog rather than trigger-oriented; lacks specific user phrases and "pushy" trigger contexts.         |
| 2   | Context Load | Context Economy          | 5/5       | Textbook progressive disclosure — lean routing SKILL.md with decision trees pointing to 60+ reference subdirectories loaded on demand.                      |
| 3   | Context Load | Degrees of Freedom       | 4/5       | Decision trees appropriately constrain product selection (low-freedom routing) while leaving implementation details to reference files (delegated freedom). |
| 4   | Execution    | Instruction Clarity      | 4/5       | Decision trees are clear and unambiguous with consistent formatting; minor gap in explicit "load this reference" instructions.                              |
| 5   | Execution    | Actionability & Workflow | 3/5       | Routes to the right product effectively but provides no workflow pattern for what happens after loading a reference file.                                   |
| 6   | Trust        | Safety & Reversibility   | 3/5       | No safety guidance at the routing layer for destructive operations (D1 migrations, R2 deletions, DNS changes).                                              |
| 7   | Lifecycle    | Verifiability            | 3/5       | Decision trees are testable as routing logic, but no explicit success criteria or validation mechanism for correct product selection.                       |
| 8   | Lifecycle    | Operational Hygiene      | 4/5       | Clean YAML frontmatter, logical directory structure, descriptive names; `references` field in frontmatter only lists 5 of 60+ products.                     |
|     |              | **Composite**            | **29/40** |                                                                                                                                                             |


### Phase Gate Assessment

No phase gate failure — all dimensions score ≥3. The skill clears all gates, though Phase 1 (Discovery) at 3/5 represents the highest-leverage improvement area.

### Detailed Findings

**1. Triggering Precision (Discovery) — 3/5**

- **Evidence:** The description reads: *"Comprehensive Cloudflare platform skill covering Workers, Pages, storage (KV, D1, R2), AI (Workers AI, Vectorize, Agents SDK), networking (Tunnel, Spectrum), security (WAF, DDoS), and infrastructure-as-code (Terraform, Pulumi). Use for any Cloudflare development task."*
- **Impact:** This is a product feature list, not a trigger description. It doesn't include user phrases ("deploy to Cloudflare," "set up a Worker," "configure my CDN"), doesn't address when *not* to trigger (e.g., generic serverless questions that aren't Cloudflare-specific), and the catch-all "Use for any Cloudflare development task" provides no trigger granularity. In a multi-skill environment, a separate "workers" skill or "serverless" skill could easily conflict.
- **Fix:** Rewrite description to: `"Routes Cloudflare platform tasks to the correct product reference. Use when the user mentions Cloudflare, Wrangler, Workers, Pages, KV, D1, R2, Durable Objects, or any Cloudflare product by name. Also triggers for edge computing, Cloudflare deployment, or 'deploy to the edge' requests. Not for generic serverless or CDN questions unless Cloudflare is explicitly mentioned."`

**5. Actionability & Workflow (Execution) — 3/5**

- **Evidence:** The skill routes to reference directories but never describes what the agent should do after selecting a product. There's no meta-workflow like "1. Use decision tree to identify product → 2. Load the corresponding reference → 3. Follow the reference's workflow → 4. Validate the output."
- **Impact:** The agent correctly identifies "you need D1" but may not know to actually read `references/d1/` or what to look for inside it. The handoff from routing layer to execution layer is implicit.
- **Fix:** Add a 3-line "How to use this skill" section at the top: "1. Match the user's task to a product using the decision trees below. 2. Read the corresponding `references/<product>/` directory for implementation details. 3. If the task spans multiple products (e.g., Workers + D1 + R2), load each relevant reference."

**6. Safety & Reversibility (Trust) — 3/5**

- **Evidence:** No safety callouts at the routing layer. Products like D1 (database), R2 (object storage), DNS configuration, and WAF rules involve destructive or high-impact operations, but the routing skill doesn't flag these.
- **Impact:** The agent could route to a destructive product reference without any priming about caution, relying entirely on the sub-reference to contain safety guidance (which may or may not exist).
- **Fix:** Add a brief "⚠️ High-impact products" note after the decision trees: "The following products involve destructive or externally-visible operations — confirm with the user before executing changes: D1 (database writes/migrations), R2 (object deletion), WAF/DDoS (security rule changes), Tunnel (network exposure), DNS, and any `wrangler publish`/`wrangler deploy` commands."

**7. Verifiability (Lifecycle) — 3/5**

- **Evidence:** No success criteria for correct routing. The decision trees are clear enough to be testable in principle, but there's no explicit validation — e.g., "if the user says 'I need a cron job,' the correct route is `cron-triggers/`."
- **Impact:** Without test fixtures, you can't systematically evaluate whether routing accuracy improves or degrades as the skill evolves.
- **Fix:** Add a `references/routing-tests.md` file with 10–15 example user queries mapped to expected product selections. This doubles as documentation and an eval suite.

### Top 3 Fixes (Priority Order)

1. **Triggering Precision**: Rewrite the description to include specific user trigger phrases ("mentions Cloudflare, Wrangler, Workers, Pages, KV, D1..."), negative triggers ("not for generic serverless questions unless Cloudflare is mentioned"), and action-oriented language instead of the current product catalog format.
2. **Actionability & Workflow**: Add a 3-step "How to use this skill" section at the top that explicitly instructs the agent to match → load reference → execute, bridging the gap between routing and implementation.
3. **Safety & Reversibility**: Add a brief high-impact product callout listing products that involve destructive or externally-visible operations, priming the agent for caution before it loads the sub-reference.

