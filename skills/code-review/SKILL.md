---
name: code-review
description: Systematic code review workflows with bundled utilities for analyzing code quality, detecting patterns, and providing structured feedback. Use this skill when reviewing pull requests or conducting code audits.
---

# Code Review Skill

Systematic code review workflows with utilities for thorough, consistent reviews.

## Overview

This skill provides structured workflows and utilities for conducting high-quality code reviews. It emphasizes:
- Systematic analysis across multiple dimensions
- Automated pattern detection via bundled `review_helpers.py`
- Structured feedback format
- Constructive, actionable suggestions

## Review Process

### 1. Initial Context Gathering

Before reviewing, understand the change:

```shell
# View PR metadata
gh pr view <pr-number> --repo <owner/repo>

# Check file changes
gh pr diff <pr-number> --repo <owner/repo>

# Review CI status
gh pr checks <pr-number> --repo <owner/repo>
```

### 2. Systematic Analysis

Review across these dimensions:

**Correctness**
- Does the code do what it claims?
- Are edge cases handled?
- Are error conditions addressed?

**Clarity**
- Is the code easy to understand?
- Are names descriptive?
- Is complexity justified?

**Testing**
- Are there adequate tests?
- Do tests cover edge cases?
- Are tests clear and maintainable?

**Documentation**
- Is public API documented?
- Are complex algorithms explained?
- Are assumptions stated?

**Performance**
- Are there obvious inefficiencies?
- Are large operations appropriate?
- Is caching used where beneficial?

**Security**
- Are inputs validated?
- Are sensitive data handled properly?
- Are dependencies trustworthy?

### 3. Pattern Detection

Use the bundled `review_helpers.py` to detect common issues:

```python
# Import review helpers (bundled with this skill)
from review_helpers import (
    check_naming_conventions,
    detect_code_smells,
    analyze_complexity,
    find_duplicate_code,
    check_test_coverage
)

# Analyze code
naming_issues = check_naming_conventions("path/to/file.py")
smells = detect_code_smells("path/to/file.py")
complexity = analyze_complexity("path/to/file.py")
duplicates = find_duplicate_code("src/")

# Report findings
for issue in naming_issues + smells:
    print(f"⚠️ {issue}")
```

### 4. Structured Feedback Format

Provide feedback in this format:

```markdown
## Summary
[Brief overview of the PR and general assessment]

## Strengths
- [Positive aspect 1]
- [Positive aspect 2]

## Issues Found

### Critical
- [ ] **[File:Line]**: [Description]
  - Why: [Explanation]
  - Fix: [Suggestion]

### Important
- [ ] **[File:Line]**: [Description]
  - Why: [Explanation]
  - Fix: [Suggestion]

### Minor
- [ ] **[File:Line]**: [Description]
  - Why: [Explanation]
  - Fix: [Suggestion]

## Suggestions
- [Non-blocking improvement 1]
- [Non-blocking improvement 2]

## Questions
- [Clarification needed 1]
- [Clarification needed 2]

## Overall Assessment
[Approve / Request Changes / Comment]
```

## Bundled Utilities — review_helpers.py

**check_naming_conventions(filepath)**
- Validates Python naming conventions (PEP 8)
- Returns list of naming issues

**detect_code_smells(filepath)**
- Identifies common anti-patterns, magic numbers, long functions, deep nesting

**analyze_complexity(filepath)**
- Calculates cyclomatic complexity; flags functions above threshold

**find_duplicate_code(directory)**
- AST-based duplication detection across files

**check_test_coverage(test_dir, source_dir)**
- Test-to-code ratio analysis; identifies untested paths

## Common Review Checklists

### Security-Sensitive Code

```markdown
**Security Checklist**:
- [ ] Input validation present
- [ ] SQL injection prevention
- [ ] XSS prevention (if web)
- [ ] Secrets not hardcoded
- [ ] Sensitive data encrypted
- [ ] Access control enforced
- [ ] Audit logging included
```

### Performance-Critical Code

```markdown
**Performance Checklist**:
- [ ] N+1 queries avoided
- [ ] Appropriate indexes used
- [ ] Caching considered
- [ ] Bulk operations used where possible
- [ ] Memory usage reasonable
- [ ] Algorithmic complexity acceptable
```

### API Changes

```markdown
**API Checklist**:
- [ ] Backward compatibility maintained or migration path provided
- [ ] Documentation updated
- [ ] Examples provided
- [ ] Error handling clear
- [ ] Type hints complete
- [ ] Deprecation warnings added if needed
```

## Shell Utilities

```shell
# Find technical debt markers
rg "TODO|FIXME|XXX|HACK" --type py src/

# Find wildcard imports (bad practice)
grep -r "import \*" src/

# Find largest files
find src/ -name "*.py" -exec wc -l {} + | sort -rn | head -10

# Find pattern with ast-grep
sg --pattern 'except: $$$' --lang python src/
```

## Best Practices

**Be Constructive**: Focus on improvement, not criticism.
- ❌ "This code is terrible."
- ✅ "This function is hard to test due to tight coupling. Consider dependency injection."

**Prioritize Issues**: Distinguish blocking from non-blocking.
- **Blocking**: Security vulnerability in password handling (line 42)
- **Important**: Missing input validation (line 67)
- **Nice-to-have**: Consider extracting helper function (line 123)

**Provide Context**: Explain why something matters.
- ❌ "This variable name is bad."
- ✅ "The name 'x' is unclear. Consider 'customer_count' — better expresses the domain and aids readability."
