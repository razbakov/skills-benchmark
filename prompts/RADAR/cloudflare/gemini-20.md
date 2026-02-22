## CRN Review: cloudflare

### Summary

This skill is a masterclass in **Discovery and Context Economy**, serving as a perfect "Routing Skill" that prevents context bloat by offloading details to sub-references. However, it is **operationally hollow**; it provides no safety guardrails for destructive actions and lacks any mechanism for the agent to verify if a deployment or configuration was successful.

### Scores

| Dimension | Score | Key Finding |
| --- | --- | --- |
| Triggering Precision | 5/5 | Excellent description with specific keywords and clear "when to use" signaling. |
| Degrees of Freedom | 4/5 | Proper heuristic routing, but lacks specific constraints for the high-fragility `wrangler` CLI. |
| Context Economy | 4/5 | Highly efficient progressive disclosure, though YAML metadata and body references are out of sync. |
| Verifiability | 1/5 | Zero success criteria, validation loops, or checkpoints to confirm work. |
| Reversibility / Safety | 1/5 | No protection against destructive operations like resource deletion or cache purging. |
| Generalizability | 5/5 | Clean, platform-agnostic, and strictly follows YAML and naming conventions. |
| **Composite** | **20/30** |  |

---

### Detailed Findings

**Verifiability (1/5)**

* **Problem:** The skill provides a map but no destination checks. There are no instructions like "Verify deployment by checking the unique `workers.dev` URL" or "Run `wrangler dev` to validate local execution."
* **Impact:** The agent may complete a task (like updating a `wrangler.toml`), but will not proactively check if the change actually deployed correctly or broke the build.
* **Fix:** Add a `# Validation` section that mandates checking `wrangler` exit codes and visiting deployment preview URLs.

**Reversibility / Safety (1/5)**

* **Problem:** Cloudflare operations can be destructive (e.g., `wrangler kv:key delete`, `wrangler d1 execute --file drop_table.sql`). The skill does not mention safety flags or confirmation prompts.
* **Impact:** An agent might execute a destructive command autonomously without a dry-run or user confirmation, leading to unrecoverable data loss in production environments.
* **Fix:** Add a global instruction requiring the use of `--dry-run` where available and mandatory user confirmation for any `delete`, `remove`, or `execute` commands.

**Context Economy (4/5)**

* **Problem:** The YAML frontmatter lists only 5 references (`workers`, `pages`, etc.), but the body contains dozens of links to sub-files (e.g., `references/r2/`, `references/tunnel/`).
* **Impact:** Some agent harnesses use the `references` key to pre-fetch or index files. By omitting 90% of the links in the YAML, the agent's ability to "see" the sub-skills during the initial load is compromised.
* **Fix:** Sync the YAML `references` list to include all core products mentioned in the Product Index.

---

### Top 3 Fixes (Priority Order)

1. **Implement a Safety Protocol:** Add a section requiring the agent to use `--dry-run` or `--preview` for all `wrangler` commands and to pause for human confirmation before any operation involving the `delete`, `bulk`, or `secret` namespaces.
2. **Define Success Criteria:** Include a "Verification" block for each major category (Compute, Storage, AI). For example: "For Workers, always verify the deployment status via the CLI output and attempt a `curl` to the provided endpoint."
3. **Synchronize Metadata:** Update the YAML `references` field to match the `Product Index`. This ensures the agent's "routing" logic is backed by immediate awareness of the available sub-files.

Would you like me to rewrite the **Safety** and **Verifiability** sections for you to paste directly into the file?