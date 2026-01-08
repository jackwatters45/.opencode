---
description: PRD-aware Ralph Loop - incremental feature development until all features pass
---

# Ralph PRD Loop

You are starting a PRD-aware Ralph Loop - an incremental development loop that implements features ONE AT A TIME until all features in the PRD pass.

## How This Works

1. Read `docs/prd.json` to get the feature list
2. Read `docs/progress.txt` to understand previous work
3. Select ONE failing feature (highest priority, dependencies satisfied)
4. Implement and verify that ONE feature
5. Update PRD and progress file
6. Output `<promise>DONE</promise>` ONLY when ALL features pass

## Phase 1: Get Your Bearings (EVERY iteration)

Execute these commands FIRST:

```bash
pwd
cat docs/progress.txt 2>/dev/null || echo "No progress file"
git log --oneline -5 2>/dev/null || echo "No git history"
cat docs/prd.json
```

Count features:
- Total features
- Passing features (`"passes": true`)
- Failing features (`"passes": false`)

## Phase 2: Check Completion

If ALL features have `"passes": true`:
1. Update docs/progress.txt with final status
2. Output: `<promise>DONE</promise>`
3. Stop working

## Phase 3: Select ONE Feature

From failing features, select the SINGLE highest priority:

1. **Priority order**: high > medium > low
2. **Dependencies**: Only select if all dependencies pass
3. **Document order**: If tied, pick first in array

**CRITICAL: Work on exactly ONE feature. Not two. Not "while I'm here". ONE.**

Announce your selection:
```
Selected feature: [id]
Description: [description]
Reason: [why this one]
```

## Phase 4: Implement

1. Run project init if needed:
   ```bash
   ./.ralph/init.sh 2>/dev/null || true
   ```

2. Implement the feature following existing code patterns

3. **Run CI checks (REQUIRED before marking complete):**
   ```bash
   bun run typecheck
   bun run test
   ```
   - Both MUST pass before proceeding
   - If typecheck fails: fix type errors first
   - If tests fail: fix or update tests

4. Run feature-specific verification:
   - Follow the `verification.steps` in the PRD
   - If `verification.command` exists, run it
   - Test manually if needed

## Phase 5: Update State

### If feature PASSES all checks (typecheck + tests + verification):

1. Update PRD - mark feature as passing:
   ```bash
   # Use jq or manual edit to set passes: true in docs/prd.json
   ```

2. Append to docs/progress.txt:
   ```
   [id] COMPLETE
   Summary: [what was implemented]
   Files: [changed files]
   Checks: typecheck pass, tests pass
   Verified: [how verified]
   ```

3. Git commit (if git repo):
   ```bash
   git add -A
   git commit -m "feat([id]): [brief description]"
   ```

### If feature FAILS (typecheck, tests, or verification):

1. **STOP trying random fixes** after 2-3 attempts
2. Revert changes:
   ```bash
   git checkout -- . 2>/dev/null || true
   ```
3. Document failure in docs/progress.txt:
   ```
   [id] FAILED
   Attempted: [what you tried]
   Checks: typecheck [pass/fail], tests [pass/fail]
   Error: [what went wrong]
   - Next approach: [suggestion for next iteration]
   ```
4. Do NOT mark as passing
5. Let the loop continue to next iteration

## Phase 6: Loop Control

After updating state:

1. Re-read PRD to check if ALL features now pass
2. If ALL pass: output `<promise>DONE</promise>`
3. If NOT all pass: the loop will auto-continue

## Git Strategy

**Commit after EVERY successful feature.** This is critical for:
- Recovery (can revert to last working state)
- Progress tracking (git log shows feature completion)
- Handoff between iterations (next iteration sees clean state)

### Commit Convention

```
feat([feature-id]): [brief description]

- Implements [what]
- Verified by [how]
```

Example:
```
feat(auth-001): implement login form

- Added login page with email/password fields
- Added /api/auth/login endpoint
- Verified: manual test, redirects to dashboard
```

### When to Commit

| Situation | Action |
|-----------|--------|
| Feature passes verification | `git add -A && git commit` |
| Feature fails | `git checkout -- .` (revert, no commit) |
| Partial progress, context ending | Commit WIP with clear message |
| PRD/progress.txt updated | Include in feature commit |

### Recovery via Git

If you break something:
```bash
git status                    # See what changed
git diff                      # Review changes
git checkout -- .             # Revert ALL uncommitted changes
git log --oneline -5          # Find last good commit
git checkout [commit] -- .    # Restore specific commit (nuclear option)
```

## Rules

- **ONE feature per iteration** - strict incrementalism prevents context exhaustion
- **CI must pass** - `bun run typecheck` and `bun run test` before marking complete
- **Commit after each feature** - git is your checkpoint system
- **Verify before marking complete** - follow PRD verification steps
- **Revert if stuck** - don't pile fixes on broken code
- **Document everything** - docs/progress.txt is memory between iterations
- **Read before writing** - never modify files you haven't read
- **Match existing patterns** - follow codebase conventions

## Arguments

Parse from: `$ARGUMENTS`

Format: `"task description" [--max-iterations=N]`

The task description provides context but the PRD defines actual features.
Default max iterations: 100
