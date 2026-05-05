# Claude Agent Boilerplate

A GitHub template for wiring up the **QRSPI multi-agent workflow** in any codebase. Drop it in, fill in four files, and you get a working `/r` + `/d` pipeline powered by Claude Code agents.

---

## What's included

```
CLAUDE.md.template            # Project-root config template — what Claude reads on every session
.claude/
  settings.json.template      # Permissions, hooks, additionalDirectories template
  commands/
    r.md                      # /r — research phase of QRSPI
    d.md                      # /d — launches the Director
    iterate-research.md       # /iterate-research — re-run research with feedback
    iterate-design.md         # /iterate-design — re-run design with feedback
    verify.md                 # /verify — typecheck/lint/build after implementation
    complete-plan.md          # /complete-plan — archive a finished plan
  agents/
    director.md               # Orchestrator: reads registry, delegates, synthesizes
    registry.md               # Team roster: who owns what
    comms-agent.md            # Status updates to the user
    _templates/
      manager.md              # Template for creating a new team manager
      specialist.md           # Template for creating a new domain specialist
  skills/                     # Experimental — same prompts as commands, callable by other agents
    r/SKILL.md
    d/SKILL.md
docs/
  multi-repo.md               # Coordination repo pattern for multi-repo features
examples/
  universal-rn-nextjs-monorepo/  # Worked example: filled-in agents + sample plans/
```

---

## How the agents fit together

```mermaid
flowchart TD
    User((You))
    User -->|"/r"| ResearchCmd[r.md command]
    User -->|"/d"| DCmd[d.md command]
    DCmd --> Director
    ResearchCmd -.writes.-> Plans[(plans/task-slug/<br/>research, design, plan,<br/>progress.md)]
    Director[Director]
    Director -.reads.-> Registry[(registry.md)]
    Director -.reads/writes.-> Plans
    Director -.calls when needed.-> Comms[Comms Agent<br/>status updates]
    Director --> M1[Engineering<br/>Manager]
    Director --> M2[Data<br/>Manager]
    Director --> M3[Quality<br/>Manager]
    M1 --> S1a[Frontend<br/>Specialist]
    M1 --> S1b[Backend<br/>Specialist]
    M2 --> S2a[DB<br/>Specialist]
    M2 --> S2b[Migration<br/>Specialist]
    M3 --> S3a[Type<br/>Reviewer]

    classDef cmd fill:#e0f2ff,stroke:#0066cc,color:#000
    classDef director fill:#ffe0e0,stroke:#cc0000,color:#000
    classDef manager fill:#fff0d0,stroke:#cc8800,color:#000
    classDef specialist fill:#e0ffe0,stroke:#008800,color:#000
    classDef support fill:#f0e0ff,stroke:#6600cc,color:#000

    class ResearchCmd,DCmd cmd
    class Director director
    class M1,M2,M3 manager
    class S1a,S1b,S2a,S2b,S3a specialist
    class Comms,Registry,Plans support
```

The team structure above is just an example — you define your own teams and specialists in `registry.md`. The Director, Comms Agent, and command structure stay the same.

---

## The QRSPI Workflow

QRSPI is a structured pattern for tackling complex engineering tasks with AI agents:

| Phase | Who | What happens |
|-------|-----|-------------|
| **Q**uestions | Human (you) | Human-written questions about the task — what to build, constraints, edge cases, anything ambiguous. Passed into the `/r` step so research can target them. |
| **R**esearch | `/r` command | Explore subagent gathers facts — files, patterns, data flows, types, collision points. No opinions. |
| **S**pecification / Design | Director | Director writes a design doc. Human reviews and corrects before any code is written. |
| **P**lan | Director | Director writes a plan grounded in the approved design. |
| **I**mplementation | Team Managers + Specialists | Managers break work into specialist assignments. Specialists implement. |

**The two gates** (R-pause and D-pause) are built into the Director. The Director always stops after research and after design to get human confirmation. This prevents wasted work from building on wrong assumptions.

