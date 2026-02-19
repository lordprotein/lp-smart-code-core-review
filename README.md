# LP Smart Code Review

A comprehensive code review skill for AI agents. Performs structured reviews with a senior engineer lens, covering architecture, security, performance, and code quality.

## Installation

```bash
npx skills add lordprotein/lp-smart-code-core-review
```

Or via Agent Skills:

```bash
npx agent-skills-cli install @lordprotein/lp-smart-code-core-review
```

## Features

- **SOLID Principles** - Detect SRP, OCP, LSP, ISP, DIP violations
- **Security Scan** - XSS, injection, SSRF, race conditions, auth gaps, secrets leakage
- **Performance** - N+1 queries, CPU hotspots, missing cache, memory issues
- **Error Handling** - Swallowed exceptions, async errors, missing boundaries
- **Boundary Conditions** - Null handling, empty collections, off-by-one, numeric limits
- **Removal Planning** - Identify dead code with safe deletion plans

## Usage

After installation, simply run:

```
/lp-smart-code-review
```

The skill will automatically review your current git changes.

### Targeted review

You can also pass an argument to review specific code instead of git changes:

```
/lp-smart-code-review src/auth/               # review all files in the auth folder
/lp-smart-code-review src/services/payment.ts  # review a specific file
/lp-smart-code-review auth                     # find and review everything related to auth
/lp-smart-code-review PaymentService           # find and review PaymentService code
/lp-smart-code-review логика корзины           # search and review cart logic by keywords
```

The argument is interpreted flexibly — it can be a file path, folder, class/function name, or a description of the feature to review.

## Workflow

0. **Mode Detection** - Targeted review (argument) or git diff (default)
1. **Preflight** - Scope changes via `git diff` (skipped in targeted mode)
2. **SOLID + Architecture** - Check design principles
3. **Removal Candidates** - Find dead/unused code
4. **Security Scan** - Vulnerability detection
5. **Code Quality** - Error handling, performance, boundaries
6. **Output** - Findings by severity (P0-P3)
7. **Confirmation** - Ask user before implementing fixes

## Severity Levels

| Level | Name | Action |
|-------|------|--------|
| P0 | Critical | Must block merge |
| P1 | High | Should fix before merge |
| P2 | Medium | Fix or create follow-up |
| P3 | Low | Optional improvement |

## Structure

```
lp-smart-code-core-review/
├── SKILL.md                 # Main skill definition
├── agents/
│   └── agent.yaml           # Agent interface config
└── references/
    ├── solid-checklist.md   # SOLID smell prompts
    ├── security-checklist.md    # Security & reliability
    ├── code-quality-checklist.md # Error, perf, boundaries
    └── removal-plan.md      # Deletion planning template
```

## References

Each checklist provides detailed prompts and anti-patterns:

- **solid-checklist.md** - SOLID violations + common code smells
- **security-checklist.md** - OWASP risks, race conditions, crypto, supply chain
- **code-quality-checklist.md** - Error handling, caching, N+1, null safety
- **removal-plan.md** - Safe vs deferred deletion with rollback plans

## License

MIT
