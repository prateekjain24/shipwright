# How Claude Code Plugins Work - Reference Guide

A synthesis of everything learned during Shipwright research. Covers plugin architecture, primitives, distribution, and ecosystem.

---

## Plugin Structure

Every plugin is a directory with a `.claude-plugin/plugin.json` manifest. Components are auto-discovered from conventional directory names.

```
my-plugin/
  .claude-plugin/
    plugin.json              # Required. Name, version, description.
  skills/                    # Skills (the primary component)
    skill-name/
      SKILL.md               # Markdown + YAML frontmatter
      references/             # Supporting docs Claude reads on demand
  agents/                    # Sub-agent definitions
    agent-name.md            # Markdown + YAML frontmatter
  commands/                  # Legacy slash commands (use skills instead)
    command-name.md
  hooks/
    hooks.json               # Lifecycle hooks
  .mcp.json                  # MCP server configurations
  README.md
```

**Key rules:**
- `.claude-plugin/plugin.json` is always required
- Component dirs go at plugin root, not inside `.claude-plugin/`
- Only create dirs for components actually used
- Kebab-case for all names
- New plugins should use `skills/*/SKILL.md`, not `commands/`

**Source:** https://code.claude.com/docs/en/plugins

---

## plugin.json

Minimal required field is `name`.

```json
{
  "name": "my-plugin",
  "version": "0.1.0",
  "description": "What this plugin does",
  "author": { "name": "Author" },
  "license": "MIT",
  "keywords": ["relevant", "tags"]
}
```

Optional: `homepage`, `repository`, custom component paths.

Use `${CLAUDE_PLUGIN_ROOT}` for all intra-plugin path references in hooks and MCP configs. Never hardcode absolute paths.

---

## Component Types

### Skills (most important)

Files: `skills/skill-name/SKILL.md`

Skills are instruction files Claude reads before executing a task. They encode domain knowledge, workflows, and best practices. Claude loads them on-demand when the user's request matches the skill's description.

**Frontmatter:**
```yaml
---
name: skill-name
description: >
  Third-person description with specific trigger phrases.
  "Use when the user wants to X, Y, or Z."
---
```

**Body:** Instructions FOR Claude (directives), not messages to the user. Written in imperative form.

**Progressive disclosure:** Keep SKILL.md body under 3,000 words. Put detailed reference content in `references/` subdirectory. Claude reads references when it needs deeper context.

**Trigger:** Skills trigger automatically based on description matching. Also available as `/plugin-name:skill-name` slash commands.

### Agents (sub-agents)

Files: `agents/agent-name.md`

Autonomous workers that Claude dispatches for complex sub-tasks. Each agent runs independently with its own context.

**Frontmatter:**
```yaml
---
name: agent-name
description: >
  When to dispatch this agent.
model: sonnet  # or opus, haiku
isolation: worktree  # optional: run in isolated git worktree
tools:
  - Read
  - Write
  - Bash
  - Grep
  - Glob
  - WebSearch
---
```

**Body:** System prompt for the agent. Include `<example>` blocks showing triggering conditions.

**Key feature:** Agents can run in parallel. Multiple agents can be dispatched simultaneously for independent tasks (e.g., research + code review in parallel).

**Worktree isolation:** Set `isolation: worktree` in frontmatter to give each agent its own git worktree. Essential for parallel code generation to avoid conflicts.

### Hooks

File: `hooks/hooks.json`

Lifecycle automation that fires at specific points. Two types: prompt-based (LLM evaluates) and command-based (deterministic script).

**Hook events:**
- `PreToolUse` -- before a tool is called
- `PostToolUse` -- after a tool completes
- `Stop` -- before Claude stops responding
- `SessionStart` -- when a session begins
- `WorktreeCreate` -- when a worktree is created
- `WorktreeRemove` -- when a worktree is removed

**Example (command-based):**
```json
{
  "hooks": [
    {
      "event": "PreToolUse",
      "matcher": { "tool": "Write" },
      "type": "command",
      "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
    }
  ]
}
```

