# Content Team Example

## Scenario: Review a Newsletter Article

You've drafted a newsletter about AI memory systems and want multi-perspective feedback before publishing.

### Start the team

```bash
/ai-pair content-team AI-Newsletter
```

Output:
```
Team ready.

Team: AI-Newsletter-content
Type: Content Team
Members:
  - author: ready
  - codex-reviewer: ready
  - gemini-reviewer: ready

Awaiting your first task.
```

### Assign a task

```
Review this draft newsletter article about why AI memory
(like MEMORY.md in Claude Code) isn't actually "memory" in the
way most people think. The article argues that it's closer to
a post-it note than a brain.

[paste your draft or provide file path]
```

The author agent reads the draft and prepares it for review. You approve sending to reviewers.

### Review results

```
## Codex Review

### Logic & Accuracy
- The claim "LLMs have no persistent state between sessions" is technically
  correct but oversimplified. Some systems do maintain vector stores that
  persist. Consider qualifying with "by default" or "natively".
- The analogy to post-it notes breaks down: post-it notes don't have
  retrieval logic. MEMORY.md is more like a briefing document.

### Fact-Checking
- The reference to "context window limits of 200K tokens" should specify
  this is Claude's limit. GPT-4 Turbo is 128K.

### Summary
Solid argument, but two factual claims need tightening.

## Gemini Review

### Readability & Flow
- The opening paragraph uses three technical terms before the reader
  gets the hook. Consider leading with the relatable scenario first.

### Engagement & Hook
- The "post-it note" metaphor is strong but introduced too late (paragraph 4).
  Move it to the opening for immediate impact.

### Audience Fit
- Tone shifts between casual (paragraphs 1-3) and academic (paragraphs 4-6).
  The casual tone matches the target audience better — maintain it throughout.

### Summary
Good content, needs structural reorganization for maximum impact.
```

Codex caught **factual precision issues**. Gemini caught **readability and structure issues**. Zero overlap.

### Iterate

You tell Team Lead:
```
Fix the factual claims Codex flagged.
Move the post-it metaphor to the opening as Gemini suggested.
Keep the casual tone throughout.
Don't change the core argument.
```

The author revises. You can send for another round of review or pass.

### End the session

```bash
/ai-pair team-stop
```

## Tips for Content Team

1. **Provide context about your audience** — reviewers give better feedback when they know who's reading
2. **Don't fix everything** — you decide which feedback matters. Codex tends to over-index on precision; Gemini tends to over-index on accessibility
3. **Use iteratively** — first round for big issues, second round for polish
4. **Style memory** — if you have a `style-memory.md` file, the author agent will automatically follow your style preferences
