---
name: code-review-process
description: Two-tier code review process for Swift/iOS projects. Defines convention compliance checks, logic review procedures, and output formatting standards.
keywords: code review, Swift, iOS, conventions, quality, process
---

# Code Review Process Skill

## Purpose
A structured code review process that ensures both convention compliance and code quality for Swift/iOS projects.

**Use this skill when**:
- Reviewing code for convention compliance
- Performing logic and quality review
- Structuring review feedback

## Two-Tier Review Process

### Tier 1: Convention Compliance
Quick checks for style guide adherence.

**Focus Areas**:
- Naming conventions (Boolean, functions, closures)
- Line length and formatting
- Modern API usage (SwiftUI, Foundation, Concurrency)
- Data model and view naming patterns

**Reference**: `@conventions/convention.md`

### Tier 2: Logic & Quality Review
Deep dive into code quality.

**Focus Areas**:
- Logic correctness and edge cases
- Memory management (retain cycles, leaks)
- Error handling patterns
- Thread safety and concurrency
- Performance considerations
- Security vulnerabilities

---

## Critical Issues (Must Fix)

These issues should be flagged as **blocking** and require immediate attention:

| Issue | Risk |
|-------|------|
| Force unwrapping in production code | App crash |
| Retain cycles in closures | Memory leak |
| UI updates off main thread | Undefined behavior |
| Hardcoded API keys or secrets | Security breach |
| Empty catch blocks | Silent failures |
| Race conditions in shared state | Data corruption |
| Blocking main thread | UI freeze |
| @Observable class missing @MainActor | Concurrency violation |
| SwiftData @Attribute(.unique) with CloudKit | Sync failure |
| SwiftData non-optional relationships with CloudKit | Sync crash |

---

## Warnings (Should Fix)

These issues should be addressed but won't block merge:

| Issue | Category |
|-------|----------|
| Convention violations | Naming, formatting |
| Deprecated/legacy APIs | Modernization |
| Suboptimal patterns | Best practices |
| Missing documentation | Maintainability |
| Code duplication | DRY principle |

---

## Review Output Format

Structure feedback as follows:

```markdown
## 🔴 Critical Issues (X found)
1. [File:Line] Description
   - Current code: `...`
   - Recommended fix: `...`
   - Risk: [Crash/Security/Data Loss]

## ⚠️ Warnings (Y found)
1. [File:Line] Description
   - Current code: `...`
   - Recommended fix: `...`
   - Category: [Convention/Performance/Maintainability]

## ✅ Positive Feedback
- Well-structured error handling in NetworkManager
- Good use of async/await throughout
- Proper memory management with [weak self]

## 📊 Summary
- Critical Issues: X (must fix before merge)
- Warnings: Y (recommended to fix)
- Code Quality: [Excellent/Good/Needs Improvement]
- Convention Compliance: X%
```

---

## Review Checklist

### Before Starting
- [ ] Code has been written and is ready for review
- [ ] Access to `conventions/convention.md` for style guide
- [ ] Understanding of project architecture

### Tier 1 Checks
- [ ] Boolean naming conventions
- [ ] Function naming patterns
- [ ] Closure declarations have named parameters
- [ ] Line length within limits
- [ ] Modern APIs used (SwiftUI, async/await)
- [ ] Data model naming (no redundant suffixes)
- [ ] View naming patterns

### Tier 2 Checks
- [ ] No retain cycles in closures
- [ ] No force unwrapping in production
- [ ] Errors properly handled (no empty catch)
- [ ] UI updates on main thread
- [ ] Async/await used correctly
- [ ] No hardcoded secrets
- [ ] Performance considerations addressed

---

## References

- `@conventions/convention.md` - Project style guide
- `@skills/memory-safety-checklist.md` - Memory safety rules
- `@skills/cloudkit-validation.md` - CloudKit constraints