**Use cases:** Pre-deploy quality gates, auto-formatting, security pattern detection, context loading.

### MCP Servers

File: `.mcp.json` at plugin root.

Connect Claude to external services (databases, APIs, SaaS tools). Standard MCP protocol.

**Types:**
- `stdio` -- local process (most common for plugins)
- `sse` -- hosted server with OAuth
- `http` -- REST API wrapper

```json
{
  "mcpServers": {
    "my-server": {
      "type": "stdio",
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/mcp/server.js"],
      "env": {
        "API_KEY": "${API_KEY}"
      }
    }
  }
}
```

### Commands (legacy)

Files: `commands/command-name.md`

Single-file slash commands. Still work, but new plugins should use skills instead. Cowork UI presents both as "Skills."

---

## Distribution

### Plugin file format

Plugins are distributed as `.plugin` files (zip archives of the plugin directory).

```bash
cd plugin-dir && zip -r /tmp/my-plugin.plugin . -x "*.DS_Store"
```

### Marketplaces

Marketplaces are git repos containing multiple plugins. Users add marketplaces and install plugins from them.

```bash
# Add a marketplace
/plugin marketplace add https://github.com/org/marketplace

# Install from marketplace
/plugin install plugin-name@marketplace-name

# List installed
/plugin  # opens interactive UI

# Enable/disable/uninstall
/plugin disable plugin-name@marketplace-name
/plugin enable plugin-name@marketplace-name
/plugin uninstall plugin-name@marketplace-name
```

Official marketplace lives at `~/.claude/plugins/marketplaces/claude-plugins-official/plugins/`.

**Source:** https://code.claude.com/docs/en/discover-plugins

### Cowork distribution

In Cowork (desktop app), plugins can be presented as `.plugin` files in chat. Users see a rich preview card with a "Save skill" install button. The Cowork plugin management system handles installation.

---

## Git Worktrees

Native Claude Code feature. Essential for parallel agent work.

**CLI usage:**
```bash
claude --worktree feature-name    # named worktree
claude --worktree                 # auto-named
```

**Sub-agent worktrees:**
Set `isolation: worktree` in agent frontmatter. Each sub-agent gets its own worktree. Auto-cleaned when agent finishes without changes.

**`.worktreeinclude`** at project root -- lists gitignored files to copy into worktrees:
```
.env
.env.local
config/secrets.json
```

**Cleanup:** No changes = auto-remove. Changes exist = prompt to keep/remove.

**Add `.claude/worktrees/` to .gitignore.**

**WorktreeCreate hook:** Can customize worktree setup (e.g., create databases, install deps).

**Source:** https://code.claude.com/docs/en/common-workflows

**Creator's tips (Boris Cherny, Feb 2026):**
1. `claude --worktree` for isolation
2. Worktree mode in Desktop app
3. Sub-agents support worktrees
4. Custom agents can set `isolation: worktree` in frontmatter
5. Also works for non-git VCS via hooks

**Source:** https://www.threads.com/@boris_cherny/post/DVAAoZ3gYut/

---

## Cowork-Specific Features

Cowork is the desktop app mode. Plugins work in both Claude Code (terminal) and Cowork, but Cowork adds:

- **Local file access** -- read/write to user's filesystem
- **Scheduled tasks** -- recurring automation (cron or one-time)
- **Computer use** -- screen control as fallback
- **MCP connectors** -- pre-built integrations (Slack, Jira, Gmail, Google Drive, Calendar)
- **Professional outputs** -- PPTX, XLSX, DOCX, PDF generation
- **Sub-agent coordination** -- parallel workstreams
- **Projects** -- persistent workspaces with memory
- **Mobile dispatch** -- assign tasks from phone

**Source:** https://support.claude.com/en/articles/13345190-get-started-with-cowork

---

## Existing Plugin Ecosystem (as of April 2026)

