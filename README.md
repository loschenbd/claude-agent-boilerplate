# Claude Agent Boilerplate

A GitHub template for wiring up the **QRSPI multi-agent workflow** in any codebase. Drop it in, fill in four files, and you get a working `/research` + `/d` pipeline powered by Claude Code agents.

---

## What's included

```
.claude/
  commands/
    research.md     # /research command — research phase of QRSPI
    d.md            # /d command — launches the Director
  agents/
    director.md     # Orchestrator: reads registry, delegates, synthesizes
    registry.md     # Team roster: who owns what
    comms-agent.md  # Status updates to the user
    _templates/
      manager.md    # Template for creating a new team manager
      specialist.md # Template for creating a new domain specialist
```

---

## The QRSPI Workflow

QRSPI is a structured pattern for tackling complex engineering tasks with AI agents:

| Phase | Who | What happens |
|-------|-----|-------------|
| **R**esearch | `/research` command | Explore subagent gathers facts — files, patterns, data flows, types, collision points. No opinions. |
| **Q**uestions / **D**esign | Director | Director writes a design doc. Human reviews and corrects before any code is written. |
| **S**pecification / **P**lan | Director | Director writes a plan grounded in the approved design. |
| **I**mplementation | Team Managers + Specialists | Managers break work into specialist assignments. Specialists implement. |

**The two gates** (R-pause and D-pause) are built into the Director. The Director always stops after research and after design to get human confirmation. This prevents wasted work from building on wrong assumptions.

---

## Setup

### 1. Clone into your project

Copy the `.claude/` directory into your repo root:

```bash
cp -r claude-agent-boilerplate/.claude /path/to/your-project/
```

Or use this repo as a GitHub template.

### 2. Fill in four files

| File | What to fill in |
|------|----------------|
| `.claude/agents/registry.md` | Your actual teams and specialists |
| `.claude/agents/director.md` routing table | Map task types → your team managers |
| `.claude/agents/_templates/manager.md` | Copy + fill in once per team |
| `.claude/agents/_templates/specialist.md` | Copy + fill in once per specialist |

Everything else — the QRSPI flow, research command, progress.md pattern — works out of the box.

### 3. Create your team files

For each team, create a manager file and specialist files:

```
.claude/agents/
  engineering/
    manager.md          # copy + fill in _templates/manager.md
    frontend-expert.md  # copy + fill in _templates/specialist.md
    backend-expert.md
  data/
    manager.md
    db-specialist.md
```

Use `_templates/manager.md` and `_templates/specialist.md` as your starting point. Every `[placeholder]` is something you fill in; the surrounding structure stays the same.

### 4. Update the Director's routing table

In `.claude/agents/director.md`, find the routing table and map your task categories to your managers:

```markdown
| If the task involves... | Route to |
|------------------------|----------|
| UI components          | `engineering/manager` → frontend-expert |
| Database schema        | `data/manager` → db-specialist |
```

### 5. Update the Director's project name

In `.claude/agents/director.md`, replace `[PROJECT NAME]` with your project name at the top.

---

## Usage

Once set up, the workflow is:

```
# Start with research (optional but recommended)
/research <describe your task or paste a ticket>

# Review plans/<task-slug>/research.md, then launch the Director
/d <your task description>
```

The Director will:
1. Check for existing research
2. Write a design doc and pause for your review
3. Write an implementation plan and pause for your review
4. Delegate to teams and synthesize results

### Resuming interrupted sessions

If a session ends mid-task, the Director writes `plans/<task-slug>/progress.md` after each phase. Running `/d <same task>` in a new session picks up from where it left off.

---

## Customization contract

| What to customize | What to leave alone |
|-------------------|---------------------|
| Project name in director.md | QRSPI phase logic (steps 1–7) |
| Routing table in director.md | R-pause and D-pause gates |
| registry.md team roster | research.md command structure |
| Agent files for your teams | comms-agent.md behavior |
| _templates/ (fill in, then delete comments) | progress.md format |

---

## Tips

- **Keep registry.md current.** The Director reads it on every task. Stale entries cause misrouting.
- **Be specific in specialist files.** The more concrete the expertise section (file paths, library names, patterns), the better the specialist performs.
- **Use the Comms Agent.** Wire your managers to call it for milestone updates and blockers — it keeps the user informed without cluttering manager output.
- **Don't skip the R-pause.** It's tempting to confirm immediately and charge ahead, but reading the research doc catches wrong assumptions before they become wrong implementations.
