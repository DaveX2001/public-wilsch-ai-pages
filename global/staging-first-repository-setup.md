---
publish: true
[[docker-deployment]]
description: "Staging-first repository setup: default branch, branch protection, PR workflow"
---

# Staging-First Repository Setup

## Overview

Configure repositories for staging-first workflow where feature branches merge into `staging` for UAT, then `staging` merges into `main` for production.

## Setup Checklist

### 1. Create Staging Branch (if not exists)

```bash
git checkout main
git checkout -b staging
git push -u origin staging
```

### 2. Set Staging as Default Branch

```bash
gh api repos/{owner}/{repo} --method PATCH -f default_branch=staging
```

**Why:** New PRs automatically target staging. Clone/checkout defaults to staging.

### 3. Branch Protection (Rulesets)

```bash
gh api repos/{owner}/{repo}/rulesets --method POST --input - <<'EOF'
{
  "name": "protect-main-staging",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "include": ["refs/heads/main", "refs/heads/staging"],
      "exclude": []
    }
  },
  "rules": [
    {"type": "deletion"},
    {"type": "non_fast_forward"}
  ]
}
EOF
```

**Rules explained:**
- `deletion` - Prevents accidental branch deletion
- `non_fast_forward` - Prevents force push (history rewrite)

### 4. Auto-Delete Merged Branches

```bash
gh api repos/{owner}/{repo} --method PATCH -f delete_branch_on_merge=true
```

### 5. Update Existing PRs to Target Staging

```bash
# List PRs targeting main
gh pr list --base main --state open

# Update each PR
gh pr edit {PR_NUMBER} --base staging
```

**Safe operation:** Only changes merge target metadata, no history changes.

## Workflow

```
feature-branch → staging (UAT) → main (production)
       │              │              │
   Development    Testing      Live users
```

## Verification

```bash
# Check default branch
gh api repos/{owner}/{repo} --jq '.default_branch'

# Check rulesets
gh api repos/{owner}/{repo}/rulesets --jq '.[].name'

# Check auto-delete setting
gh api repos/{owner}/{repo} --jq '.delete_branch_on_merge'
```

## Related

- [[staging-uat-workflow]] - Testing pyramid and UAT process
- [[feature-branching-environment-isolation]] - Environment architecture

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

