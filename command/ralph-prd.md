---
description: PRD-aware Ralph Loop - incremental feature development until all features pass
---

# Ralph PRD Loop

Incremental development loop that implements features ONE AT A TIME until all features in the PRD pass.

## How This Works

1. Read `plans/prd.json` (flat array of features)
2. Read `plans/progress.txt` for context
3. Select ONE failing feature
4. Implement and verify
5. Update PRD and progress
6. Output `<promise>DONE</promise>` when ALL features pass

## Phase 1: Get Bearings (EVERY iteration)

```bash
cat plans/progress.txt 2>/dev/null || echo "No progress file"
git log --oneline -5 2>/dev/null || echo "No git history"
cat plans/prd.json
```

Count: total features, passing (`passes: true`), failing (`passes: false`)

## Phase 2: Check Completion

If ALL features have `passes: true`:
1. Update plans/progress.txt with final status
2. Output: `<promise>DONE</promise>`
3. Stop

## Phase 3: Select ONE Feature

From failing features, select based on:
1. **Category priority**: `error` > `user-flow` > `behavior` > `api` > `ui`
2. **Dependencies**: Only if all dependencies pass
3. **Array order**: If tied, pick first

**CRITICAL: ONE feature only. Not two. Not "while I'm here". ONE.**

Announce:
```
Selected: [id]
Category: [category]
Description: [description]
```

## Phase 4: Implement

1. Run project init if needed:
   ```bash
   ./.ralph/init.sh 2>/dev/null || true
   ```

2. Implement following existing code patterns

3. **CI checks (REQUIRED):**
   ```bash
   bun run typecheck
   bun run test
   ```
   Both MUST pass before proceeding.

4. Verify using the feature's `steps` array

## Phase 5: Update State

### If PASSES (typecheck + tests + verification):

1. Update PRD - set `passes: true` for this feature

2. Append to plans/progress.txt:
   ```
   [id] COMPLETE
   Summary: [what was implemented]
   Files: [changed files]
   ```

3. Git commit:
   ```bash
   git add -A
   git commit -m "feat([id]): [brief description]"
   ```

### If FAILS:

1. **STOP** after 2-3 attempts
2. Revert: `git checkout -- .`
3. Document in plans/progress.txt:
   ```
   [id] FAILED
   Attempted: [what you tried]
   Error: [what went wrong]
   Next: [suggestion for next iteration]
   ```
4. Do NOT mark as passing
5. Loop continues to next iteration

## Phase 6: Loop Control

1. Re-read PRD
2. If ALL pass: `<promise>DONE</promise>`
3. If NOT all pass: loop auto-continues

## Git Strategy

**Commit after EVERY successful feature.**

```
feat([id]): [brief description]
```

| Situation | Action |
|-----------|--------|
| Feature passes | `git add -A && git commit` |
| Feature fails | `git checkout -- .` (revert) |
| Context ending | Commit WIP with clear message |

Recovery:
```bash
git checkout -- .             # Revert uncommitted
git log --oneline -5          # Find last good
```

## Rules

- **ONE feature per iteration**
- **CI must pass** - typecheck + tests
- **Commit per feature**
- **Revert if stuck**
- **Document everything** in progress.txt
