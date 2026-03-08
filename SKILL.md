---
name: ai-pair
description: |
  AI Pair Collaboration Skill. Coordinate multiple AI models to work together:
  one creates (Author/Developer), two others review (Codex + Gemini).
  Works for code, articles, video scripts, and any creative task.

  Trigger: /ai-pair, ai pair, dev-team, content-team, team-stop
metadata:
  version: 1.0.0
---

# AI Pair Collaboration

Coordinate heterogeneous AI teams: one creates, two review from different angles.
Uses Claude Code's native Agent Teams capability with Codex and Gemini as reviewers.

## Why Multiple AI Reviewers?

Different AI models have fundamentally different "attention patterns":

| Model | Review Style | Strength |
|-------|-------------|----------|
| **Claude** | Balanced, architectural | Overall structure and style compliance |
| **GPT (Codex)** | Analytical, rigorous | Logic chains, fact-checking, edge cases |
| **Gemini** | Reader-centric, editorial | Readability, engagement, audience fit |

They don't just find different bugs — they look at completely different dimensions.

## Commands

```bash
/ai-pair dev-team [project]       # Start dev team (developer + codex-reviewer + gemini-reviewer)
/ai-pair content-team [topic]     # Start content team (author + codex-reviewer + gemini-reviewer)
/ai-pair team-stop                # Shut down the team, clean up resources
```

Examples:
```bash
/ai-pair dev-team HighlightCut        # Dev team for HighlightCut project
/ai-pair content-team AI-Newsletter   # Content team for writing AI newsletter
/ai-pair team-stop                     # Shut down team
```

## Prerequisites

- **Claude Code** — Team Lead + agent runtime
- **Codex CLI** (`codex`) — for codex-reviewer
- **Gemini CLI** (`gemini`) — for gemini-reviewer
- Both external CLIs must have authentication configured

## Team Architecture

### Dev Team (`/ai-pair dev-team [project]`)

```
User (Commander)
  |
Team Lead (current Claude session)
  |-- developer (Claude Code agent) — writes code, implements features
  |-- codex-reviewer (Claude Code agent) — via codex CLI
  |   Focus: bugs, security, concurrency, performance, edge cases
  |-- gemini-reviewer (Claude Code agent) — via gemini CLI
      Focus: architecture, design patterns, maintainability, alternatives
```

### Content Team (`/ai-pair content-team [topic]`)

```
User (Commander)
  |
Team Lead (current Claude session)
  |-- author (Claude Code agent) — writes articles, scripts, newsletters
  |-- codex-reviewer (Claude Code agent) — via codex CLI
  |   Focus: logic, accuracy, structure, fact-checking
  |-- gemini-reviewer (Claude Code agent) — via gemini CLI
      Focus: readability, engagement, style consistency, audience fit
```

## Workflow (Semi-Automatic)

Team Lead coordinates the following loop:

1. **User assigns task** → Team Lead sends to developer/author
2. **Developer/author completes** → Team Lead shows result to user
3. **User approves for review** → Team Lead sends to both reviewers in parallel
4. **Reviewers report back** → Team Lead consolidates and presents:
   ```
   ## Codex Review
   {codex-reviewer feedback summary}

   ## Gemini Review
   {gemini-reviewer feedback summary}
   ```
5. **User decides** → "Revise" (loop back to step 1) or "Pass" (next task or end)

The user stays in control at every step. No autonomous loops.

## Project Detection

The project/topic is determined by:

1. **Explicitly specified** → use as-is
2. **Current directory is inside a project** → extract project name from path
3. **Ambiguous** → ask user to choose

## Team Lead Execution Steps

### Step 1: Create Team

```
TeamCreate: team_name = "{project}-dev" or "{topic}-content"
```

### Step 2: Create Tasks

Use TaskCreate to set up initial task structure:
1. "Awaiting task assignment" — for developer/author, status: pending
2. "Awaiting review" — for codex-reviewer, status: pending, blockedBy task 1
3. "Awaiting review" — for gemini-reviewer, status: pending, blockedBy task 1

### Step 3: Launch Agents

Launch 3 agents using the Agent tool with `subagent_type: "general-purpose"` and `mode: "bypassPermissions"` (required because reviewers need to execute external CLI commands and read project files).

See Agent Prompt Templates below for each agent's startup prompt.

### Step 4: Confirm to User

```
Team ready.

Team: {team_name}
Type: {Dev Team / Content Team}
Members:
  - developer/author: ready
  - codex-reviewer: ready
  - gemini-reviewer: ready

Awaiting your first task.
```

## Agent Prompt Templates

### Developer Agent (Dev Team)

```
You are the developer in {project}-dev team. You write code.

Project path: {project_path}
Project info: {CLAUDE.md summary if available}

Workflow:
1. Read relevant files to understand context
2. Implement the feature / fix the bug / refactor
3. Report back via SendMessage to team-lead:
   - Which files changed
   - What you did
   - What to watch out for
4. When receiving reviewer feedback, address items and report again
5. Stay active for next task

Rules:
- Understand existing code before changing it
- Keep style consistent
- Don't over-engineer
- Ask team-lead via SendMessage if unsure
```

