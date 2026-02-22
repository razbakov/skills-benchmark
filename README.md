# SKILL.md Review Schema Benchmark

Which review framework best evaluates AI agent skill files?

We designed 5 review schemas for evaluating SKILL.md files, ran them against real-world skills using multiple LLMs, and collected the results. Now we want your judgment.

This benchmark is feeding into [Skills Manager](https://github.com/razbakov/skills-manager), a management layer for AI agent skills.

## Prompts

| Prompt | Dimensions | Scale | 
|---|---|---|
| **[RADAR](prompts/RADAR/prompt.md)** | 6 | 30 |
| **[PRISM](prompts/PRISM/prompt.md)** | 8 | 40 | 
| **[A2E](prompts/A2E/prompt.md)** | 10 | 30 | 
| **[TDC-VRG](prompts/TDC-VRG/prompt.md)** | 6 | 18 | 
| **[Logic-Linter](prompts/Logic-Linter/prompt.md)** | 6 | 30 |

## Results

Each schema folder contains subfolders per skill reviewed. Result files are named `<model>-<score>.md`.

**Best comparison point:** the [cloudflare skill](https://github.com/dmmulroy/cloudflare-skill) reviewed across 3 schemas × 3 models:

| Schema | Claude | ChatGPT | Gemini |
|---|---|---|---|
| RADAR | [21/30](prompts/RADAR/cloudflare/claude-21.md) | [20/30](prompts/RADAR/cloudflare/chatgpt-20.md) | [20/30](prompts/RADAR/cloudflare/gemini-20.md) |
| PRISM | [29/40](prompts/PRISM/cloudflare/claude-29.md) | [21/40](prompts/PRISM/cloudflare/chatgpt-21.md) | [23/40](prompts/PRISM/cloudflare/gemini-23.md) |
| TDC-VRG | [9/18](prompts/TDC-VRG/cloudflare/claude-9.md) | [6/18](prompts/TDC-VRG/cloudflare/chatgpt-6.md) | [9/18](prompts/TDC-VRG/cloudflare/gemini-9.md) |

## We want your feedback

Read a few reviews, then [open an issue](../../issues) with your take:

- Which review would you actually use to improve a skill?
- Which found real problems vs. generated noise?
- Were the suggested fixes specific enough to act on?
- Did any schema over- or under-score?

## Contributing

- **Run a review:** Pick a schema + a skill + an LLM → submit the result as a PR
- **Propose a schema:** Add `prompts/<NAME>/prompt.md` with at least one review
- **Suggest skills to review:** Open an issue

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

MIT
