# Example Cursor Rule

Save this as `.cursor/rules/routines.mdc` in your project (adjust the frontmatter format to match your Cursor version).

```
---
description: At the start of every conversation, run the routines orchestrator to check daily/weekly routines and scheduled tasks
alwaysApply: true
---

# Routines & Scheduled Tasks

At the **start of every conversation**, before doing anything else:

1. Read `[path-to]/routines/ORCHESTRATOR.md` for the full flag logic and execution rules
2. Read `[path-to]/routines/state.md` to check the current state
3. Execute the flag logic from the orchestrator:
   - If `date` < today: reset flags, update date, identify due routines
   - If `date` == today and flags == 1: routines already done, skip
   - If `date` == today and flags == 0: identify incomplete routine steps
4. If routines are due, present a brief summary and ask if the user would like to run them
5. After handling routines, check for any additional scheduled tasks

Each routine step is a skill. When running a routine, read the skill file and follow its workflow. Update state.md after each skill completes.

If no routines or tasks are due, proceed normally without mentioning it.
```

Replace `[path-to]` with the actual path to your routines folder in your workspace.
