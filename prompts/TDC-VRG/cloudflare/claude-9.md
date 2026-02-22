## Summary

This SKILL.md is a **routing/index skill** for the Cloudflare platform. It acts as a decision-tree dispatcher: given a user's intent (run code, store data, use AI, etc.), it directs the agent to the correct product-specific reference file. It covers ~60+ Cloudflare products across compute, storage, AI, networking, security, media, and IaC. It contains no implementation detail itself — all substance is deferred to `references/` sub-files.

---

## Scorecard

| Dimension | Score | Rationale |
|---|---|---|
| 1. Triggering Precision | 2 | Good decision trees for routing, but the description is overly broad ("any Cloudflare development task") and no negative triggers are defined. |
| 2. Degrees of Freedom Calibration | 1 | No guidance on what to do when multiple branches match, no defaults, no "ask user vs. proceed" heuristics. |
| 3. Context Economy | 3 | Exemplary — purely a routing index with decision trees and tables; all detail deferred to references. |
| 4. Verifiability | 0 | No success criteria, no validation steps, no "how to tell it worked" for the routing decision itself. |
| 5. Reversibility / Safety | 1 | No destructive actions in this file itself (which is good), but no guidance on cost implications, beta/GA status, or "try X before Y" safeguards. |
| 6. Generalizability | 2 | Broad coverage is strong, but assumes user always knows their intent category; no cross-cutting patterns (e.g., "I need auth" or "I need a full-stack app" which spans multiple trees). |

---

## Detailed Findings

### 1. Triggering Precision (2/3)

**Evidence:** The `description` field says *"Use for any Cloudflare development task."* Decision trees cover 7 intent categories with clear leaf nodes.

**Risks:** The blanket trigger "any Cloudflare development task" means this skill will activate for casual Cloudflare questions (e.g., "what's Cloudflare's pricing?") where it's not useful. No negative triggers exclude non-development queries (DNS management, billing, account admin). Could conflict with a general "web deployment" skill.

**Fixes:**
- Add explicit negative triggers: "Do NOT use for: Cloudflare account/billing questions, DNS-only configuration, general 'what is Cloudflare' questions."
- Narrow description to: "Use when the user wants to **build, deploy, or configure** something on the Cloudflare developer platform."

### 2. Degrees of Freedom Calibration (1/3)

**Evidence:** Each tree branch maps intent → single product reference. No guidance exists for ambiguous cases.

**Risks:** A request like "I need a full-stack app with a database and auth" touches Workers/Pages + D1 + Turnstile — the agent gets no guidance on how to combine references or which to load first. No "when in doubt, ask the user" instruction.

**Fixes:**
- Add a "Multi-product patterns" section with 3-4 common combos (e.g., "Full-stack app → Pages + D1 + KV for sessions").
- Add explicit instruction: "If the user's request spans multiple trees, load the primary compute reference first, then storage, then ancillary services."
- Add: "If the user's intent is ambiguous between two leaf nodes, ask which constraint matters more (e.g., 'Do you need SQL queries or just key-value lookups?')."

### 3. Context Economy (3/3)

**Evidence:** The entire file is a routing layer — decision trees + product index tables. No redundant prose, no duplicated implementation details.

**Risks:** Minimal. The product index table partially duplicates the decision trees, but serves as a flat lookup which is useful.

**Fixes:**
- Minor: could add a one-line note clarifying that the product index is for direct lookup while trees are for intent-based discovery (but not required).

### 4. Verifiability (0/3)

**Evidence:** No section addresses validation. There is no "how to confirm the right product was selected" or "checkpoint before loading a reference."

**Risks:** The agent could route to the wrong product (e.g., KV instead of D1 for relational data) with no mechanism to catch the error before proceeding deep into implementation.

**Fixes:**
- Add a "Routing Validation" section: "After selecting a product, confirm with the user: 'Based on your requirements, I'll use [Product]. This is best for [one-liner]. Does that sound right?'"
- Add brief disambiguators to each leaf node (e.g., "KV → eventually consistent, no queries" vs "D1 → SQL, relational, consistent").

### 5. Reversibility / Safety (1/3)

**Evidence:** The file itself doesn't execute anything destructive, which is inherently safe. However, there's no mention of cost tiers, beta status, or "prefer free-tier-compatible options first."

**Risks:** Agent could route to a paid/enterprise product (Spectrum, Argo Smart Routing, Bot Management) without the user knowing it requires a paid plan. Some products are in beta (Containers, Pipelines) and may break.

