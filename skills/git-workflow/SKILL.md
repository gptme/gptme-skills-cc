---
name: git-workflow
description: Reliable git workflow for AI agents: conventional commits, worktree-based feature branches, pre-push validation, and CI gate. Use when creating branches, writing commits, or preparing pull requests.
---

# Git Workflow for AI Agents

Reliable git practices tuned for AI-driven development: avoid the common failure modes (accidentally pushing to main, force-pushing over others' work, skipping CI), and produce commit histories that are easy for humans to review and revert.

## Commit Format — Conventional Commits

Every commit follows this format:

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

**Types:**
- `feat`: new feature
- `fix`: bug fix
- `docs`: documentation only
- `refactor`: code change that neither fixes a bug nor adds a feature
- `test`: adding or updating tests
- `chore`: maintenance (deps, config, CI)
- `perf`: performance improvement

**Examples:**
```
feat(auth): add OAuth2 PKCE flow
fix(api): handle null response from /users endpoint
docs(readme): update installation steps for Python 3.12
refactor(parser): extract token validation into separate module
test(auth): add edge cases for expired JWT tokens
chore(deps): upgrade requests to 2.32.0
```

**Rules:**
- Keep the subject line under 72 characters
- Use imperative mood: "add" not "added", "fix" not "fixed"
- Don't end the subject with a period
- Reference issues: `fix(login): handle empty password (#123)`

## Feature Branch Workflow

Never commit directly to `main`/`master` for non-trivial changes. Use feature branches:

```shell
# 1. Start from a clean main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feat/your-feature-name

# 3. Make changes, commit often
git add path/to/changed/file.py
git commit -m "feat(scope): description"

# 4. Push and create PR
git push origin feat/your-feature-name
gh pr create --title "feat: your feature" --body "Description of changes"
```

## Worktree Pattern (for Parallel Work)

When you need to work on multiple features simultaneously without switching branches:

```shell
# Create isolated worktree for a feature
WORKTREE="/tmp/worktrees/project/feature-name"
git worktree add "$WORKTREE" -b feat/feature-name origin/main

# Work in the worktree
cd "$WORKTREE"
# ... make changes, commit ...

# Push from worktree
git push origin feat/feature-name

# Clean up when PR is merged
git worktree remove "$WORKTREE"
git branch -d feat/feature-name
```

**When to use worktrees:**
- You have two features in flight simultaneously
- You need to test a fix while working on a feature
- Parallel CI runs on different branches

## Pre-Push Validation

Always run these before pushing:

```shell
# Quick validation (run before every push)
git diff --check HEAD          # Whitespace errors
python -m py_compile src/*.py  # Syntax errors (Python)

# Thorough validation (run before PR creation)
make lint        # Linting (or: ruff check ., eslint ., etc.)
make typecheck   # Type checking (or: mypy ., tsc --noEmit)
make test        # Test suite (or: pytest, jest, etc.)
```

If you don't have a Makefile, adapt these to your project's tools.

## CI Gate

After pushing, **wait for CI before merging**:

```shell
# Watch CI on your PR
gh pr checks <pr-number> --watch

# If checks fail, read the logs
gh run view --log-failed

# Fix, push, loop until green
git commit path/to/fix.py -m "fix: address CI failure in test_X"
git push
gh pr checks <pr-number> --watch
```

**Never merge a PR with failing CI.** Even if the failure looks unrelated — verify it, don't assume.

## Commit Discipline

**Commit explicitly, not with `git add .`:**

```shell
# ✅ Explicit — immune to staging accidents
git commit src/module.py tests/test_module.py -m "fix: handle edge case in parser"

# ❌ Implicit — picks up unrelated staged files
git add .
git commit -m "fix: handle edge case"
```

**Commit early and often:**
- After each logical unit of work (not after 500 lines of changes)
- Before switching context or trying a different approach
- When you have a working intermediate state (even if not final)

**Atomic commits:**
- Each commit should leave the repo in a working state
- If it's hard to describe in one line, it should probably be split

## Undoing Mistakes

```shell
# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset HEAD~1

# Discard a file's changes since last commit
git checkout -- path/to/file.py

# Undo a pushed commit (creates a new revert commit — safe for shared branches)
git revert HEAD
git push

# DANGER: rewrite history (only on your own unshared branches)
git push --force-with-lease  # Safer than --force; fails if remote changed
```

## PR Best Practices

```markdown
## PR Description Template

### What
[1-3 sentences: what this PR does]

### Why
[Context: why this change is needed, what problem it solves]

### How
[Brief technical approach if non-obvious]

### Testing
- [ ] Tests added/updated
- [ ] CI passes
- [ ] Manual testing: [describe what you tested]

### Related
- Closes #[issue-number]
- Related to #[other-issue]
```

**PR size:**
- Aim for < 400 lines changed per PR
- Split large features into a series of small, reviewable PRs
- One concern per PR — avoid mixing features and refactors

## Branch Naming

```
feat/short-description        # New feature
fix/issue-description         # Bug fix
refactor/component-name       # Refactoring
docs/topic                    # Documentation
chore/task-name               # Maintenance
```

Keep branch names lowercase, use hyphens not underscores, keep them under 50 characters.

## Useful Aliases

```shell
# One-line log with graph
git log --oneline --graph --decorate -20

# Show what changed in last commit
git show --stat HEAD

# List branches with last commit date
git branch -v

# Check what's staged vs unstaged
git status --short

# Find when a line was introduced
git log -S "function_name" --source --all
```
