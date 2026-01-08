---
description: Archive completed PRD and progress to versioned folder
---

# Archive PRD

Archive the current PRD and progress files to a versioned folder, then reset for the next development cycle.

## Arguments

`$ARGUMENTS` should be a version string, e.g., `v0.1.0`, `v1.0.0`, `2024-Q1`

If no argument provided, read version from `docs/prd.json` and use that.

## Your Task

1. **Validate current PRD is complete**
   ```bash
   cat docs/prd.json
   ```
   - Count features where `passes: false`
   - If any features are incomplete, WARN the user and ask for confirmation before archiving
   - Show: "X of Y features passing. Archive anyway? (some features incomplete)"

2. **Determine version string**
   - Use `$ARGUMENTS` if provided
   - Otherwise use `version` field from `docs/prd.json`
   - Validate it's a reasonable version string

3. **Create archive directory**
   ```bash
   mkdir -p docs/archive/$VERSION
   ```

4. **Copy files to archive**
   ```bash
   cp docs/prd.json docs/archive/$VERSION/
   cp docs/progress.txt docs/archive/$VERSION/
   ```

5. **Add completion summary to archived progress**
   Append to `docs/archive/$VERSION/progress.txt`:
   ```markdown
   ---

   ## Archive Summary

   - Archived: [ISO timestamp]
   - Version: [version]
   - Features: X/Y passing
   - Duration: [if calculable from dates in progress]
   ```

6. **Reset current PRD for next cycle**
   Create new `docs/prd.json`:
   ```json
   {
     "project": "[same project name]",
     "version": "[incremented version or placeholder]",
     "created": "[ISO timestamp]",
     "previous_version": "[archived version]",
     "features": []
   }
   ```

7. **Reset progress file**
   Create new `docs/progress.txt`:
   ```markdown
   # Ralph Progress Log

   Project: [project name]
   Version: [new version]
   Started: [ISO timestamp]
   Previous: See [docs/archive/$VERSION/progress.txt](archive/$VERSION/progress.txt)

   ## Current Status

   - Features defined: 0
   - Next step: Add features to docs/prd.json

   ## Session History

   ```

8. **Git commit the archive**
   ```bash
   git add docs/archive/$VERSION docs/prd.json docs/progress.txt
   git commit -m "chore: archive PRD $VERSION"
   ```

9. **Report to user**
   Show:
   - Archive location: `docs/archive/$VERSION/`
   - Files archived: prd.json, progress.txt
   - Current PRD reset and ready for new features
   - Suggest: "Add new features to docs/prd.json for the next release"
