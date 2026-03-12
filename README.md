# AI-Pair: Heterogeneous AI Team Collaboration

# AI-Pair：异构 AI 团队协作

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: Experimental](https://img.shields.io/badge/Status-Experimental-orange.svg)](#status)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills)

Coordinate multiple AI models to work together as a team. One creates, two review — not for redundancy, but because different models naturally focus on different dimensions.

让不同 AI 模型组成团队协作。一个创作，两个审查 — 不是为了冗余，而是因为不同模型天然关注不同维度。

> **Next Step:** Want to turn Skills from demo to asset? Check out [Agent Skills Resource Library](https://www.axtonliu.ai/agent-skills) (includes slides, PDF, diagnostics)

## Status

> **Status: Experimental | 状态：实验性**
>
> - This is a public prototype that works for real workflows, but does not yet cover all edge cases. | 公开原型，可用于实际工作流，但未覆盖所有边界情况。
> - Requires Claude Code + Codex CLI + Gemini CLI
> - My primary focus is demonstrating how tools and systems work together, not maintaining this codebase. | 重点是展示工具和系统如何协作，而非维护这个代码库。
> - If you encounter issues, please submit a reproducible case (input + output + steps to reproduce). | 如遇问题，请提交可复现的案例。

## Why This Exists | 为什么做这个

Most people use multiple AI subscriptions by asking the same question to each and comparing answers. That's useful sometimes, but it only uses one dimension of what different models can do — you get multiple answers to the same question, instead of multiple perspectives on the same work.

大部分人用多个 AI 的方式是：同一个问题分别问一遍，然后对比答案。这有时候有用，但只用到了不同模型能力的一个维度 — 你得到的是同一个问题的多个回答，而不是同一份工作的多个视角。

AI-Pair turns model differences into a structured workflow: assign each model a role that matches its strength, and let them review the same work from different angles. It's a [Claude Code Skill](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills) — a reusable instruction set that extends Claude Code's capabilities.

AI-Pair 把模型差异变成结构化的工作流：给每个模型分配匹配其特长的角色，让它们从不同角度审查同一份工作。它是一个 [Claude Code Skill](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills) — 一组可复用的指令，扩展 Claude Code 的能力。

## How It Works | 工作原理

```
User (you) | 用户（你）
  |
Team Lead (Claude Code session) | 团队领导（Claude Code 会话）
  |-- creator (Claude Code agent) — writes code or content | 创作者 — 写代码或内容
  |-- codex-reviewer (agent → Codex CLI) — analytical review | 分析型审查
  |-- gemini-reviewer (agent → Gemini CLI) — editorial review | 编辑型审查
```

The workflow is semi-automatic — you stay in control at every step:

工作流是半自动的 — 每一步你都保持控制权：

1. You assign a task → creator executes | 你下达任务 → 创作者执行
2. Creator reports back → you decide whether to send for review | 创作者回报 → 你决定是否送审
3. Both reviewers analyze in parallel → consolidated report | 两个审查者并行分析 → 汇总报告
4. You decide: revise or pass → loop or next task | 你决定：修改还是通过 → 循环或下一个任务

## Prerequisites | 前置条件

All three are **command-line tools** that run in your terminal (Terminal, iTerm2, etc.), not desktop apps.

三个都是**命令行工具**，在终端中运行（Terminal、iTerm2 等），不是桌面应用。

| Tool | Purpose | Install |
|------|---------|---------|
| [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) | Team Lead + agent runtime | `npm install -g @anthropic-ai/claude-code` |
| [Codex CLI](https://github.com/openai/codex) | GPT-powered reviewer | `npm install -g @openai/codex` |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Gemini-powered reviewer | `npm install -g @google/gemini-cli` |

All three CLIs must have authentication configured before use.

三个 CLI 使用前都需要配置好认证。

> **Quick check | 快速检查:** Run `claude --version`, `codex --version`, and `gemini --version` to verify all three are installed.

## Installation | 安装

### Option A: Direct Install (Recommended) | 直接安装（推荐）

```bash
# Clone to your global Claude Code skills directory
# 克隆到 Claude Code 全局 skills 目录
git clone https://github.com/axtonliu/ai-pair.git ~/.claude/skills/ai-pair
```

For project-level installation, clone into `.claude/skills/ai-pair` within your project directory instead.

如需项目级安装，克隆到项目目录下的 `.claude/skills/ai-pair`。

### Option B: Manual | 手动安装

1. Download `SKILL.md` from this repo | 下载本仓库的 `SKILL.md`
2. Place it in `~/.claude/skills/ai-pair/SKILL.md` | 放到 `~/.claude/skills/ai-pair/SKILL.md`
3. Restart Claude Code | 重启 Claude Code

## Usage | 使用

### Dev Team — for code, bugs, refactoring | 开发团队 — 写代码、修 bug、重构

```bash
/ai-pair dev-team MyProject
```

Team Lead creates | 团队领导创建:
- **developer** — writes code | 写代码
- **codex-reviewer** — checks bugs, security, performance, edge cases | 审查 bug、安全、性能、边界条件
- **gemini-reviewer** — checks architecture, design patterns, maintainability | 审查架构、设计模式、可维护性

### Content Team — for articles, scripts, newsletters | 内容团队 — 写文章、脚本、Newsletter

```bash
/ai-pair content-team AI-Newsletter
```

Team Lead creates | 团队领导创建:
- **author** — writes content | 写内容
- **codex-reviewer** — checks logic, accuracy, structure, fact-checking | 审查逻辑、准确性、结构、事实核查
- **gemini-reviewer** — checks readability, engagement, style, audience fit | 审查可读性、吸引力、风格、受众适配

### Stop Team | 关闭团队

```bash
/ai-pair team-stop
```

## Real-World Example | 真实案例

We used `content-team` to review a newsletter article. The three AIs found completely different issues:

我们用 `content-team` 审查了一篇 Newsletter 文章。三个 AI 发现的问题完全不同：

- **Claude** (Team Lead): spotted an overreach in interpreting a cited source | 发现对引用来源的过度解读
- **GPT** (Codex): dissected the argument chain and challenged a logical leap | 拆解论证链，质疑逻辑跳跃
- **Gemini**: suggested the opening was too academic for the target audience | 建议开头对目标读者来说太学术化

None of these overlapped. That's the point. See [`examples/`](examples/) for step-by-step walkthrough scenarios.

三者零重叠。这就是意义所在。查看 [`examples/`](examples/) 获取分步演示场景。

## File Structure | 文件结构

```
ai-pair/
├── SKILL.md       # Claude Code skill definition | Skill 定义文件
├── README.md      # This file | 本文件
├── LICENSE         # MIT
└── examples/      # Usage examples | 使用示例
    ├── dev-team.md
    └── content-team.md
```

## Troubleshooting | 常见问题

### Reviewers not actually calling Codex/Gemini CLI | 审查者没有真正调用 Codex/Gemini CLI

**Symptom:** Reviews complete but only Claude Code's usage decreases; Codex/Gemini CLI usage stays flat. The sub-agents are role-playing as Codex/Gemini instead of actually invoking them.

**症状：** 审查完成但只有 Claude Code 的用量在下降；Codex/Gemini CLI 用量没有任何变化。Sub-agent 在角色扮演而非真正调用外部 CLI。

**How to verify | 如何验证:** Check the review output for the `**Source: Codex CLI**` / `**Source: Gemini CLI**` label and the `### CLI Raw Output` section. If these are missing, the CLI was not called.

**如何验证：** 检查审查输出中是否有 `**Source: Codex CLI**` / `**Source: Gemini CLI**` 标签和 `### CLI Raw Output` 部分。如果缺失，说明 CLI 没有被调用。

**Fix | 解决方案:** This was addressed in v1.1.0 with mandatory CLI invocation rules. If you're on an older version, update your SKILL.md. If the issue persists, ensure both CLIs are installed and authenticated (`codex --version`, `gemini --version`).

**解决方案：** 此问题已在 v1.1.0 中通过强制 CLI 调用规则修复。如果你使用旧版本，请更新 SKILL.md。如果问题仍然存在，确认两个 CLI 都已安装并完成认证（`codex --version`、`gemini --version`）。

## What's Not Included | 未包含的功能

This open-source version includes the **Agent Teams mode** only. The full private version also has:

开源版仅包含 **Agent Teams 模式**。完整私有版还包括：

- **Manual mode** — two CLI instances communicating via shared file | 手动模式 — 两个 CLI 通过共享文件通信
- **iTerm2 orchestration** — automated Author/Reviewer relay with file watchers | iTerm2 编排 — 自动化的创作/审查中继

These require specific local setup and are maintained separately.

这些需要特定的本地配置，单独维护。

## Evolution | 演变

AI-Pair evolved from [AI Roundtable](https://github.com/axtonliu/ai-roundtable), a Chrome extension that lets multiple AI web interfaces discuss and cross-review in the same panel. AI-Pair moves this concept to the command line with structured role assignments, making it more practical for daily workflows.

AI-Pair 从 [AI Roundtable](https://github.com/axtonliu/ai-roundtable) 演变而来。AI Roundtable 是一个 Chrome 扩展，让多个 AI 的网页版在同一个面板里讨论和互评。AI-Pair 把这个概念搬到了命令行，加入了结构化的角色分工，更适合日常工作流。

## Contributing | 贡献

Contributions welcome (low-maintenance project):

欢迎贡献（低维护项目）：

- Reproducible bug reports (input + output + steps + environment) | 可复现的 bug 报告
- Documentation improvements | 文档改进
- Small PRs (fixes/docs) | 小型 PR（修复/文档）

> **Note:** Feature requests may not be acted on due to limited maintenance capacity. | 功能需求可能因维护精力有限而无法响应。

## License | 许可证

[MIT](LICENSE) - Axton Liu

---

## Author | 作者

**Axton Liu** — AI Educator & Creator

- Website: [axtonliu.ai](https://www.axtonliu.ai)
- YouTube: [@AxtonLiu](https://youtube.com/@AxtonLiu)
- Twitter/X: [@axtonliu](https://x.com/axtonliu)

### Learn More

- [MAPS™ AI Agent Course](https://www.axtonliu.ai/aiagent) - Systematic AI agent skills training
- [Claude Skills: A Systematic Guide](https://www.axtonliu.ai/newsletters/ai-2/posts/claude-agent-skills-maps-framework) - Complete methodology
- [AI Elite Weekly Newsletter](https://www.axtonliu.ai/newsletters/ai-2) - Weekly AI insights
- [Free AI Course](https://www.axtonliu.ai/axton-free-course) - Get started with AI

---

© AXTONLIU™ & AI 精英学院™ 版权所有
