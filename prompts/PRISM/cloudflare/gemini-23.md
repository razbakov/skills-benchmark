## Skill Review: cloudflare

### Summary

The `cloudflare` skill is a high-quality "Router" skill that excels in **Context Economy**, acting as a lightweight traffic controller for a massive ecosystem. However, it currently suffers from a **Phase 3 (Execution)** weakness: it lacks any actual instructions or workflow logic, serving only as a directory. While it organizes information brilliantly, it doesn't tell the agent *how* to build, validate, or deploy anything.

### Scores

| # | Phase | Dimension | Score | Key Finding |
| --- | --- | --- | --- | --- |
| 1 | Discovery | Triggering Precision | 5/5 | Excellent "pushy" description with specific product mentions and clear use cases. |
| 2 | Context Load | Context Economy | 5/5 | Masterclass in progressive disclosure; uses decision trees to point to external refs. |
| 3 | Context Load | Degrees of Freedom | 3/5 | Appropriate for a router, but lacks "default" patterns for common cross-product tasks. |
| 4 | Execution | Instruction Clarity | 2/5 | There are no instructions; the agent is left to guess how to "load" or use the refs. |
| 5 | Execution | Actionability & Workflow | 1/5 | No plan-validate-execute loops or concrete success criteria provided. |
| 6 | Trust | Safety & Reversibility | 2/5 | No mention of destructive ops (Wrangler delete, KV purge) or confirmation gates. |
| 7 | Lifecycle | Verifiability | 1/5 | No success criteria or validation scripts defined to check if a deployment worked. |
| 8 | Lifecycle | Operational Hygiene | 4/5 | Well-structured YAML and paths, though missing explicit `wrangler` dependency info. |
|  |  | **Composite** | **23/40** |  |

### Phase Gate Assessment

> ⚠️ **Phase gate failure: Execution**. Scores in later phases are unreliable until this is resolved. **Instruction Clarity** scored **2/5** — The skill provides a map but no engine. An agent triggered by this skill knows where to look for data but has no instructions on the sequence of operations required to build a Cloudflare project.

---

### Detailed Findings

**4. Instruction Clarity (Execution) — 2/5**

* **Evidence:** The file ends after the "Other Services" table. There is no `# Instructions` or `# Workflow` section.
* **Impact:** When the skill triggers, the agent sees a list of files but doesn't know if it should read them all, copy-paste from them, or run a specific initialization command. It leads to "hallucinated workflows" where the agent guesses the steps.
* **Fix:** Add a "General Workflow" section: "1. Identify product via Decision Tree. 2. Read the specific reference file. 3. Initialize project using `npx wrangler init`. 4. Configure `wrangler.toml` based on the reference."

**5. Actionability & Workflow Design (Execution) — 1/5**

* **Evidence:** No mention of `wrangler`, no error handling patterns, and no feedback loops for deployment.
* **Impact:** The agent might try to deploy code that hasn't been type-checked or authenticated, leading to a high failure rate in multi-turn tasks.
* **Fix:** Implement a "Validation Loop": "Before recommending a deployment, verify the `wrangler.toml` structure and run `npx wrangler types` to catch binding errors."

**6. Safety & Reversibility (Trust) — 2/5**

* **Evidence:** The skill covers WAF and Security products but doesn't protect the user from its own actions (e.g., deleting a D1 database or an R2 bucket).
* **Impact:** An agent might accidentally execute a destructive command like `wrangler kv:namespace delete` without a human-in-the-loop confirmation.
* **Fix:** Add a "Safety Rails" section: "Always require explicit human confirmation before executing `delete`, `bulk-put`, or `secret put` commands."

---

### Top 3 Fixes (Priority Order)

1. **Instruction Clarity**: Add a `# Standard Operating Procedure` section immediately following the decision trees. Detail the 3-step process: (1) Route to reference, (2) Validate environment via `wrangler whoami`, (3) Execute local development before deployment.
2. **Actionability**: Add a `# Common Tools` section that explicitly defines `wrangler` as the primary CLI tool, providing the install command (`npm install -g wrangler`) and a "Dry Run" requirement for configuration changes.
3. **Verifiability**: Define a "Success Definition" for the agent. Example: "A task is successful only if `wrangler deploy --dry-run` passes and the generated `wrangler.toml` includes all necessary resource bindings identified in Phase 1."

Would you like me to rewrite the **Instruction Clarity** section for you to include a standardized `wrangler` workflow?
