# Primitives Map

How Claude Code plugin primitives map to Shipwright's phases.

| Primitive | Shape | Build | Ship | Learn | Brain |
|-----------|-------|-------|------|-------|-------|
| **Skills** | Reflection + validation + research | Ticket breakdown + stack setup + mid-build reflection | Deploy + SEO + analytics + GTM | Retrospective + re-engagement | State management |
| **Agents** | research-agent (competitor scan, signs of demand) | code-agent (worktree-isolated), review-agent | -- | -- | -- |
| **Hooks** | -- | Pre-build: verify pitch exists | Pre-ship: run tests | -- | Post-phase: update SHIPWRIGHT.md |
| **Worktrees** | -- | One per ticket (isolation: worktree) | -- | -- | -- |
| **Scheduled Tasks** | -- | -- | Post-deploy check-in (7 days) | Weekly metrics summary (optional) | -- |
| **Memory** | Save decisions, validation results | Save tech choices, architecture decisions | Save deploy config, distribution plan | Save learnings, cycle outcomes | Sync all |
| **MCP Connectors** | Web search | GitHub | Vercel (future) | Analytics (future) | -- |
| **File State** | SHIPWRIGHT.md (pitch, validation, research) | SHIPWRIGHT.md (tickets) + ticket files | SHIPWRIGHT.md (deploy, distribution) | SHIPWRIGHT.md (learnings) + cycle archive | SHIPWRIGHT.md (all) |

---

## Primitive Notes

**Skills** are the core unit. Each phase is a skill. The orchestrator skill (`skills/shipwright/`) manages transitions between them.

**Agents** run as sub-processes with specific capabilities. research-agent and code-agent are the two that do heavy lifting. review-agent is lighter -- it reads code and flags issues.

**Worktrees** are only used during Build. Each code-agent instance gets an isolated worktree so multiple tickets can be worked in parallel without conflicts.

**Scheduled Tasks** are primarily used for re-engagement (see [learn.md](learn.md)). They require Cowork mode. In Claude Code, the same functionality is approximated via SHIPWRIGHT.md reminders.

**Memory** persists across sessions. Key things to remember: project decisions, builder preferences, tech stack choices, what worked and what didn't.

**MCP Connectors** are external integrations. V1 uses web search (for research-agent). Future versions add GitHub (for automated PRs), Vercel (for deploy status), and analytics platforms (for Learn).