```mermaid
flowchart TD
    Start([You have a task<br/>+ <b>Q</b>: targeted questions])
    Start -->|"/r task"| Explore[Explore subagent<br/>gathers codebase facts]
    Explore --> RDoc[plans/task-slug/<br/>research.md]
    RDoc --> RGate{{"<b>R-pause</b> — you review research<br/><i>/iterate-research to revise</i>"}}
    RGate -->|"/d task"| Director1[Director reads<br/>registry + research]
    Director1 --> Design[design.md<br/>current state, desired state,<br/>constraints, decisions]
    Design --> DGate{{"<b>D-pause</b> — you review design<br/><i>/iterate-design to revise</i>"}}
    DGate -->|approved| Plan[plan.md<br/>objective, tasks,<br/>order, success criteria]
    Plan --> Implement[Managers + Specialists<br/>implement in parallel]
    Implement --> Synth[Director synthesizes<br/>3–5 sentence summary]
    Synth --> Verify{{"<b>V-phase</b> (optional) — /verify<br/><i>failures → re-implement</i>"}}
    Verify -->|pass| Complete["/complete-plan<br/>archive to plans/_done/"]
    Complete --> End([Done])

    classDef gate fill:#fff4d6,stroke:#d4a017,color:#000,stroke-width:2px
    classDef artifact fill:#f0f0f0,stroke:#666,color:#000
    classDef cmd fill:#e0f2ff,stroke:#0066cc,color:#000

    class RGate,DGate,Verify gate
    class RDoc,Design,Plan artifact
    class Explore,Complete cmd
```

The yellow gates are the load-bearing parts. The R-pause and D-pause prevent agents from building on wrong assumptions; the V-phase prevents declaring done before the change actually works. Each gate node carries its iteration/failure path as an italic hint — one command (`/iterate-research`, `/iterate-design`) to redo a phase, or send `/verify` failures back into implementation.

---

## Prerequisites

