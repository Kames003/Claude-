# Skill Templates

Hotové skills na okamžité použitie v projektoch.

---

## Predpoklady

Pred použitím šablón by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](01-struktura-skillov.md))
- Znalosť priority ([Priorita](02-priorita.md))

---

## Code Review Skill

```yaml
---
name: code-review
description: Use when reviewing code, checking PR, finding bugs,
code quality analysis, or when user mentions "review", "check", "audit"
allowed-tools: [Read, Glob, Grep]
model: sonnet
---

# Code Review Skill

## Review Checklist
- [ ] Logic errors
- [ ] Security issues
- [ ] Performance concerns
- [ ] Code style
- [ ] Test coverage
- [ ] Documentation

## Focus Areas
1. Error handling - are all errors caught?
2. Input validation - is user input validated?
3. Edge cases - are they handled?
4. Performance - any N+1 queries or loops?
5. Security - any injection risks?

## Output Format
For each issue found:
- File and line number
- Severity (critical/high/medium/low)
- Description
- Suggested fix
```

---

## Testing Skill

```yaml
---
name: testing
description: Use when writing tests, running tests, checking coverage,
TDD workflow, or mentions of "test", "vitest", "jest", "coverage"
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep]
model: sonnet
---

# Testing Skill

## TDD Workflow
1. Write failing test first
2. Implement minimum code
3. Run test to verify
4. Refactor if needed

## Test Structure
- describe() for grouping
- it() for individual tests
- beforeEach() for setup
- Mock external dependencies

## Commands
- npm run test - all tests
- npm run test -- path/file.test.ts - single file
- npm run test -- --coverage - with coverage

## Conventions
- Tests in __tests__ directories
- Name: [filename].test.ts
- One assertion per test (preferred)
- Test behavior, not implementation
```

---

## Deployment Skill

```yaml
---
name: deployment
description: Use when deploying, releasing, CI/CD pipeline,
pushing to production/staging, or mentions "deploy", "release", "ship"
allowed-tools: [Read, Bash, Glob, Grep]
model: sonnet
---

# Deployment Skill

## Pre-deployment Checklist
- [ ] All tests passing
- [ ] No TypeScript errors
- [ ] Build succeeds
- [ ] Environment variables set
- [ ] Database migrations ready

## Commands
- npm run build - Production build
- npm run lint - Check linting
- npm run test -- --run - Run tests once

## Environments
- Development: localhost:3000
- Staging: staging.example.com
- Production: example.com

## Rollback
If issues occur:
1. Revert to previous commit
2. Deploy previous version
3. Investigate logs
```

---

## Documentation Skill

```yaml
---
name: documentation
description: Use when writing docs, README, JSDoc comments,
API documentation, or mentions "document", "readme", "jsdoc"
allowed-tools: [Read, Write, Edit, Glob, Grep]
model: sonnet
---

# Documentation Skill

## Types of Documentation
- README.md - Project overview
- API docs - Endpoint documentation
- JSDoc - Function documentation
- CHANGELOG - Version history

## JSDoc Format
/**
 * Brief description.
 *
 * @param paramName - Description
 * @returns Description of return value
 * @throws Error description
 * @example
 * functionName('example')
 */

## README Structure
1. Title and description
2. Installation
3. Usage
4. API reference
5. Contributing
6. License
```

---

## Security Audit Skill

```yaml
---
name: security-audit
description: Use for security review, vulnerability scanning,
checking for security issues, or mentions "security", "vulnerability", "audit"
allowed-tools: [Read, Glob, Grep]
model: opus
---

# Security Audit Skill

## OWASP Top 10 Checks
1. Injection
2. Broken Authentication
3. Sensitive Data Exposure
4. XML External Entities
5. Broken Access Control
6. Security Misconfiguration
7. XSS
8. Insecure Deserialization
9. Vulnerable Components
10. Insufficient Logging

## Patterns to Find
- SQL queries with string concatenation
- eval() or new Function()
- innerHTML with user input
- Hardcoded secrets
- Missing input validation
- Unvalidated redirects

## Output Format
For each vulnerability:
- Location (file:line)
- Severity (Critical/High/Medium/Low)
- Description
- Remediation steps
- References (CWE, CVE if applicable)
```

---

## Refactoring Skill

```yaml
---
name: refactoring
description: Use for code refactoring, improving code quality,
reducing complexity, or mentions "refactor", "clean up", "simplify"
allowed-tools: [Read, Write, Edit, Glob, Grep]
model: sonnet
---

# Refactoring Skill

## Refactoring Principles
- Small, incremental changes
- Tests must pass after each change
- No behavior changes (unless fixing bugs)
- Improve readability and maintainability

## Common Refactorings
- Extract function/method
- Rename for clarity
- Remove duplication
- Simplify conditionals
- Split large files

## Code Smells to Address
- Long functions (>50 lines)
- Deep nesting (>3 levels)
- Duplicated code
- God objects
- Magic numbers
- Complex conditionals

## Process
1. Ensure tests exist
2. Make small change
3. Run tests
4. Commit
5. Repeat
```

---

## Performance Skill

```yaml
---
name: performance
description: Use for performance optimization, profiling,
improving speed, or mentions "slow", "optimize", "performance", "speed"
allowed-tools: [Read, Edit, Bash, Glob, Grep]
model: opus
---

# Performance Skill

## Analysis Areas
- Database queries (N+1, missing indexes)
- Memory usage
- Bundle size
- Render performance
- Network requests

## Optimization Techniques
- Memoization (useMemo, useCallback)
- Code splitting
- Lazy loading
- Caching
- Query optimization

## Profiling Commands
- npm run build -- --analyze - Bundle analysis
- React DevTools Profiler
- Chrome Performance tab

## Database Optimization
- Add indexes for frequent queries
- Use select() to limit fields
- Implement pagination
- Cache expensive queries
```

---

## Ako používať šablóny

### 1. Skopírujte do projektu

```bash
mkdir -p .claude/skills/code-review
# Skopírujte obsah do .claude/skills/code-review/SKILL.md
```

### 2. Upravte podľa potreby

- Zmeňte triggery v `description`
- Pridajte/odoberte `allowed-tools`
- Upravte obsah pre váš projekt

### 3. Testujte aktiváciu

```
Skúste prompt: "Review this code"
Skill by sa mal aktivovať
```

---

## Rozšírená knižnica šablón

Pre ďalšie skill šablóny a pokročilé príklady pozrite:

**[Appendix B: Skill Templates](../../appendices/b-skill-templates.md)** - Kompletná knižnica skill šablón pre rôzne use cases

---

## Ďalej

[Best Practices](06-best-practices.md) - Overené vzory a zlaté pravidlá
