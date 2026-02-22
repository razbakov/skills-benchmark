# Quality: Review Skill with AI

As a user, I want an AI to review a skill across multiple dimensions, so that I can understand its safety, efficiency, and overall quality before using it.

## Context
When browsing or inspecting skills, it is often difficult to quickly determine if a skill is safe, well-written, and efficient without reading the code manually. An AI-powered review provides immediate feedback on the skill's quality, helping users avoid poorly written or potentially insecure skills.

## Acceptance Criteria

### Review Initiation
- A "Review with AI" button is visible when viewing a skill's details.
- Clicking the button immediately starts the review process and displays a loading message (e.g., "Analyzing skill...").

### Multidimensional Review
- The review results display evaluations across at least four distinct dimensions:
  - **Security**: Checks for prompt injection risks or dangerous commands.
  - **Efficiency**: Estimates token usage and context bloat.
  - **Compliance**: Verifies adherence to the open Agent Skills standard.
  - **Best Practices**: Evaluates the clarity and structure of the instructions.
- Each dimension presents a clear status indicator (e.g., Pass, Warning, or Action Required) alongside a brief, easy-to-understand explanation.

### Overall Results Display
- An overall summarized quality score or verdict is shown at the top of the review section.
- The user can view the full text of the review without leaving the skill details page.
- If the review process encounters an error or times out, a clear error message is displayed: "Unable to complete AI review at this time. Please try again."
