# Contributing

Thanks for your interest in improving how we evaluate AI agent skills. Here's how you can help.

## Provide feedback on existing reviews

The most valuable contribution right now is your judgment. Pick a skill you're familiar with, read how different schemas reviewed it, and tell us what you think.

**Open an issue** with the title: `Feedback: [schema] review of [skill]`

Include:
- Which review(s) you read
- What the review got right
- What it missed or got wrong
- Whether the fixes were actionable enough to implement
- Your overall preference between schemas (if you compared multiple)

## Run a review

Pick a schema prompt and a skill, run it through an LLM, and submit the result.

### Steps

1. Choose a schema from `prompts/*/prompt.md`
2. Choose a SKILL.md to review (see [Skill sources](#skill-sources) below)
3. Paste the prompt as a system message, then provide the SKILL.md content as the user message
4. Save the output as `<model>-<composite-score>.md`
5. Submit a PR adding it to the appropriate folder: `prompts/<schema>/<skill-name>/`

### Naming convention

```
prompts/<SCHEMA>/<skill-name>/<model>-<score>.md
```

- **model**: `claude`, `chatgpt`, `gemini`, `deepseek`, `kimi`, `llama`, etc.
- **score**: The composite score from the review output (e.g., `21` for 21/30)
- If the same model is run multiple times, append a number: `claude-21-2.md`

### Skill sources

Good skills to review:
- [Anthropic built-in skills](https://github.com/anthropics/claude-code/tree/main/plugins) (frontend-design, docx, pdf, xlsx, pptx)
- [Cloudflare skill](https://github.com/dmmulroy/cloudflare-skill)
- [OpenAI skills](https://github.com/openai/skills)
- [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) — curated list of 380+ skills
- [Cloudflare official skills](https://github.com/cloudflare/skills)
- Any SKILL.md from your own projects

## Propose a new schema

If you have a review framework you'd like to benchmark:

1. Create a folder: `prompts/<SCHEMA-NAME>/`
2. Add your `prompt.md` — the full system prompt
3. Run it against at least one skill with at least one LLM
4. Submit a PR

Your prompt should:
- Define clear dimensions with scoring criteria
- Specify a structured output format
- Include scoring anchors (what does a 3 vs. a 5 look like?)

## Analysis contributions

If you want to do quantitative or qualitative analysis across results:
- Inter-model agreement (do different LLMs give similar scores on the same schema?)
- Inter-schema agreement (do different schemas flag the same problems?)
- Actionability audit (could a skill author fix the issues based on the review alone?)
- Calibration check (do scores match expert judgment?)

Submit analysis as an issue, a PR with a markdown report, or a discussion post.

## Code of conduct

Be constructive. The goal is to find what works, not to declare winners. Every schema here represents real thought and effort.
