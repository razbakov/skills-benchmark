## Summary

This skill acts as a high-level router and index for the Cloudflare ecosystem, directing the agent to specific products based on functional requirements. It triggers on any Cloudflare-related development task and provides a structured mapping of use cases (Compute, Storage, AI, etc.) to detailed sub-references. The primary constraint is that it serves as a directory rather than a standalone implementation guide.

---

## CRN Scorecard

| Dimension | Score | Rationale |
| --- | --- | --- |
| **Triggering Precision** | 2 | Clear positive triggers, but lacks negative triggers or disambiguation for non-Cloudflare tasks. |
| **DoF Calibration** | 1 | Maps choices well, but lacks instructions on defaults or when to prompt the user for clarification. |
| **Context Economy** | 3 | Highly efficient; uses tables and trees to organize a massive scope without bloating the prompt. |
| **Verifiability** | 0 | No success criteria or validation steps to ensure the selected product meets the user's constraints. |
| **Reversibility / Safety** | 0 | Completely ignores the destructive nature of infrastructure changes (e.g., deleting KV data). |
| **Generalizability** | 3 | Excellent coverage across the entire platform; adaptable to any Cloudflare project. |

---

## Detailed Findings

### 1. Triggering Precision

* **Evidence:** "Use for any Cloudflare development task." and the detailed description list.
* **Risks/failure modes:** The agent might try to force a Cloudflare solution (like R2) for a user who already has an AWS-based stack because there are no "When NOT to use" instructions.
* **Fixes:** * Add a `### Do Not Use When` section (e.g., when the user specifies a different provider or local-only dev).

### 2. Degrees of Freedom Calibration

* **Evidence:** The decision trees (e.g., `Need storage? ├─ Key-value → kv/`).
* **Risks/failure modes:** If a user says "I need to store data," the agent might pick `kv/` by default without asking about consistency needs or file sizes, leading to sub-optimal architecture.
* **Fixes:** * Add a "Decision Protocol" section: "If requirement is ambiguous, ask [X, Y, Z] before selecting a product."

### 3. Context Economy

* **Evidence:** Use of ASCII trees and Markdown tables for the Product Index.
* **Risks/failure modes:** Minimal. The file is lean and focuses on navigation.
* **Fixes:** * None needed; this is the skill's strongest suit.

### 4. Verifiability

* **Evidence:** Missing required information.
* **Risks/failure modes:** The agent may recommend a product (like Workers AI) for a region or plan where it is unavailable, and there is no step to "verify compatibility."
* **Fixes:** * Add a `Validation` section: "Confirm product availability for the user's plan (Free/Pro/Biz/Ent) before proceeding."

### 5. Reversibility / Safety

* **Evidence:** Missing required information.
* **Risks/failure modes:** A simple instruction to "Clear my KV store" could be executed immediately without a backup or confirmation, leading to permanent data loss.
* **Fixes:** * Add a `Safety Constraints` section requiring `--dry-run` or manual user confirmation for `wrangler delete` or `put` operations.

### 6. Generalizability

* **Evidence:** Comprehensive list covering everything from `WAF` to `Agents SDK`.
* **Risks/failure modes:** Low; it covers almost every modern Cloudflare service.
* **Fixes:** * Ensure the "Infrastructure as Code" section mentions versioning (e.g., "Always check for latest provider versions").

---

## Top 5 Issues

1. **Critical Safety Void:** No safeguards for destructive actions (e.g., deleting namespaces, flushing cache).
2. **Zero Verifiability:** No success criteria or checks to ensure the suggested product is actually viable for the user.
3. **Ambiguity in Selection:** Decision trees provide paths but no "Asking vs. Guessing" logic.
4. **Missing Negative Triggers:** No guidance on when Cloudflare is *not* the right tool.
5. **Plan Ignorance:** Fails to account for feature gatekeeping (e.g., Smart Placement or certain WAF rules require specific plans).

---

## Patch Plan

1. **Add Safety Protocol:** Insert a section requiring user confirmation for all destructive `wrangler` commands.
2. **Define Negative Triggers:** Add "Do not activate if the user is requesting assistance for other providers (AWS/GCP) unless migration is mentioned."
3. **Insert Verification Steps:** Add a checklist for the agent to verify plan compatibility and regional availability.
4. **Add Decision Logic:** Add a rule: "If a user's 'I need' statement matches >1 tree branch, list the trade-offs and ask for preference."
5. **Standardize Output:** Require a "Next Steps" summary after a product is selected.
6. **Idempotency Check:** Instruct the agent to prefer configuration (IaC) over manual dashboard steps where possible.

---

## Targeted Rewrite

### Revised Header & Safety

```markdown
name: cloudflare
description: Central router for Cloudflare platform tasks. Use to identify the correct product (Compute, Storage, AI, Security) and load specific sub-references. 
# Triggering Logic
- **Activate when:** User mentions Cloudflare, Wrangler, or edge deployment.
- **Do NOT activate when:** Task is generic web dev or specific to other clouds (AWS/GCP/Azure) without migration intent.

## 🛡️ Safety & Validation
- **Destructive Actions:** ALWAYS require user confirmation before suggesting commands that delete, overwrite, or purge data (e.g., `wrangler kv:namespace delete`, `purge_cache`).
- **Plan Constraints:** Before recommending a product, verify if it requires a Paid plan (e.g., Workers KV has strict Free tier limits; Durable Objects require a Paid plan).
- **Verification:** After selecting a product, state *why* it fits the user's scale, latency, and budget requirements.

```

### Revised Decision Protocol

```markdown
## Decision Protocol
If the user's request is vague ("I want to store files"), do not guess. Ask:
1. **Scale:** How much data? (e.g., <1GB vs TBs)
2. **Access Pattern:** High-frequency KV lookups or large object storage?
3. **Consistency:** Does it need to be globally instant or eventually consistent?

```

Would you like me to generate a similar review for one of the sub-references like `workers.md` or `r2.md`?