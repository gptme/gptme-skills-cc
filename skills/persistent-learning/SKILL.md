---
name: persistent-learning
description: Capture insights, patterns, and lessons into durable files that persist across Claude sessions. Use when you discover something worth remembering — a failure mode, a better approach, a non-obvious fact — before it evaporates at session end.
---

# Persistent Learning

Claude sessions are stateless — everything you learn vanishes when the conversation ends.
This skill gives you a systematic way to fight that: write insights to files before applying them,
so future sessions inherit the knowledge.

## The Core Rule

**Persist first, then apply.**

When you discover something valuable — a pattern that worked, a failure mode, a non-obvious fact — write it to a file immediately. Don't apply it first and "write the lesson later." Later doesn't happen; the session ends.

## When to Use

Invoke this skill when you observe any of these signals:

- You're about to apply an insight but haven't written it down yet
- You just spent 15+ minutes debugging something that has a non-obvious cause
- A task failed the same way it failed in a previous session (you can tell from the history)
- You found a better approach than what the existing documentation says
- Someone corrected your behavior — and that correction should apply next time

## What to Capture

### High-value targets

1. **Failure modes with specific triggers**: "When X, Y fails with error Z because W"
2. **Non-obvious workarounds**: "The correct way to do X is Y, not Z (which looks obvious but breaks because…)"
3. **Validated patterns**: approaches you verified work across at least 2 cases
4. **Decision rationale**: why you chose A over B, so future sessions don't re-litigate it
5. **Environment facts**: versions, configs, constraints that aren't in the code

### Low-value targets (skip these)

- Things already in official documentation
- Obvious facts any capable agent would know
- Single-instance observations that might not generalize
- Temporary states (a bug that will be fixed, a service outage)

## The File Format

Create a Markdown file with:

```markdown
# [Short descriptive title]

## Rule
One-sentence statement of the behavior to adopt.

## Context
When does this apply? What triggers it?

## Detection
Observable signals — what should prompt recall of this lesson?

## Pattern
The correct approach. Be specific. Include commands, code, or examples.

## Why
The non-obvious reason this matters. If it's obvious, you don't need a lesson.

## Anti-patterns
What NOT to do, with brief explanation of why it fails.
```

**File naming**: use kebab-case, specific enough to find later:
- ✅ `git-push-rejects-when-worktree-not-on-branch.md`
- ❌ `git-issue.md`

## Where to Store Lessons

In a Claude Code project, store lessons in a `lessons/` directory at the repo root, organized by category:

```
lessons/
├── tools/         ← tool-specific patterns (git, gh, docker...)
├── workflow/      ← process patterns (debugging, review, deploy...)
├── patterns/      ← general programming patterns
└── README.md      ← index of all lessons
```

If you're building an agent with CLAUDE.md, reference the lessons directory there so it's auto-loaded.

## Workflow

```
1. Notice insight (failure, success, correction, discovery)
   ↓
2. STOP — write the lesson file BEFORE continuing
   ↓
3. Commit the lesson file ("docs(lessons): add lesson on X")
   ↓
4. Apply the lesson to current work
   ↓
5. Reference the lesson in your notes/journal
```

**The trap**: It feels faster to apply first, then write the lesson. But the lesson
captured after the fix describes the solution — not the signal that should trigger
recall. Capture while the failure is live in your context.

## Common Rationalizations (resist these)

| Rationalization | Reality |
|---|---|
| "I'll remember this next time" | Sessions are stateless. "I'll remember" = "I'll lose it." |
| "Writing a lesson takes time I don't have" | 5 min now = 0 min in every future session. The investment compounds. |
| "This is too specific to be worth capturing" | The most specific lessons are the most valuable — they fire precisely when needed. |
| "I'll write the lesson after I finish the fix" | You'll write a weaker lesson that describes the solution, not the signal to detect it. |

## Verify the Loop Closed

Before ending a session where you learned something, check:

```shell
git diff --name-only | grep "lessons/"
```

If you made meaningful progress but have no lesson file in the diff, ask yourself:
**What did I learn that I'd want the next session to know?**

If the answer is "nothing" — fine. If you're actually rationalizing — write the lesson.

## Example

**Session**: Spent 20 minutes debugging why `gh pr create` returned 422.

**Wrong approach**: Fix the issue, move on.

**Right approach**:

```markdown
# gh pr create 422 when base branch has no commits yet

## Rule
Always push at least one commit to the base branch before running `gh pr create`.

## Context
When creating a PR in a fresh repository or from a branch that hasn't diverged yet.

## Detection
- `gh pr create` returns HTTP 422
- Error message mentions "No commits between X and Y"

## Pattern
```shell
# Create an initial commit if the repo is empty
echo "# Project" > README.md
git add README.md
git commit -m "chore: initial commit"
git push origin master

# Now create the branch and PR
git checkout -b my-feature
# ... make changes ...
gh pr create --base master
```

## Why
GitHub requires at least one commit difference between the base and head branch to create a PR.
```

Then: `git commit lessons/tools/gh-pr-create-422-empty-repo.md -m "docs(lessons): gh pr create 422 when base has no commits"`

## Integration with CLAUDE.md

If you want lessons auto-loaded in future sessions, add to `CLAUDE.md`:

```markdown
## Lessons

Learned patterns are in `lessons/`. Read relevant ones before starting work in that domain.
Key lessons: [list 3-5 most critical ones]
```

Or use Claude Code's CLAUDE.md `@include` syntax to auto-load the lessons directory.
