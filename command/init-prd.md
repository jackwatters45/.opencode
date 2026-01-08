---
description: Initialize PRD-based development structure for Ralph Loop
---

# Initialize PRD Structure

Create the PRD tracking files for incremental feature development.

## Your Task

1. Detect project name from `package.json` (`name` field) or use current directory name

2. Create `docs/` directory if it doesn't exist

3. Create `docs/prd.json` with this structure:
```json
{
  "project": "[detected project name]",
  "version": "0.1.0",
  "created": "ISO timestamp",
  "features": []
}
```

4. Create `docs/progress.txt`:
```
Ralph Progress Log
Project initialized: [ISO timestamp]

Status: 0 features defined
Next: Add features to docs/prd.json

--- Session History ---
```

5. Create `.ralph/init.sh` (executable) for project-specific dev setup:
```bash
#!/usr/bin/env bash
set -euo pipefail
echo "Starting development environment..."
# Customize for your project:
# - npm run dev
# - docker-compose up -d
# - source .venv/bin/activate
echo "Development environment ready."
```

6. **Check for existing files FIRST:**
   ```bash
   ls docs/prd.json docs/progress.txt 2>/dev/null
   ```
   - If `docs/prd.json` exists: **DO NOT overwrite** - tell user PRD already exists
   - If `docs/progress.txt` exists: **DO NOT overwrite** - tell user progress file already exists
   - If `.ralph/init.sh` exists: skip, don't overwrite
   - Only create files that don't exist
   - If all files exist, just report "Ralph PRD already initialized" and stop

7. After creation, show the user:
   - `docs/prd.json` - add your features here (committed to VCS)
   - `docs/progress.txt` - session history (committed to VCS)
   - `.ralph/init.sh` - customize for your dev environment
   - **Ensure `bun run typecheck` and `bun run test` work** (required for CI checks)
   - Start with: `/ralph-prd "implement all features"`

## PRD Feature Structure

Each feature in the `features` array should have:
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

## Example Features

Suggest example features based on project type:

**Web apps:** Authentication, CRUD, API endpoints
**CLI tools:** Argument parsing, core functionality, help output
**Libraries:** Core API, type definitions, documentation
