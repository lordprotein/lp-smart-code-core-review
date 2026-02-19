---
name: lp-smart-code-review
description: "Expert code review with a senior engineer lens. Reviews git changes or targeted code (files, folders, features). Detects SOLID violations, security risks, and proposes actionable improvements."
---

# Code Review Expert

## Overview

Perform a structured code review with focus on SOLID, architecture, removal candidates, and security risks. Default to review-only output unless the user asks to implement changes.

**Two modes of operation:**

- **Default mode** (no argument): Reviews current git changes via `git diff`.
- **Targeted review mode** (with argument): Reviews specific code — a file path, folder, feature name, function, or keyword. The argument is interpreted flexibly: it can be a path (`src/auth/`), an entity name (`PaymentService`), or a description (`логика корзины`).

## Severity Levels

| Level | Name | Description | Action |
|-------|------|-------------|--------|
| **P0** | Critical | Security vulnerability, data loss risk, correctness bug | Must block merge |
| **P1** | High | Logic error, significant SOLID violation, performance regression | Should fix before merge |
| **P2** | Medium | Code smell, maintainability concern, minor SOLID violation | Fix in this PR or create follow-up |
| **P3** | Low | Style, naming, minor suggestion | Optional improvement |

## Workflow

### 0) Mode detection

If the user provides an argument (text after the skill command), switch to **targeted review mode**:

1. **Determine target type**:
   - If argument looks like a file/directory path → read those files directly
   - If argument is a name/keyword (e.g. "auth", "PaymentService", "роутинг") → use `rg`, `grep`, `find` to locate all related files, functions, classes, and modules
   - If argument is a description (e.g. "логика оплаты") → search for related code by keywords

2. **Scope the review**:
   - List all discovered files and ask the user to confirm the scope
   - If too many files found (>10), suggest narrowing down or review in batches

3. **Proceed to step 2** (skip git diff in step 1, review the discovered code instead)

If no argument is provided → proceed to step 1 (default git diff review).

### 1) Preflight context

> **Note**: In targeted review mode (step 0), this step is skipped — the review scope is already established.

- Use `git status -sb`, `git diff --stat`, and `git diff` to scope changes.
- If needed, use `rg` or `grep` to find related modules, usages, and contracts.
- Identify entry points, ownership boundaries, and critical paths (auth, payments, data writes, network).

**Edge cases:**
- **No changes**: If `git diff` is empty, inform user and ask if they want to review staged changes or a specific commit range.
- **Large diff (>500 lines)**: Summarize by file first, then review in batches by module/feature area.
- **Mixed concerns**: Group findings by logical feature, not just file order.

### 2) SOLID + architecture smells

- Load `references/solid-checklist.md` for specific prompts.
- Look for:
  - **SRP**: Overloaded modules with unrelated responsibilities.
  - **OCP**: Frequent edits to add behavior instead of extension points.
  - **LSP**: Subclasses that break expectations or require type checks.
  - **ISP**: Wide interfaces with unused methods.
  - **DIP**: High-level logic tied to low-level implementations.
- When you propose a refactor, explain *why* it improves cohesion/coupling and outline a minimal, safe split.
- If refactor is non-trivial, propose an incremental plan instead of a large rewrite.

### 3) Removal candidates + iteration plan

- Load `references/removal-plan.md` for template.
- Identify code that is unused, redundant, or feature-flagged off.
- Distinguish **safe delete now** vs **defer with plan**.
- Provide a follow-up plan with concrete steps and checkpoints (tests/metrics).

### 4) Security and reliability scan

- Load `references/security-checklist.md` for coverage.
- Check for:
  - XSS, injection (SQL/NoSQL/command), SSRF, path traversal
  - AuthZ/AuthN gaps, missing tenancy checks
  - Secret leakage or API keys in logs/env/files
  - Rate limits, unbounded loops, CPU/memory hotspots
  - Unsafe deserialization, weak crypto, insecure defaults
  - **Race conditions**: concurrent access, check-then-act, TOCTOU, missing locks
- Call out both **exploitability** and **impact**.

### 5) Code quality scan

- Load `references/code-quality-checklist.md` for coverage.
- Check for:
  - **Error handling**: swallowed exceptions, overly broad catch, missing error handling, async errors
  - **Performance**: N+1 queries, CPU-intensive ops in hot paths, missing cache, unbounded memory
  - **Boundary conditions**: null/undefined handling, empty collections, numeric boundaries, off-by-one
- Flag issues that may cause silent failures or production incidents.

### 6) Output format

Structure your review as follows:

```markdown
## Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]

---

## Findings

### P0 - Critical
(none or list)

### P1 - High
1. **[file:line]** Brief title
  - Description of issue
  - Suggested fix

### P2 - Medium
2. (continue numbering across sections)
  - ...

### P3 - Low
...

---

## Removal/Iteration Plan
(if applicable)

## Additional Suggestions
(optional improvements, not blocking)
```

**Inline comments**: Use this format for file-specific findings:
```
::code-comment{file="path/to/file.ts" line="42" severity="P1"}
Description of the issue and suggested fix.
::
```

**Clean review**: If no issues found, explicitly state:
- What was checked
- Any areas not covered (e.g., "Did not verify database migrations")
- Residual risks or recommended follow-up tests

### 7) Next steps confirmation

After presenting findings, ask user how to proceed:

```markdown
---

## Next Steps

I found X issues (P0: _, P1: _, P2: _, P3: _).

**How would you like to proceed?**

1. **Fix all** - I'll implement all suggested fixes
2. **Fix P0/P1 only** - Address critical and high priority issues
3. **Fix specific items** - Tell me which issues to fix
4. **No changes** - Review complete, no implementation needed

Please choose an option or provide specific instructions.
```

**Important**: Do NOT implement any changes until user explicitly confirms. This is a review-first workflow.

## Resources

### references/

| File | Purpose |
|------|---------|
| `solid-checklist.md` | SOLID smell prompts and refactor heuristics |
| `security-checklist.md` | Web/app security and runtime risk checklist |
| `code-quality-checklist.md` | Error handling, performance, boundary conditions |
| `removal-plan.md` | Template for deletion candidates and follow-up plan |
