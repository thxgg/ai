# AI

## How I Use AI

I use AI coding agents as my primary development workflow. My setup is built around [OpenCode](https://opencode.ai), a terminal-based AI coding tool that I've heavily customized through agents, skills, MCP integrations, plugins, and granular permission controls — all managed as dotfiles via GNU Stow.

### Tools

**OpenCode** is my primary tool. I use it in the terminal with a multi-agent, multi-model architecture. I also have lightly configured (Exa search and GitHub MCP servers) and played around with **Amp** but am not actively using it due to lack of integration with ChatGPT Pro and Claude Max plans.

I don't use GitHub Copilot, Cursor, or any other AI-assisted editor/IDE. My editor is Neovim with no AI plugins — all AI interaction happens through the dedicated agent tools.

Code review is done in a mixture of ways, mostly via [lazygit](https://github.com/jesseduffield/lazygit) but sometimes with the help of the GitHub PR view and browsing the changes in Neovim.

### Models

I use a multi-model strategy, picking models based on task characteristics:

| Model | Role | Why |
|-------|------|-----|
| **Claude Opus 4.6** | Primary coding — planning and building | Best at agentic coding with long-context reasoning |
| **GPT-5.4** | Deep reasoning, architecture advice | Strong at strategic thinking and tradeoff analysis |
| **Gemini 3.1 Pro** | Code review | Good at systematic, evidence-based review (this is an experiment based on [Amp's current model setup](https://ampcode.com/models)) |
| **Claude Sonnet 4.6** | Reference tasks, documentation, codebase exploration | Fast and capable for lookup-heavy work |
| **Gemini 3 Flash** | Commit messages | Cheap and fast for simple, structured output |

Important note is that I switched off GPT-5.4 as it is too slow and meanders too much for simpler tasks. Still generally better when the task scope is not yet precisely defined in my experience.

### Agents

I define specialized agents with distinct roles, model assignments, and permission scopes:

**Primary agents** (I switch between these directly):
- **plan** — Read-only mode using Claude Opus 4. For analyzing code, planning changes, and reasoning before writing anything.
- **build** — Full-access mode using Claude Opus 4. For implementing changes, running builds, committing code.
- **deep** — Full-access mode using GPT-5.4. For problems that benefit from deeper reasoning or a different perspective. This one is experimental and inspired by Amp.

**Subagents** (invoked by primary agents or commands as specialists):
- **oracle** — GPT-5.4 in read-only mode. Senior engineering advisor for architecture decisions. Returns structured responses (TL;DR, Recommendation, Rationale, Risks, When to Reconsider). Emphasizes YAGNI and simplicity. Based on Amp's Orcale agent.
- **review** — Gemini 3.1 Pro. Code reviewer focused on bugs, security, and maintainability. Requires evidence-backed findings with severity ratings and file:line references.
- **librarian** — Claude Sonnet 4.6. Explores remote codebases across GitHub, npm, PyPI, and crates.io to understand library internals. Based on Amp's Librarian agent.
- **opencode-expert** — Claude Sonnet 4.6. Answers questions about OpenCode configuration and features using built-in documentation.

### MCP Servers

I extend agent capabilities through Model Context Protocol servers:

| Server | Purpose |
|--------|---------|
| **GitHub** | Full GitHub API access — issues, PRs, repos, code search |
| **Exa** | Web search and content discovery |
| **opensrc** | Remote repository exploration (fetch and search GitHub repos, npm/PyPI/crates packages without cloning) |
| **Steward** | My own published MCP server ([`@thxgg/steward`](https://www.npmjs.com/package/@thxgg/steward)) for repository-aware PRD and task management. This is the approach I've found most successful when working on non-trivial or larger tasks |

I also have **Playwright** (browser automation), **Figma** (design integration) but they are disabled by default and enabled only on demand.

### Skills

Skills are domain-specific instruction sets that agents load on demand. Some highlights:

- **index-knowledge** — Generates hierarchical `AGENTS.md` knowledge base files for codebases, giving agents structured context about project architecture.
- **librarian** — Deep multi-repository exploration workflow using opensrc-mcp.
- **web-animation-design** — Comprehensive animation design guide based on Emil Kowalski's course (easing, springs, timing, accessibility).
- **frontend-design** — Vue/Nuxt + Tailwind CSS v4 + shadcn-vue interface design with intentional aesthetics.
- **postgres** — Extensive PostgreSQL reference covering schema design, query optimization, indexing, MVCC, replication, and monitoring. Ships with 16 reference files.
- **commit-release** — Full npm + GitHub release workflow (version bump, tag, publish, verify).
- **worktree** — Git worktree management with forked agent sessions.
- **mermaid** — Diagram validation via Mermaid CLI rendering.
- **agent-browser** — Browser automation via a Playwright-based Rust CLI.
- **prd / prd-task / complete-next-task** — Full PRD lifecycle: guided discovery → structured document → executable task breakdown → automated implementation with commit tracking (those are auto-registered by the Steward MCP).

### Commands

Custom slash commands wire skills and workflows together:

- `/commit` — One-line conventional commits (uses Gemini Flash for speed).
- `/commit-release` — Commit + full release workflow with version bumping.
- `/code-review` — Launches 3 parallel review subagents with distinct focus areas, deduplicates findings, then runs a validation pass to confirm issues.
- `/pr` — Branch state check → code review → push → `gh pr create`.
- `/search` — Codebase search with ranked results.
- `/worktree` — Create/list/resume/finish git worktree sessions.
- `/librarian` — Deep exploration of remote repositories.
- `/index-knowledge` — Generate AGENTS.md knowledge base for a codebase.
- `/frontend-design`, `/web-animation-design` — Design-focused skill invocations (credit to [Emil Kowalski](https://x.com/emilkowalski) for his amazing design and animations course and SKILL files).

### Plugins

Only currently used plugin is for the TUI notification sound when an agent is idle or needs my input.

### Workflow

My typical workflow looks like:

1. **Plan** — Switch to the `plan` agent (read-only). Explore the codebase, reason about the problem, consult the `oracle` for architecture advice if needed.
2. **Build** — Switch to the `build` agent. Implement changes, run tests, iterate.
3. **Review** — Run `/code-review` for multi-pass automated review with deduplication and validation.

For complex features, I use the PRD skills to go from problem discovery → scoped requirements document → task breakdown → automated implementation with commit tracking.

## Recommendations

Use TDD when working on bugs.
Everything is trade-offs. Understand them.
Focus on system design.
Index-knowledge your repositories (never use the default `/init`).
Spend more time exploring the domain.
A well-structured, clean repository is just as important to LLMs as it is to humans.

## Resources

### X (Twitter)

- [Dax](https://x.com/thdxr)
- [Dillon Mulroy](https://x.com/dillon_mulroy)
- [Mitchell Hashimoto](https://x.com/mitchellh)
- [Thorsten Ball](https://x.com/thorstenball)
- [Adam Elmore](https://x.com/adamdotdev)
- [Quinn Slack](https://x.com/sqs)

### YouTube

- [Ben Dicken](https://www.youtube.com/@benjdicken)
- [The Pragmatic Engineer](https://www.youtube.com/@pragmaticengineer)
