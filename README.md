# LP Smart Code Review

A comprehensive code review skill for AI agents. Performs structured reviews with a senior engineer lens, covering architecture, security, performance, and code quality.

## Installation

```bash
npx skills add lordprotein/smart-code-review
```

Or via Agent Skills:

```bash
npx agent-skills-cli install @lordprotein/smart-code-review
```

## Features

- **SOLID Principles** - Detect SRP, OCP, LSP, ISP, DIP violations
- **Security Scan** - XSS, injection, SSRF, race conditions, auth gaps, secrets leakage
- **Performance** - Big O complexity analysis, memory leak detection, N+1 queries, CPU hotspots, caching issues
- **Error Handling** - Swallowed exceptions, async errors, missing boundaries
- **Boundary Conditions** - Null handling, empty collections, off-by-one, numeric limits
- **Removal Planning** - Identify dead code with safe deletion plans

## Usage

After installation, simply run:

```
/smart-code-review
```

The skill will automatically review your current git changes.

### Targeted review

You can also pass an argument to review specific code instead of git changes:

```
/smart-code-review src/auth/               # review all files in the auth folder
/smart-code-review src/services/payment.ts  # review a specific file
/smart-code-review auth                     # find and review everything related to auth
/smart-code-review PaymentService           # find and review PaymentService code
/smart-code-review логика корзины           # search and review cart logic by keywords
```

The argument is interpreted flexibly — it can be a file path, folder, class/function name, or a description of the feature to review.

## Workflow

0. **Mode Detection** - Targeted review (argument) or git diff (default)
1. **Preflight** - Scope changes via `git diff` (skipped in targeted mode)
2. **SOLID + Architecture** - Check design principles
3. **Removal Candidates** - Find dead/unused code
4. **Security Scan** - Vulnerability detection
5. **Code Quality** - Error handling, boundaries, test coverage gaps, breaking changes
6. **Performance** - Complexity, memory leaks, CPU, I/O, caching
7. **Output** - Findings by category (Security, Architecture, Performance, Quality) with severity badges
8. **Confirmation** - Ask user before implementing fixes

## Severity Levels

| Badge | Level | Action |
|-------|-------|--------|
| 🔴 | Critical | Must block merge |
| 🟠 | High | Should fix before merge |
| 🟡 | Medium | Fix or create follow-up |
| 🟢 | Low | Optional improvement |

## Structure

```
smart-code-review/
├── SKILL.md                 # Main skill definition
├── agents/
│   └── agent.yaml           # Agent interface config
└── references/
    ├── solid-checklist.md         # SOLID smell prompts
    ├── solid-react-checklist.md   # React-specific SOLID patterns
    ├── security-checklist.md      # Security & reliability
    ├── code-quality-checklist.md  # Error handling, boundaries
    ├── performance-checklist.md   # Big O, memory leaks, CPU, I/O, caching
    └── finding-format.md          # Per-finding template and formatting rules
```

## References

Each checklist provides detailed prompts and anti-patterns:

- **solid-checklist.md** - SOLID violations + common code smells
- **solid-react-checklist.md** - React-specific SOLID patterns: god-hooks, wide interfaces, anti-patterns
- **security-checklist.md** - OWASP risks, race conditions, crypto, supply chain
- **code-quality-checklist.md** - Error handling, null safety, boundary conditions
- **performance-checklist.md** - Big O complexity, memory leaks, CPU hot paths, database & I/O, caching, profiling mindset
- **finding-format.md** - Per-finding template, formatting rules

## License

MIT