- **Claude Code** CLI installed and authenticated (`claude` in your PATH) — [install guide](https://docs.anthropic.com/en/docs/claude-code/getting-started)
- A git repository with **at least one commit** — Claude Code agents that run in worktree isolation mode need a commit to branch from
- Branch named **`main`** (not `master`) — recommended for consistency with GitHub defaults and Claude Code tooling
- A `plans/` directory will be created automatically at your repo root when you first run `/r` or `/d`; add it to `.gitignore` if you don't want plans committed

### A note on git worktrees

Some agents in this workflow run in **worktree isolation** — Claude Code spins up a temporary git worktree so the agent's changes don't touch your working tree until you review them. The runner creates and cleans up worktrees automatically. What you need to support this:

1. A git repo with at least one commit (so there's a base to branch from)
2. A reasonably clean working tree before launching agents — uncommitted changes in your working tree won't appear in the agent's isolated copy
3. If the agent makes changes, the worktree path and branch are returned so you can review and merge on your own terms

---

## Setup

```mermaid
flowchart TD
    Start([Want to use this boilerplate])
    Start --> Choice{New project or<br/>existing codebase?}
    Choice -->|Existing codebase| Existing["cp -r .claude/<br/>into your repo"]
    Choice -->|New project| NewChoice{GitHub template<br/>or manual clone?}
    NewChoice -->|Template button| GH["Use this template<br/>on GitHub"]
    NewChoice -->|Manual| Manual[git clone +<br/>reset history]
    Existing --> Config[Configure 6 things]
    GH --> Config
    Manual --> Config
    Config --> C1[1. Customize CLAUDE.md<br/>from template]
    C1 --> C2[2. Customize settings.json<br/>from template]
    C2 --> C3[3. Name the Director]
    C3 --> C4[4. Fill registry.md]
    C4 --> C5[5. Create team files<br/>from _templates/]
    C5 --> C6[6. Update routing table]
    C6 --> Ready([Run /r or /d])

    classDef terminal fill:#d0ffd0,stroke:#008800,color:#000
    classDef choice fill:#fff4d6,stroke:#d4a017,color:#000
    classDef config fill:#e0f2ff,stroke:#0066cc,color:#000

    class Start,Ready terminal
    class Choice,NewChoice choice
    class C1,C2,C3,C4,C5,C6 config
```

Choose the path that matches your situation:

- [Adding to an existing codebase](#adding-to-an-existing-codebase)
- [Starting a new project from scratch](#starting-a-new-project-from-scratch)

Then continue with [Configuring for your project](#configuring-for-your-project).

---

### Adding to an existing codebase

Copy the `.claude/` directory and the project-root templates into your repo:

```bash
BOILER=/path/to/claude-agent-boilerplate
PROJECT=/path/to/your-project

# Agent infrastructure
cp -r $BOILER/.claude $PROJECT/

# Project-root templates (you'll customize these)
cp $BOILER/CLAUDE.md.template $PROJECT/

# Optional but recommended docs
cp -r $BOILER/docs $PROJECT/
```

If your project already has a `.claude/` directory, merge selectively — the key directories to add are `.claude/commands/`, `.claude/agents/`, and `.claude/skills/`. If you already have a `CLAUDE.md`, merge in the "How to work in this repo" section from the template instead of replacing.

Then add `plans/` to your `.gitignore` so the `_done/` history is the only plan directory committed:

```bash
echo "/plans/" >> .gitignore   # only the root plans/, not nested ones in examples/
```

---

### Starting a new project from scratch

**Option A — GitHub template (recommended)**

1. On GitHub, open this repo and click **"Use this template" → "Create a new repository"**
2. Clone your new repo locally
3. Add your project's source code alongside the `.claude/` directory
4. Continue with [Configuring for your project](#configuring-for-your-project)

**Option B — Manual clone**

```bash
git clone https://github.com/loschenbd/claude-agent-boilerplate my-new-project
cd my-new-project

# Start fresh history
rm -rf .git && git init && git branch -m main

# Initial commit (required — worktree isolation needs at least one commit)
git add . && git commit -m "chore: init from claude-agent-boilerplate"

# Set up a remote (recommended for team use and pushing agent worktree branches)
gh repo create my-new-project --public --source . --remote origin --push
# or manually:
# git remote add origin https://github.com/<you>/my-new-project.git
# git push -u origin main
```

Then add your project source and continue below.

---

### Configuring for your project

There are six things to fill in — but you don't have to do them by hand. Each step has a paste-able prompt that hands the work to Claude Code itself. The agent reads the template, inspects your codebase, fills in the placeholders, and asks you about anything it can't infer.

**Want to do all 6 at once?** Paste this in Claude Code and it'll walk through the whole sequence, pausing for your review between steps:

```
I'm setting up this project with the claude-agent-boilerplate QRSPI
workflow. Walk through the 6 setup steps from the README's
"Configuring for your project" section in order: CLAUDE.md,
.claude/settings.json, Director project name, registry.md,
team specialist files, and the Director's routing table.
Pause after each step so I can review before proceeding.
Read each template at the start of its step and ask me about
anything you can't determine from the codebase.
```

Or do them individually:

---

**Step 1 — Project-root `CLAUDE.md`**

Renames the template, then fills in the stack, layout, conventions, and "things not to do" sections based on your codebase.

> **Prompt:**
> ```
> Set up CLAUDE.md for this project. Read CLAUDE.md.template at the
> repo root, then look at package.json (or pyproject.toml, Cargo.toml,
> go.mod, etc.) plus the top-level directory structure to infer the
> stack, layout, and conventions. Rename the template to CLAUDE.md
> and fill in the placeholders. Ask me about anything you can't infer
> from the code — especially "Things NOT to do," which is project
> context only I know.
> ```

<details><summary>Manual alternative</summary>

```bash
mv CLAUDE.md.template CLAUDE.md
# then edit it
```

</details>

---

**Step 2 — `.claude/settings.json`**

Renames the template, strips the `_comment_*` keys (JSON doesn't support comments), and tightens the permissions block based on what your project actually needs.

> **Prompt:**
> ```
> Set up .claude/settings.json. Read .claude/settings.json.template,
> strip all _comment_* keys (those are human guidance, not real JSON),
> and tighten the permissions for this project — look at scripts in
> package.json (or equivalent) to know what Bash commands to add to
> the allow list. Leave additionalDirectories and hooks empty for
> now. Save as .claude/settings.json.
> ```

<details><summary>Manual alternative</summary>

```bash
cd .claude && mv settings.json.template settings.json
# then edit it — strip the _comment_* keys
```

</details>

---

**Step 3 — Name the Director**

Replaces `[PROJECT NAME]` in `director.md` with the actual project name from `package.json` (or equivalent).

> **Prompt:**
> ```
> Replace [PROJECT NAME] in .claude/agents/director.md with the real
> project name (read from package.json's "name" field, or from
> CLAUDE.md if you already filled that in).
> ```

---

**Step 4 — Fill in the registry**

Drafts a sensible team structure for `registry.md` based on your codebase. **Pauses for your approval before writing** — the registry shape determines everything else.

> **Prompt:**
> ```
> Propose a team structure for .claude/agents/registry.md. Read the
> registry template, look at the codebase to figure out what
> specializations make sense (frontend, backend, database, CI, etc.),
> and draft a registry with teams and specialists. Show me the draft
> for approval BEFORE writing the file. Don't generate the individual
> specialist files yet — that's the next step.
> ```

---

**Step 5 — Create your team files**

After the registry is approved, generates a `manager.md` and per-specialist files for each team using `_templates/manager.md` and `_templates/specialist.md`.

> **Prompt:**
> ```
> Now that registry.md is approved, generate the team files. For
> each team listed in registry.md, create a manager.md (from
> .claude/agents/_templates/manager.md) and one file per specialist
> (from _templates/specialist.md). Fill in every [placeholder] using
> the registry, CLAUDE.md, and the actual codebase as context.
> Delete the instruction comments from each generated file before
> saving.
> ```

A filled-in tree looks like:

```
.claude/agents/
  engineering/
    manager.md
    frontend-expert.md
    backend-expert.md
  data/
    manager.md
    db-specialist.md
  _templates/          # keep these for future teams
    manager.md
    specialist.md
```

<details><summary>Manual alternative</summary>

```bash
# Example: creating an engineering team with two specialists
mkdir -p .claude/agents/engineering
cp .claude/agents/_templates/manager.md .claude/agents/engineering/manager.md
cp .claude/agents/_templates/specialist.md .claude/agents/engineering/frontend-expert.md
cp .claude/agents/_templates/specialist.md .claude/agents/engineering/backend-expert.md
# then fill in every [placeholder]
```

</details>

---

**Step 6 — Update the Director's routing table**

Fills in the routing table in `director.md` so each task category maps to a real specialist.

> **Prompt:**
> ```
> Fill in the routing table in .claude/agents/director.md. Use the
> team and specialist names from registry.md, and write task-category
> descriptions that map to each specialist's actual expertise (read
> the specialist files for their domain). Replace the entire example
> table — keep the same column structure.
> ```

The result should look like:

```markdown
| If the task involves... | Route to |
|------------------------|----------|
| UI components          | `engineering/manager` → frontend-expert |
| Database schema        | `data/manager` → db-specialist |
```

---

### Publishing as a GitHub template (optional)

If you want your configured version to be reusable by your team:

1. Push to GitHub
2. Go to **Settings → General** and check **"Template repository"**
3. Team members can now click **"Use this template"** to bootstrap new projects with your org's agent setup already configured

---

## Usage

### Commands

| Command | When to use |
|---------|-------------|
| `/r <task>` | Start of any complex change. Gathers facts; produces `research.md`. No code changes. |
| `/d <task>` | Run the full QRSPI flow: design → plan → delegate → synthesize. Will pause for your review at R-pause and D-pause. |
| `/iterate-research <feedback>` | After rejecting `research.md` — re-runs research with corrective feedback. |
| `/iterate-design <feedback>` | After rejecting `design.md` — re-runs design with corrective feedback. |
| `/verify` | After implementation — runs typecheck/lint/build and reports failures. Optional V-phase. |
| `/complete-plan` | After all plan tasks are done — writes a wrap-up and archives the plan to `plans/_done/`. |

### Typical flow

```bash
/r <describe your task or paste a ticket>
# review plans/<slug>/research.md
# if wrong: /iterate-research <feedback>

/d <your task description>
# review design.md at the D-pause
# if wrong: /iterate-design <feedback>
# review plan.md, confirm
# implementation runs, Director synthesizes

/verify   # optional but recommended

/complete-plan   # archives the work with a wrap-up
```

### Resuming interrupted sessions

If a session ends mid-task, the Director writes `plans/<task-slug>/progress.md` after each phase. Running `/d <same task>` in a new session picks up from where it left off.

---

## Customization contract

| What to customize | What to leave alone |
|-------------------|---------------------|
| `CLAUDE.md.template` (rename + fill in) | QRSPI phase logic in director.md (steps 1–7) |
| `.claude/settings.json.template` (rename, strip `_comment_*` keys) | R-pause / D-pause / V-phase gate behavior |
| Project name in director.md | research.md command structure |
| Routing table in director.md | iterate-research.md / iterate-design.md / verify.md / complete-plan.md |
| registry.md team roster | comms-agent.md behavior |
| Agent files for your teams (from `_templates/`) | progress.md format |
| Team layout under `.claude/agents/` | Skill format under `.claude/skills/` (still experimental) |

---

## Tips

- **Keep registry.md current.** The Director reads it on every task. Stale entries cause misrouting.
- **Be specific in specialist files.** The more concrete the expertise section (file paths, library names, patterns), the better the specialist performs.
- **Use the Comms Agent.** Wire your managers to call it for milestone updates and blockers — it keeps the user informed without cluttering manager output.
- **Don't skip the R-pause.** It's tempting to confirm immediately and charge ahead, but reading the research doc catches wrong assumptions before they become wrong implementations.

---

## Worked example

See [`examples/universal-rn-nextjs-monorepo/`](examples/universal-rn-nextjs-monorepo/) for a fully configured version of this boilerplate plus sample artifacts from a real QRSPI run.

It includes:

- A filled-in `.claude/` for a universal React Native + Next.js monorepo: 4 teams, 17 specialists, complete routing table, cross-team conventions
- Sample plan artifacts at `examples/.../plans/example-add-csv-export/` — `research.md`, `design.md`, `plan.md`, and `progress.md` for a realistic feature, showing exactly what each QRSPI phase produces

[**→ Browse the example**](examples/universal-rn-nextjs-monorepo/)

---

## Multi-repo workflow

For features spanning multiple repos, see [`docs/multi-repo.md`](docs/multi-repo.md). It covers the **coordination repo pattern** — keeping QRSPI agents in a dedicated `coordination/` repo that drives work across sibling repos via `additionalDirectories`, plus an optional `workspaces/<task-slug>/` worktree layout for parallel tasks.

---

## Skills (experimental)

`.claude/skills/r/SKILL.md` and `.claude/skills/d/SKILL.md` mirror the slash commands in skill format, so other agents/skills can invoke them programmatically (not just the user typing `/r`). The skill format is still evolving, so these are experimental — the slash commands remain the primary entry point.

---

## Credits

Special thanks to [Jerry Bruns](https://devjerry.me) ([@devjerry0](https://github.com/devjerry0)). The QRSPI methodology is inspired by the work of [Dex Horthy](https://github.com/dexhorthy) and [HumanLayer](https://github.com/humanlayer) on structured human-in-the-loop AI agent workflows.

Built on [Claude Code](https://docs.anthropic.com/en/docs/claude-code) by [Anthropic](https://www.anthropic.com) — the agentic coding tool that powers the `/r` and `/d` commands, the Director, and all specialist agents in this workflow.
