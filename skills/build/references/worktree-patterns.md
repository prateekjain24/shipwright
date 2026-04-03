# Worktree Patterns for Build Phase

This doc is for agents. Read this before spawning a code-agent for a ticket, and pass it along so the agent knows how to isolate and commit work.

---

## One Worktree per Ticket

Each ticket gets its own git worktree. Why?

- **Isolation:** One agent per worktree. No merge conflicts while tickets are in flight.
- **Parallel execution:** Multiple agents can build simultaneously without stepping on each other.
- **Clean commits:** Each ticket's work is in one branch, easy to review and roll back.
- **No build breakage:** If ticket 003 breaks main, ticket 004 isn't blocked -- it still has its own isolated branch.

---

## Worktree Metadata

When spawning a code-agent, pass:

```yaml
isolation: worktree
```

This tells the agent:

1. You're working in a worktree, not the main branch.
2. You have no network access to other agents' changes while you're building.
3. You must commit your work when done.
4. The orchestrator (Build skill) will merge your branch back to main.

---

## Creating and Using Worktrees

### Setup (done by Build skill)

```bash
git worktree add .claude/worktrees/TICKET-003 -b ticket-003-habit-crud
cd .claude/worktrees/TICKET-003
```

This creates a new branch `ticket-003-habit-crud` and checks it out. The agent works here.

### During Build (agent does this)

1. Make code changes
2. Run tests if available
3. Commit: `git commit -m "TICKET-003: Habit CRUD"`
   - Message format: `TICKET-NNN: {ticket-title}`
   - This makes it easy to trace which ticket each commit belongs to
4. If multi-step, commit multiple times:
   - `git commit -m "TICKET-003: Add Habit schema"`
   - `git commit -m "TICKET-003: Add CRUD endpoints"`
   - `git commit -m "TICKET-003: Add UI"`
   - **Do not squash.** Intermediate commits help with understanding the work.
5. When done, report the final commit hash

### After Build (orchestrator does this)

1. Read the commit hash from the ticket file (.shipwright/tickets/TICKET-003.md)
2. Check out main: `git checkout main`
3. Merge the worktree branch: `git merge ticket-003-habit-crud`
4. Clean up the worktree: `git worktree remove .claude/worktrees/TICKET-003`

---

## The `.worktreeinclude` File

Create `.worktreeinclude` at project root:

```
.env
.env.local
.shipwright/project.json
```

This tells git which files to **share** across worktrees (instead of copying them). Why?

- `.env`, `.env.local`: Agents need the same secrets/config to run.
- `.shipwright/project.json`: Agents need to read (but not write) the same project metadata.

Everything else is isolated per worktree.

**Implementation note:** Some git versions don't support .worktreeinclude natively. In that case, copy these files to each worktree before the agent starts building.

---

## Commit Tracking

The Build skill updates `.shipwright/SHIPWRIGHT.md` with commit hashes:

```markdown
| Ticket | Status | Points | Commit Hash |
|--------|--------|--------|-------------|
| TICKET-001 | done | 1 | a1b2c3d |
| TICKET-002 | done | 1 | e4f5g6h |
| TICKET-003 | in-progress | 1 | |
```

When a code-agent finishes:

1. Agent reports its final commit hash (from `git rev-parse HEAD`)
2. Build skill reads it and updates the table
3. This creates an audit trail: which tickets shipped, exactly what code went into each

---

## Merge Order and Conflict Resolution

After all tickets are done, merge back to main in **dependency order** (reverse order of execution):

1. Tickets with no dependents first (TICKET-009 if it's a leaf)
2. Work backward through the dependency chain
3. OR: Merge in the order they were completed (usually fine)

**Conflict resolution:**

- If ticket A and B edited the same file, git will detect the conflict
- Build skill surfaces the conflict and asks the builder: "TICKET-A and TICKET-B both edited users.ts. Review and decide: [diff preview]"
- Builder resolves (or instructs agent to resolve), commit the merge

**Prevent conflicts:**

- Clear dependencies prevent most conflicts
- If two tickets are editing the same file, they should either be dependent (B depends on A) or be combined into one ticket

---

## `.claude/worktrees/` in `.gitignore`

Add to `.gitignore`:

```
.claude/worktrees/
```

Worktree directories are temporary. They should never be committed to the repo.

---

## Cleanup Rules

After a ticket finishes:

| Scenario | Action |
|----------|--------|
| Agent finishes, commits cleanly | Merge branch to main, remove worktree |
| Agent finishes, no changes | Remove worktree (no merge) |
| Agent abandons ticket / scope cut mid-build | Remove worktree (no merge) |
| Agent builds but tests fail | Do not merge. Ask builder: "TICKET-003 built but tests failed. Fix or skip?" |
| Merge conflict on merge | Surface conflict, ask builder to resolve before finalizing |

**No orphaned worktrees.** After Build phase, all worktrees should be cleaned up and merged back to main (or discarded).

---

## Example: Build Workflow with Two Tickets

Start state: main branch, clean working directory

```bash
# Ticket 001
git worktree add .claude/worktrees/TICKET-001 -b ticket-001-schema
cd .claude/worktrees/TICKET-001
# ... agent builds, commits: "TICKET-001: User schema"
cd ../..
git checkout main
git merge ticket-001-schema
git worktree remove .claude/worktrees/TICKET-001

# Ticket 002 (depends on 001, so starts after 001 is merged)
git worktree add .claude/worktrees/TICKET-002 -b ticket-002-crud
cd .claude/worktrees/TICKET-002
# ... agent builds, uses the schema from 001, commits: "TICKET-002: User CRUD"
cd ../..
git checkout main
git merge ticket-002-crud
git worktree remove .claude/worktrees/TICKET-002
```

Result: main branch has both commits, in order. Worktrees cleaned up.

---

## For Agents Reading This

When Build skill spawns you with `isolation: worktree`:

1. You're in `.claude/worktrees/TICKET-NNN/`
2. Make your changes
3. Commit with `git commit -m "TICKET-NNN: {title}"`
4. Report your final commit hash (run `git rev-parse HEAD` and include it in your output)
5. Do not merge or push (that's the orchestrator's job)
6. Do not create other branches
7. If you need to undo something, use `git reset` within your worktree
8. If you're stuck, ask Build skill: "This ticket is blocked on [reason]"

The Build skill will handle cleanup.