**Fixes:**
- Add a column or annotation for plan requirements: `[Free]`, `[Pro+]`, `[Enterprise]`, `[Beta]`.
- Add instruction: "If a selected product requires a paid plan or is in beta, inform the user before proceeding."

### 6. Generalizability (2/3)

**Evidence:** Covers the full Cloudflare developer platform with 60+ products. Decision trees are organized by user intent.

**Risks:** Users with cross-cutting needs ("I want to build a SaaS app") or non-standard framings ("I need to migrate from AWS Lambda") won't find a matching tree. All trees assume the user thinks in Cloudflare's product categories.

**Fixes:**
- Add 2-3 "scenario-based" entry points: "Migrating from AWS → Workers (Lambda), R2 (S3), D1 (DynamoDB), Queues (SQS)."
- Add a "Common stacks" section: "JAMstack → Pages + Workers + D1 + R2", "AI chatbot → Workers + Workers AI + Vectorize + D1."

---

## Top 5 Issues

1. **No verifiability (score 0):** Zero routing-validation checkpoints; wrong product selection propagates silently into implementation.
2. **No multi-product guidance:** Ambiguous or composite requests have no resolution heuristic — agent must guess which tree/branch to follow.
3. **No plan/beta annotations:** Agent may route users to paid or unstable products without disclosure.
4. **Over-broad trigger:** "Any Cloudflare development task" will over-fire for non-development queries.
5. **No scenario-based entry points:** Users thinking in terms of use cases ("build a SaaS," "migrate from AWS") rather than Cloudflare categories get no help.

---

## Patch Plan

1. ☐ Add a "Routing Validation" section with a confirm-with-user checkpoint after product selection.
2. ☐ Add "Multi-product Patterns" section with 3-4 common combos and a load-order heuristic.
3. ☐ Annotate products with `[Free]` / `[Pro+]` / `[Enterprise]` / `[Beta]` tags in the decision trees.
4. ☐ Narrow the `description` field; add explicit negative triggers (billing, DNS-only, account admin).
5. ☐ Add "Common Stacks / Scenarios" section for composite use cases and migration patterns.
6. ☐ Add disambiguating one-liners to storage and compute leaf nodes in the decision trees.
7. ☐ Add instruction: "If ambiguous between two leaf nodes, ask the user one clarifying question."
8. ☐ Add instruction: "If a product requires a paid plan or is in beta, inform user before loading reference."
9. ☐ Add a "When NOT to use this skill" subsection.
10. ☐ Consider adding a `references:` entry for the top 3-5 most commonly used products beyond the current 5.

---

## Targeted Rewrite

### Rewrite 1: `description` field + new "When NOT to use" section

```yaml
---
name: cloudflare
description: >
  Cloudflare developer platform skill covering Workers, Pages, storage (KV, D1, R2),
  AI (Workers AI, Vectorize, Agents SDK), networking, security, and IaC.
  Use when the user wants to build, deploy, or configure an application or service
  on Cloudflare. Do NOT use for: Cloudflare account/billing questions, DNS-only
  configuration, general "what is Cloudflare" questions, or CDN cache-purge-only tasks.
references:
  - workers
  - pages
  - d1
  - durable-objects
  - workers-ai
---
```

### Rewrite 2: New "Routing Validation & Multi-Product Guidance" section (insert after Quick Decision Trees)

```markdown
## Routing Validation

After selecting a product from the decision trees:

1. **Confirm with the user:** "Based on your requirements, I'll use [Product] — it's designed for [one-liner]. Sound right?"
2. **Check plan requirements:** If the product requires a paid plan or is in beta, tell the user before loading the reference.
3. **Disambiguate if needed:** If two leaf nodes could fit, ask one targeted question (e.g., "Do you need SQL queries, or is simple key-value access enough?").

## Multi-Product Patterns

For composite requests, load references in this order: **compute → storage → ancillary services**.

| Scenario | Stack |
|----------|-------|
| Full-stack web app | Pages + D1 + KV (sessions) + R2 (uploads) |
| AI chatbot / RAG | Workers + Workers AI + Vectorize + D1 |
| Real-time collaboration | Workers + Durable Objects + Pages |
| SaaS platform (multi-tenant) | Workers for Platforms + D1 + KV + Queues |
| Migrate from AWS Lambda + S3 + SQS | Workers + R2 + Queues |

If the user's request spans 3+ products, summarize the proposed stack before diving into any single reference.
```