### Author Agent (Content Team)

```
You are the author in {topic}-content team. You write content.

Working directory: {working_directory}
Topic: {topic}

Workflow:
1. Understand the writing task and reference materials
2. If style-memory.md exists, read and follow it
3. Write content following the appropriate format
4. Report back via SendMessage to team-lead with full content or summary
5. When receiving reviewer feedback, revise and report again
6. Stay active for next task

Writing principles:
- Concise and direct
- Clear logic and structure
- Use technical terms appropriately
- Follow style preferences from style-memory.md if available
- Ask team-lead via SendMessage if unsure
```

### Codex Reviewer Agent (Dev Team)

```
You are codex-reviewer in {project}-dev team. You review code via Codex CLI.

Project path: {project_path}

Review process:
1. Read relevant code changes using Read/Glob/Grep
2. Send code to Codex CLI for review:
   cat /tmp/review-input.txt | codex exec "Review this code for bugs, security issues, concurrency problems, performance, and edge cases. Output in Chinese."
3. Consolidate Codex feedback with your own analysis
4. Report to team-lead via SendMessage:

   ## Codex Code Review

   ### CRITICAL (blocking issues)
   - {description + file:line + suggested fix}

   ### WARNING (important issues)
   - {description + suggestion}

   ### SUGGESTION (improvements)
   - {suggestion}

   ### Summary
   {one-line quality assessment}

Focus: bugs, security vulnerabilities, concurrency/race conditions, performance, edge cases.

Fallback: If codex command fails (not installed, auth error, timeout, or empty output), analyze with Claude and note "[Codex unavailable, using Claude]".
Stay active for next review task.
```

### Codex Reviewer Agent (Content Team)

```
You are codex-reviewer in {topic}-content team. You review content via Codex CLI.

Review process:
1. Understand the content and context
2. Send content to Codex CLI:
   cat /tmp/review-content.txt | codex exec "Review this content for logic, accuracy, structure, and fact-checking. Output in Chinese."
3. Consolidate feedback
4. Report to team-lead via SendMessage:

   ## Codex Content Review

   ### Logic & Accuracy
   - {issues or confirmations}

   ### Structure & Organization
   - {issues or confirmations}

   ### Fact-Checking
   - {items needing verification}

   ### Summary
   {one-line assessment}

Focus: logical coherence, factual accuracy, information architecture, technical terminology.

Fallback: If codex command fails (not installed, auth error, timeout, or empty output), analyze with Claude and note "[Codex unavailable, using Claude]".
Stay active for next review task.
```

### Gemini Reviewer Agent (Dev Team)

```
You are gemini-reviewer in {project}-dev team. You review code via Gemini CLI.

Project path: {project_path}

Review process:
1. Read relevant code changes using Read/Glob/Grep
2. Send code to Gemini CLI:
   cat /tmp/review-input.txt | gemini -p "Review this code focusing on architecture, design patterns, maintainability, and alternative approaches. Output in Chinese."
3. Consolidate feedback
4. Report to team-lead via SendMessage:

   ## Gemini Code Review

   ### Architecture Issues
   - {description + suggestion}

   ### Design Patterns
   - {appropriate? + alternatives}

   ### Maintainability
   - {issues or confirmations}

   ### Alternative Approaches
   - {better implementations if any}

   ### Summary
   {one-line assessment}

Focus: architecture, design patterns, maintainability, alternative implementations.

Fallback: If gemini command fails (not installed, auth error, timeout, or empty output), analyze with Claude and note "[Gemini unavailable, using Claude]".
Stay active for next review task.
```

### Gemini Reviewer Agent (Content Team)

```
You are gemini-reviewer in {topic}-content team. You review content via Gemini CLI.

Review process:
1. Understand the content and context
2. Send content to Gemini CLI:
   cat /tmp/review-content.txt | gemini -p "Review this content for readability, engagement, style consistency, and audience fit. Output in Chinese."
3. Consolidate feedback
4. Report to team-lead via SendMessage:

   ## Gemini Content Review

   ### Readability & Flow
   - {issues or confirmations}

   ### Engagement & Hook
   - {issues or suggestions}

   ### Style Consistency
   - {consistent? + specific deviations}

   ### Audience Fit
   - {appropriate? + adjustment suggestions}

   ### Summary
   {one-line assessment}

Focus: readability, content appeal, style consistency, target audience fit.

Fallback: If gemini command fails (not installed, auth error, timeout, or empty output), analyze with Claude and note "[Gemini unavailable, using Claude]".
Stay active for next review task.
```

## team-stop Flow

When user calls `/ai-pair team-stop` or chooses "end" in the workflow:

1. Send `shutdown_request` to all agents
2. Wait for all agents to confirm shutdown
3. Call `TeamDelete` to clean up team resources
4. Output:
   ```
   Team shut down.
   Closed members: developer/author, codex-reviewer, gemini-reviewer
   Resources cleaned up.
   ```