### Official Anthropic plugins (50+ in official marketplace)
- `feature-dev` -- 7-phase feature development workflow
- `code-review` -- automated PR review with 5 parallel agents
- `ralph-loop` -- autonomous multi-hour coding sessions
- `frontend-design` -- UI/UX design patterns
- `security-guidance` -- security pattern monitoring
- `commit-commands` -- git workflow automation
- `claude-md-management` -- auto-maintains CLAUDE.md
- `linear` -- Linear issue tracker integration

### PM-focused plugins
- **pmcopilot** -- 12+ skills: PRD, roadmap, sprint review, competitive teardown, metrics review, experiment design, market sizing, launch checklist, stakeholder update, user research, prioritization, app store intel
- **pmprompt** -- 26+ PM frameworks (open source)
- **Product Management skill** (ooiyeefei) -- competitor research, gap analysis, PRDs via `.pm/` folder

### Workflow plugins
- **Superpowers** (Prime Radiant) -- brainstorm -> worktree -> plan -> execute with hard gates
- **Get Shit Done** -- spec-driven workflows with multi-agent orchestration
- **Artifex** -- structured 16-step ticket workflow with `/next-ticket`, `/ticket-status`, `/mark-done`
- **Workflow Orchestrator** -- task breakdown and sub-agent delegation
- **tickets-plugin** (boomship) -- file-based issue tracker

### Marketplaces and directories
- Official: `~/.claude/plugins/marketplaces/claude-plugins-official/`
- Skills.sh: https://skills.sh/ -- 24K+ community skills
- Claude Marketplaces: https://claudemarketplaces.com/
- awesome-claude-plugins: https://github.com/Chat2AnyLLM/awesome-claude-plugins
- awesome-claude-code-subagents: https://github.com/VoltAgent/awesome-claude-code-subagents (100+ subagents)
- alirezarezvani/claude-skills: https://github.com/alirezarezvani/claude-skills (220+ skills)

### Key references
- Plugin creation docs: https://code.claude.com/docs/en/plugins
- Plugin discovery docs: https://code.claude.com/docs/en/discover-plugins
- Plugins reference (technical spec): https://code.claude.com/docs/en/plugins-reference
- Common workflows (worktrees, etc.): https://code.claude.com/docs/en/common-workflows
- Cowork getting started: https://support.claude.com/en/articles/13345190-get-started-with-cowork
- Next.js AI agents guide: https://nextjs.org/docs/app/guides/ai-agents
- Sachin Rekhi's PM guide: https://www.sachinrekhi.com/p/claude-code-for-product-managers
- prodmgmt.world PM plugins list: https://www.prodmgmt.world/claude-code

---

## Key Lessons Learned

1. **Skills > Commands.** Skills with `references/` subdirectories are the modern pattern. Commands are legacy.

2. **Agents are underused.** Most plugins have skills but not agents. Parallel agent dispatch (like pmcopilot's competitive-teardown using app-teardown + web-teardown in parallel) is powerful and rare.

3. **Worktrees are the key to parallel code generation.** Without them, multiple agents editing the same files will conflict.

4. **State is the hard problem.** Most plugins are stateless (run a command, get output, forget). Stateful plugins (maintaining project context across sessions) are the frontier.

5. **The plugin ecosystem is still early.** 50+ official plugins, hundreds of community ones, but most are thin wrappers around prompts. Few use the full primitive set (skills + agents + hooks + MCP + worktrees).

6. **Cowork and Claude Code share the same plugin format** but Cowork adds scheduled tasks, computer use, and MCP connectors that terminal doesn't have.

7. **`.plugin` files are just zipped directories.** Distribution is simple. The hard part is writing good skills.

8. **Progressive disclosure matters.** SKILL.md should be lean (<3K words). Deep content goes in `references/`. Claude reads references on demand.

9. **Description quality determines trigger accuracy.** A skill's frontmatter description is how Claude decides whether to load it. Specific trigger phrases > vague descriptions.

10. **Next.js 16+ ships AGENTS.md and CLAUDE.md automatically** via `create-next-app`. Bundled docs in `node_modules/next/dist/docs/` give agents version-matched API references.
