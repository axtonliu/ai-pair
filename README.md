# AI-Pair: Heterogeneous AI Team Collaboration

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills)

Coordinate multiple AI models to review the same work from fundamentally different angles. One creates, two review — not for redundancy, but because they see different things.

一个创作，两个审查 — 不是为了冗余，而是因为不同 AI 模型天然关注不同维度。

---

> **Status: Experimental**
> - Public prototype, works for real workflows
> - Requires Claude Code + Codex CLI + Gemini CLI
> - Low-maintenance; submit reproducible issues only

---

## Why This Exists

Most people use multiple AI subscriptions by asking the same question to each and comparing answers. That's the biggest waste.

Different models have different "attention patterns" — they don't just find different bugs, they look at completely different dimensions:

| Model | Review Style | Best For |
|-------|-------------|----------|
| **Claude** | Balanced, architectural | Overall structure, style compliance |
| **GPT (via Codex)** | Analytical, rigorous | Logic chains, fact-checking, edge cases |
| **Gemini** | Reader-centric, editorial | Readability, engagement, audience fit |

AI-Pair turns this difference into a structured workflow. It's a [Claude Code Skill](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills) — a reusable instruction set that extends Claude Code's capabilities.

## How It Works

```
User (you)
  |
Team Lead (Claude Code session)
  |-- creator (Claude Code agent) — writes code or content
  |-- codex-reviewer (agent → Codex CLI) — analytical review
  |-- gemini-reviewer (agent → Gemini CLI) — editorial review
```

The workflow is semi-automatic — you stay in control at every step:

1. You assign a task → creator executes
2. Creator reports back → you decide whether to send for review
3. Both reviewers analyze in parallel → consolidated report
4. You decide: revise or pass → loop or next task

## Prerequisites

| Tool | Purpose | Install |
|------|---------|---------|
| [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) | Team Lead + agent runtime | `npm install -g @anthropic-ai/claude-code` |
| [Codex CLI](https://github.com/openai/codex) | GPT-powered reviewer | `npm install -g @openai/codex` |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Gemini-powered reviewer | `npm install -g @google/gemini-cli` |

All three CLIs must have authentication configured before use.

> **Quick check:** Run `claude --version`, `codex --version`, and `gemini --version` to verify all three are installed.

## Installation

### Option A: Direct Install (Recommended)

```bash
# Clone to your global Claude Code skills directory
git clone https://github.com/axtonliu/ai-pair.git ~/.claude/skills/ai-pair
```

For project-level installation, clone into `.claude/skills/ai-pair` within your project directory instead.

### Option B: Manual

1. Download `SKILL.md` from this repo
2. Place it in `~/.claude/skills/ai-pair/SKILL.md`
3. Restart Claude Code

## Usage

### Dev Team — for code, bugs, refactoring

```bash
/ai-pair dev-team MyProject
```

Team Lead creates:
- **developer** — writes code
- **codex-reviewer** — checks bugs, security, performance, edge cases
- **gemini-reviewer** — checks architecture, design patterns, maintainability

### Content Team — for articles, scripts, newsletters

```bash
/ai-pair content-team AI-Newsletter
```

Team Lead creates:
- **author** — writes content
- **codex-reviewer** — checks logic, accuracy, structure, fact-checking
- **gemini-reviewer** — checks readability, engagement, style, audience fit

### Stop Team

```bash
/ai-pair team-stop
```

## Real-World Example

We used `content-team` to review a newsletter article. The three AIs found completely different issues:

- **Claude** (Team Lead): spotted an overreach in interpreting a research paper
- **GPT** (Codex): independently found the "Lost in the Middle" paper and challenged a citation
- **Gemini**: suggested the opening was too academic for the target audience

None of these overlapped. That's the point. See [`examples/`](examples/) for step-by-step walkthrough scenarios.

## File Structure

```
ai-pair/
├── SKILL.md       # Claude Code skill definition (Agent Teams mode)
├── README.md      # This file
├── LICENSE         # MIT
└── examples/      # Usage examples
    ├── dev-team.md
    └── content-team.md
```

## What's Not Included

This open-source version includes the **Agent Teams mode** only. The full private version also has:

- **Manual mode** — two CLI instances communicating via shared COLLAB.md file
- **iTerm2 orchestration** — automated Author/Reviewer relay with file watchers

These require specific local setup (iTerm2, Python dependencies) and are maintained separately.

## Evolution

AI-Pair evolved from [AI Roundtable](https://github.com/axtonliu/ai-roundtable), which focused on multi-AI discussions. AI-Pair restructures the concept around a create-review workflow that's more practical for daily use.

## Contributing

This is a low-maintenance project. Bug fixes and documentation improvements are welcome. For feature requests, please open an issue to discuss first.

## License

[MIT](LICENSE) - Axton Liu

## Author

**Axton Liu** — AI educator, creator of MAPS AI Framework

- YouTube: [@AxtonLiu](https://youtube.com/@AxtonLiu)
- X/Twitter: [@axtonliu](https://x.com/axtonliu)
- Website: [axtonliu.ai](https://axtonliu.ai)
