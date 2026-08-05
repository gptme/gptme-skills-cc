---
name: progressive-disclosure
description: Template and guide for restructuring large documentation files into token-efficient directory structures. Reduces context bloat by 40-60% while maintaining full accessibility. Use when documentation files exceed 500 lines or 5k tokens.
---

# Progressive Disclosure for Documentation

A pattern for restructuring large documentation files into slim indexes with on-demand subdirectories, reducing always-included context by 40-60%.

## Problem

Large documentation files (500+ lines, 5k+ tokens) that are always included in context:
- Waste context budget on rarely-needed content
- Slow down response times
- Crowd out more relevant information
- Create cognitive overload

## Solution

Split monolithic docs into:
1. **Slim index** (~20% of original) — Always included, provides overview + navigation
2. **Detail directories** — On-demand loading when specific topics needed

## Structure

```txt
Before:
  TOOLS.md (~11k tokens always included)
  ├── Section 1 (rarely needed)
  ├── Section 2 (rarely needed)
  ├── ... (15+ sections)
  └── Section N (rarely needed)

After:
  tools/
  ├── README.md (~4k tokens always included)  ← Slim index
  ├── topic1/README.md   ← Detailed docs (~1k tokens each)
  ├── topic2/README.md   ← Loaded on-demand
  └── .../README.md      ← etc.
```

## When to Use

**Good candidates:**
- Files > 500 lines or 5k tokens
- Files always included in context (via config or imports)
- Files with distinct sections rarely needed together
- Documentation with both overview and detailed reference content

**Keep as single file when:**
- File is < 200 lines
- Content is frequently needed together
- File is only occasionally included

## Implementation Guide

### Step 1: Audit Current File

```shell
wc -l LARGE_FILE.md                    # Line count
wc -c LARGE_FILE.md                    # Byte count
grep "^## " LARGE_FILE.md             # List top-level sections
```

### Step 2: Plan the Structure

Identify which sections:
- Are frequently needed together → keep in index
- Are rarely needed → move to detail files
- Can be summarized → create summary + link

### Step 3: Create the Index

```markdown
# Tool Name

Brief description (2-3 sentences). Always keep this here.

## Quick Reference

| Command | Purpose |
|---------|---------|
| `cmd1` | Short description |
| `cmd2` | Short description |

## Topics

- [Topic A](topic-a/README.md) — When to read this
- [Topic B](topic-b/README.md) — When to read this
- [Topic C](topic-c/README.md) — When to read this
```

### Step 4: Move Detail Content

Each detail file should:
- Open with 1-2 sentence context ("This covers X for when you need Y")
- Include complete content for that topic
- Link back to index

```markdown
# Topic A — Detailed Guide

*For when you need deep-dive on X. See [index](../README.md) for overview.*

[Full content here...]
```

### Step 5: Update References

Update any files that reference the old path:
```shell
grep -r "OLD_FILE.md" --include="*.md" .
# Update each reference to point to new index
```

## Example Transformation

### Before (1 file, 800 lines)

```markdown
# API Reference

## Authentication
[200 lines of auth docs]

## Endpoints
[300 lines of endpoint docs]

## Error Codes
[150 lines of error docs]

## Webhooks
[150 lines of webhook docs]
```

### After (5 files, ~200 lines total in index)

```txt
api/
├── README.md (80 lines)          ← Overview + quick reference
├── auth/README.md (220 lines)    ← Full auth docs
├── endpoints/README.md (320 lines) ← Full endpoint docs
├── errors/README.md (160 lines)  ← Full error docs
└── webhooks/README.md (160 lines) ← Full webhook docs
```

**Index (`api/README.md`):**
```markdown
# API Reference

REST API for interacting with the service.

## Authentication

All requests require Bearer token. [→ Full auth docs](auth/README.md)

## Common Endpoints

| Method | Path | Purpose |
|--------|------|---------|
| GET | /users | List users |
| POST | /users | Create user |

[→ Complete endpoint reference](endpoints/README.md)

## Error Handling

Errors follow RFC 7807 format. [→ Error codes reference](errors/README.md)

## Webhooks

[→ Webhook setup and events](webhooks/README.md)
```

## Metrics

After applying this pattern, measure improvement:
```shell
# Count tokens (approximate: chars/4)
wc -c api/README.md | awk '{print int($1/4), "tokens in index"}'

# vs original
wc -c API.md | awk '{print int($1/4), "tokens before"}'
```

Typical results: 40-60% reduction in always-loaded tokens.

## Tips

1. **Don't over-split**: 3-6 subdirectories is usually right; more = navigation overhead
2. **Keep quick reference in index**: The 20% most-used commands stay in the main file
3. **Descriptive links**: "→ Full auth docs" beats "see here" — reader knows what they get
4. **Cross-link generously**: Detail files link back; index links forward
