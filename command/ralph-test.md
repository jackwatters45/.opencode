---
description: Test coverage Ralph Loop - incrementally increase test coverage until target reached
---

# Ralph Test Coverage Loop

Incremental test coverage loop that writes ONE test at a time until coverage target is reached.

## How This Works

1. Run coverage to see current state
2. Identify most important uncovered user-facing code
3. Write ONE meaningful test
4. Verify coverage increased
5. Document progress
6. Output `<promise>DONE</promise>` when target reached

## Phase 1: Get Bearings (EVERY iteration)

```bash
cat plans/test-progress.txt 2>/dev/null || echo "No progress file"
git log --oneline -5 2>/dev/null || echo "No git history"
bun run coverage 2>&1 | tail -50
```

Note current coverage percentage.

## Phase 2: Check Completion

If coverage target reached (default 80%, or specified in args):
1. Update plans/test-progress.txt with final status
2. Output: `<promise>DONE</promise>`
3. Stop

## Phase 3: Identify What to Test

1. Run `bun run coverage` to see uncovered files/lines
2. Read the uncovered code
3. Identify the most important **USER-FACING FEATURE** that lacks tests
4. Focus on behavior users care about, not implementation details

**CRITICAL: ONE test per iteration. Not two. ONE.**

Announce:
```
Testing: [file]
Feature: [what user behavior this tests]
Current coverage: X%
```

## Phase 4: Write Test

1. Write ONE meaningful test that validates real user behavior
2. Test should fail first if feature is broken
3. Run the test:
   ```bash
   bun run test
   ```

4. Run coverage again:
   ```bash
   bun run coverage
   ```
   Coverage should increase.

5. Run typecheck:
   ```bash
   bun run typecheck
   ```

## Phase 5: Update State

### If test PASSES and coverage increased:

1. Append to plans/test-progress.txt:
   ```
   test([file]): [describe user behavior tested]
   Coverage: X% -> Y%
   [file]: A% -> B%
   ```

2. Git commit:
   ```bash
   git add -A
   git commit -m "test([file]): [describe user behavior tested]"
   ```

### If test FAILS or coverage didn't increase:

1. **STOP** after 2-3 attempts
2. Revert: `git checkout -- .`
3. Document in plans/test-progress.txt:
   ```
   FAILED: [file]
   Attempted: [what you tried]
   Error: [what went wrong]
   ```
4. Try a different file/feature next iteration

## Phase 6: Loop Control

1. Run coverage again
2. If target reached: `<promise>DONE</promise>`
3. If NOT: loop auto-continues

## What to Test (Priority)

1. **User-facing features** - what users actually do
2. **Error paths** - what happens when things go wrong
3. **Edge cases** - boundary conditions users might hit

## What NOT to Test

- UI wrappers / display logic
- External service calls (mock these)
- Framework boilerplate
- Already-covered code

Use `/* v8 ignore */` comments for legitimately untestable code.

## Arguments

`$ARGUMENTS` format: `[--target=N]`

Default target: 80% coverage

## Rules

- **ONE test per iteration**
- **Test user behavior**, not implementation
- **Coverage must increase** each iteration
- **Commit per test**
- **Document everything** in test-progress.txt
