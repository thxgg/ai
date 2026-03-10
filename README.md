# AI

## How I Use AI

I use AI coding agents as my primary development workflow. My setup is built around [OpenCode](https://opencode.ai), a terminal-based AI coding tool that I've heavily customized through agents, skills, MCP integrations, plugins, and granular permission controls — all managed as dotfiles via GNU Stow.

### Tools

**OpenCode** is my primary tool. I use it in the terminal with a multi-agent, multi-model architecture. I also have **Amp** configured as a secondary tool with a lighter setup (Exa search and GitHub MCP servers).

I don't use GitHub Copilot, Cursor, or any other AI-assisted editor/IDE. My editor is Neovim with no AI plugins — all AI interaction happens through the dedicated agent tools.

### Models

I use a multi-model strategy, picking models based on task characteristics:

| Model | Role | Why |
|-------|------|-----|
| **Claude Opus 4** | Primary coding — planning and building | Best at agentic coding with long-context reasoning |
| **GPT-5.4** | Deep reasoning, architecture advice | Strong at strategic thinking and tradeoff analysis |
| **Gemini 3.1 Pro** | Code review | Good at systematic, evidence-based review |
| **Claude Sonnet 4** | Reference tasks, documentation, codebase exploration | Fast and capable for lookup-heavy work |
| **Gemini 3 Flash** | Commit messages | Cheap and fast for simple, structured output |

### Agents

I define specialized agents with distinct roles, model assignments, and permission scopes:

**Primary agents** (I switch between these directly):
- **plan** — Read-only mode using Claude Opus 4. For analyzing code, planning changes, and reasoning before writing anything.
- **build** — Full-access mode using Claude Opus 4. For implementing changes, running builds, committing code.
- **deep** — Full-access mode using GPT-5.4. For problems that benefit from deeper reasoning or a different perspective.

**Subagents** (invoked by primary agents or commands as specialists):
- **oracle** — GPT-5.4 in read-only mode. Senior engineering advisor for architecture decisions. Returns structured responses (TL;DR, Recommendation, Rationale, Risks, When to Reconsider). Emphasizes YAGNI and simplicity.
- **review** — Gemini 3.1 Pro. Code reviewer focused on bugs, security, and maintainability. Requires evidence-backed findings with severity ratings and file:line references.
- **librarian** — Claude Sonnet 4. Explores remote codebases across GitHub, npm, PyPI, and crates.io to understand library internals.
- **opencode-expert** — Claude Sonnet 4. Answers questions about OpenCode configuration and features using built-in documentation.

### MCP Servers

I extend agent capabilities through Model Context Protocol servers:

| Server | Purpose |
|--------|---------|
| **GitHub** | Full GitHub API access — issues, PRs, repos, code search |
| **Exa** | Web search and content discovery |
| **opensrc** | Remote repository exploration (fetch and search GitHub repos, npm/PyPI/crates packages without cloning) |
| **Steward** | My own published MCP server ([`@thxgg/steward`](https://www.npmjs.com/package/@thxgg/steward)) for repository-aware PRD and task management |

I also have **Playwright** (browser automation), **Figma** (design integration), and **Overseer** (task persistence) configured but currently disabled.

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
- **prd / prd-task / complete-next-task** — Full PRD lifecycle: guided discovery → structured document → executable task breakdown → automated implementation with commit tracking.

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
- `/frontend-design`, `/web-animation-design` — Design-focused skill invocations.

### Plugins

I've written custom OpenCode plugins in TypeScript (currently archived but representative of the kind of tooling I build):

- **Worktree Session Plugin** (~1,200 lines) — Creates git worktrees, forks OpenCode sessions, opens them in tmux or Ghostty. Uses Bun SQLite for persistent branch-to-session tracking. Handles Shortcut story ID resolution for branch naming.
- **Preemptive Compaction Plugin** (~280 lines) — Monitors context window usage and triggers automatic summarization when context reaches 80% capacity. Knows per-model context limits, has cooldown logic, and auto-continues after compaction.

### Permission Model

I maintain a granular permission system that balances autonomy with safety:

- **File reads**: Allow all except `.env`, `.envrc`, `.dev.vars`, `secrets/*`, and MCP auth files.
- **External directories**: Auto-allow `~/Projects/**` and `~/.config/**`; prompt for anything else.
- **Bash commands**: Tiered — most git operations, runtimes, package managers, and build tools are allowed. Destructive operations (`git push`, `rm`, `kill`, merge/close/delete on GitHub) require confirmation. Hard resets and force pushes are denied outright.
- **Doom loop detection**: Prompts for confirmation to prevent runaway agent loops.

### Workflow

My typical workflow looks like:

1. **Plan** — Switch to the `plan` agent (read-only). Explore the codebase, reason about the problem, consult the `oracle` for architecture advice if needed.
2. **Build** — Switch to the `build` agent. Implement changes, run tests, iterate.
3. **Review** — Run `/code-review` for multi-pass automated review with deduplication and validation.
4. **Ship** — Run `/pr` to push and create a pull request, or `/commit-release` for versioned packages.

For complex features, I use the PRD skills to go from problem discovery → scoped requirements document → task breakdown → automated implementation with commit tracking.

### Fun Details

- Notification sounds are World of Warcraft quest accept/complete chimes and a Gears of War active reload sound.
- The entire configuration lives in a dotfiles repo managed with GNU Stow, so spinning up a new machine gets the full AI setup automatically.
- The dotfiles repo itself is documented with hierarchical `AGENTS.md` files (generated by the `index-knowledge` skill), so agents have structured context when working on the dotfiles themselves.

## Resources

### X (Twitter)

People I follow who shape how I think about developer tooling, AI-assisted coding, and software engineering:

- [Dax](https://x.com/thdxr) — co-creator of SST and OpenCode
- [Dillon Mulroy](https://x.com/dillon_mulroy) — contributor to OpenCode
- [Mitchell Hashimoto](https://x.com/mitchellh) — creator of Ghostty, co-founder of HashiCorp
- [Thorsten Ball](https://x.com/thorstenball) — software engineer at Sourcegraph, author of *Writing An Interpreter/Compiler In Go*
- [Adam Elmore](https://x.com/adamdotdev) — AWS Hero, co-founder of StatMuse, contributor to OpenCode
- [Quinn Slack](https://x.com/sqs) — CEO of Sourcegraph

### YouTube

- [Ben Dicken](https://www.youtube.com/@benjdicken) — programming deep-dives and visualizations
- [The Pragmatic Engineer](https://www.youtube.com/@pragmaticengineer) — software engineering industry analysis and interviews with builders
