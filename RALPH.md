# Ralph PRD System

PRD-based incremental feature development for oh-my-opencode's Ralph Loop.

## Commands

- `/init-prd` - Initialize PRD structure (creates docs/prd.json, docs/progress.txt, .ralph/init.sh)
- `/ralph-prd "task"` - Run PRD-aware development loop until all features pass
- `/archive-prd [version]` - Archive completed PRD to docs/archive/{version}/

## File Structure (per-project)

```
project/
├── docs/
│   ├── prd.json         # Feature list with pass/fail tracking
│   ├── progress.txt      # Session history and memory
│   └── archive/         # Completed PRDs by version
└── .ralph/
    └── init.sh          # Project-specific dev setup
```

## PRD Feature Format

```json
{
  "id": "feature-001",
  "category": "category-name",
  "priority": "high|medium|low",
  "description": "What this feature does",
  "passes": false,
  "dependencies": ["other-feature-id"],
  "verification": {
    "steps": ["Step 1", "Step 2"],
    "automated": false,
    "command": null
  }
}
```

## Core Rules

1. **ONE feature per iteration** - never implement multiple features at once
2. **CI must pass** - run `bun run typecheck` and `bun run test` before marking complete
3. **Commit per feature** - git commit after each successful feature
4. **Revert on failure** - `git checkout -- .` if stuck, document in progress.txt
5. **Document everything** - progress.txt is memory between iterations

## Workflow

1. `/init-prd` → creates structure
2. Edit `docs/prd.json` with features
3. `/ralph-prd "implement"` → loops until all pass
4. `/archive-prd v1.0.0` → archives, resets for next cycle
