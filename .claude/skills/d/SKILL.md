---
name: d
description: Launch the Director to handle a complex task — runs the full QRSPI flow (research → design → plan → delegate → synthesize) with human review gates between phases. Use when a task touches multiple files or specialists.
---

> **Experimental.** This skill mirrors the `/d` slash command and is provided so other agents/skills can hand off to the Director programmatically. The slash command remains the primary entry point for users.

Launch the Director agent to handle this task. The Director will read the registry, decompose the work, delegate to the appropriate teams, and return a synthesized result.

Pass the user's full request to the Director as-is.

The Director's full flow is documented in `.claude/agents/director.md`. The QRSPI gates (R-pause and D-pause) will surface to the user for review between phases.
