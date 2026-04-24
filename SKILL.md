---
name: ai-pair
description: "Orchestrate multi-model AI teams for code and content creation with cross-model review. Spawns a developer or author agent (Claude Code), then dispatches parallel reviews to Codex CLI and Gemini CLI, each focusing on different quality dimensions. Use when you need AI pair programming, multi-model collaboration, collaborative code review, heterogeneous AI team review, or cross-model content editing."
user-invocable: true
triggers:
  - ai-pair
  - dev-team
  - content-team
  - team-stop
  - multi-model collaboration
  - AI pair programming
  - collaborative review
  - team of AIs
  - cross-model review
metadata:
  version: 1.5.0
---

# AI Pair Collaboration

Coordinate heterogeneous AI teams: one agent creates, two review from different angles via external CLIs (Codex + Gemini). Semi-automatic workflow — user stays in control at every step.

## Commands

```bash
/ai-pair dev-team [project]       # Developer + codex-reviewer + gemini-reviewer
/ai-pair content-team [topic]     # Author + codex-reviewer + gemini-reviewer
/ai-pair team-stop                # Shut down team, clean up resources
```

## Prerequisites

- **Claude Code** — Team Lead + agent runtime
- **Codex CLI** (`codex`) — GPT-powered reviewer
- **Gemini CLI** (`gemini`) — Gemini-powered reviewer
- Both external CLIs must have authentication configured

## Team Architecture

**Dev Team** — `developer` (Claude agent: code/features) + `codex-reviewer` (Codex CLI: bugs, security, concurrency, performance, edge cases) + `gemini-reviewer` (Gemini CLI: architecture, design patterns, maintainability, alternatives)

**Content Team** — `author` (Claude agent: articles, scripts, newsletters) + `codex-reviewer` (Codex CLI: logic, accuracy, structure, fact-checking) + `gemini-reviewer` (Gemini CLI: readability, engagement, style, audience fit)

## Workflow

1. **User assigns task** → Team Lead sends to developer/author
2. **Creator completes** → Team Lead shows result to user
3. **User approves for review** → Both reviewers analyze in parallel
4. **Reviewers report** → Team Lead consolidates into Codex Review + Gemini Review sections
5. **User decides** → "Revise" (loop) or "Pass" (next task or end)

## Team Lead Execution Steps

### Step 1: Create Team

```
TeamCreate: team_name = "{project}-dev" or "{topic}-content"
```

### Step 2: Create Tasks

Use TaskCreate:
1. "Awaiting task assignment" — developer/author, status: pending
2. "Awaiting review" — codex-reviewer, status: pending, blockedBy task 1
3. "Awaiting review" — gemini-reviewer, status: pending, blockedBy task 1

### Step 3: Pre-flight CLI Check

```bash
command -v codex && codex --version || echo "CODEX_MISSING"
command -v gemini && gemini --version || echo "GEMINI_MISSING"
```

If either CLI is missing, warn user and ask whether to proceed with degraded mode (Claude-only review, clearly labeled) or abort.

### Step 4: Launch Agents

Launch 3 agents via the Agent tool. Full prompts and the CLI Invocation Protocol are in [agent-prompts.md](agent-prompts.md). Example developer launch:

```
Agent({
  description: "developer in MyApp-dev",
  subagent_type: "general-purpose",
  prompt: "You are the developer in MyApp-dev team. Project path: /path/to/MyApp. Read relevant files, implement the task, report back via SendMessage to team-lead with which files changed, what you did, and what to watch out for. Stay active for next task."
})
```

Reviewer agents **must** call external CLIs via Bash with `timeout: 600000`. Example codex-reviewer CLI call:

```bash
REVIEW_FILE=$(mktemp /tmp/codex-review-XXXXXX.txt)
# write code/diff to $REVIEW_FILE, then:
codex review --uncommitted 2>&1       # or: codex review --commit <SHA>
# or for arbitrary content:
codex exec "Review the code in $REVIEW_FILE for bugs, security, concurrency, performance, edge cases." 2>&1
rm -f $REVIEW_FILE
```

If timeout: retry with degraded reasoning (xhigh → high → medium → low → Claude fallback). See [agent-prompts.md](agent-prompts.md) for full protocol and all four reviewer variants.

### Step 5: Confirm to User

Report team status: team name, type, all members ready, awaiting first task.

## Project Detection

1. **Explicitly specified** → use as-is
2. **Current directory is inside a project** → extract project name from path
3. **Ambiguous** → ask user to choose

## team-stop Flow

1. Send `shutdown_request` to all agents
2. Wait for all agents to confirm shutdown
3. Call `TeamDelete` to clean up team resources
4. Output: `Team shut down. Closed members: developer/author, codex-reviewer, gemini-reviewer. Resources cleaned up.`
