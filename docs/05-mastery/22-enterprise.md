# Enterprise Deployment

CI/CD, tímová spolupráca a škálovanie Claude Code.

---

## GitHub Actions Integration

### Automated Code Review

```yaml
# .github/workflows/claude-review.yml

name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude --non-interactive "
            Review the changes in this PR.
            Focus on:
            - Code quality
            - Security issues
            - Performance concerns
            - Test coverage

            Provide feedback as GitHub PR comments.
          "

      - name: Post Results
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs')
            const review = fs.readFileSync('review-output.md', 'utf8')

            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: review
            })
```

### Automated Testing

```yaml
# .github/workflows/claude-test.yml

name: Claude Test Generation

on:
  push:
    paths:
      - 'src/**/*.ts'
      - 'src/**/*.tsx'

jobs:
  generate-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate Missing Tests
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude --non-interactive "
            Find source files without corresponding test files.
            Generate tests for uncovered files.
            Use existing test patterns as reference.
          "

      - name: Create PR with Tests
        uses: peter-evans/create-pull-request@v5
        with:
          title: 'test: add missing tests'
          commit-message: 'test: add generated tests'
          branch: claude/add-tests
```

---

## Team Skill Governance

### Centralized Skills Repository

```
organization/
└── claude-skills/
    ├── global/           # Všetky projekty
    │   ├── security/
    │   └── coding-standards/
    ├── frontend/         # Frontend tímy
    │   ├── react/
    │   └── testing/
    └── backend/          # Backend tímy
        ├── api-design/
        └── database/
```

### Skill Approval Workflow

```yaml
# .github/workflows/skill-approval.yml

name: Skill Change Approval

on:
  pull_request:
    paths:
      - '.claude/skills/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Validate Skill Structure
        run: |
          # Check SKILL.md exists
          # Validate frontmatter
          # Check description quality
          ./scripts/validate-skills.sh

      - name: Security Review
        run: |
          # Check allowed-tools aren't too permissive
          # Verify no sensitive data in skills
          ./scripts/security-check-skills.sh

  require-approval:
    needs: validate
    runs-on: ubuntu-latest
    steps:
      - name: Require CODEOWNERS Approval
        uses: actions/github-script@v7
        with:
          script: |
            // Require review from skills-maintainers team
```

### CODEOWNERS

```
# .github/CODEOWNERS

# Skills require maintainer approval
.claude/skills/** @org/skills-maintainers

# Security skills require security team
.claude/skills/security/** @org/security-team
```

---

## Enterprise Hierarchy

```
Enterprise (Org-wide policies)
    ↓
Team/Department Skills
    ↓
Project Skills
    ↓
Personal Preferences
```

### Enterprise Config

```json
// organization/.claude/enterprise.json
{
  "enforcedPolicies": {
    "blockedTools": ["Bash"],  // No shell access
    "requiredSkills": ["security-guidelines"],
    "maxTokensPerDay": 1000000,
    "allowedModels": ["sonnet"]
  },
  "auditLog": {
    "enabled": true,
    "destination": "s3://company-audit-logs/"
  }
}
```

---

## Monitoring & Observability

### Metrics Dashboard

```typescript
// src/lib/metrics.ts

import { metrics } from '@opentelemetry/api'

const meter = metrics.getMeter('claude-code')

const requestCounter = meter.createCounter('claude_requests_total', {
  description: 'Total Claude API requests'
})

const tokenHistogram = meter.createHistogram('claude_tokens_used', {
  description: 'Tokens used per request'
})

const errorCounter = meter.createCounter('claude_errors_total', {
  description: 'Total errors'
})

export function recordRequest(
  model: string,
  inputTokens: number,
  outputTokens: number,
  success: boolean
): void {
  requestCounter.add(1, { model, success: String(success) })
  tokenHistogram.record(inputTokens + outputTokens, { model, type: 'total' })

  if (!success) {
    errorCounter.add(1, { model })
  }
}
```

### Grafana Dashboard

```json
{
  "dashboard": {
    "title": "Claude Code Metrics",
    "panels": [
      {
        "title": "Requests per Minute",
        "targets": [{
          "expr": "rate(claude_requests_total[5m])"
        }]
      },
      {
        "title": "Token Usage",
        "targets": [{
          "expr": "sum(claude_tokens_used) by (model)"
        }]
      },
      {
        "title": "Error Rate",
        "targets": [{
          "expr": "rate(claude_errors_total[5m]) / rate(claude_requests_total[5m])"
        }]
      },
      {
        "title": "Cost Estimate",
        "targets": [{
          "expr": "sum(claude_tokens_used * on(model) group_left token_cost)"
        }]
      }
    ]
  }
}
```

---

## Cost Management

### Budget Allocation

```typescript
// src/lib/budget.ts

interface TeamBudget {
  teamId: string
  monthlyLimit: number
  currentUsage: number
  alertThreshold: number  // Percentage
}

async function checkTeamBudget(teamId: string): Promise<boolean> {
  const budget = await getBudget(teamId)

  const usagePercent = (budget.currentUsage / budget.monthlyLimit) * 100

  if (usagePercent >= 100) {
    await notifyTeam(teamId, 'Budget exhausted')
    return false
  }

  if (usagePercent >= budget.alertThreshold) {
    await notifyTeam(teamId, `Budget at ${usagePercent.toFixed(0)}%`)
  }

  return true
}
```

### Chargeback Reports

```typescript
interface ChargebackReport {
  period: string
  teams: Array<{
    teamId: string
    requests: number
    inputTokens: number
    outputTokens: number
    cost: number
  }>
  totalCost: number
}

async function generateChargebackReport(
  startDate: Date,
  endDate: Date
): Promise<ChargebackReport> {
  const usage = await getUsageByTeam(startDate, endDate)

  return {
    period: `${startDate.toISOString()} - ${endDate.toISOString()}`,
    teams: usage.map(t => ({
      ...t,
      cost: calculateCost(t.inputTokens, t.outputTokens, t.model)
    })),
    totalCost: usage.reduce((sum, t) => sum + t.cost, 0)
  }
}
```

---

## Compliance

### SOC 2 Considerations

```
[ ] Audit logging enabled
[ ] Access controls documented
[ ] Data retention policies defined
[ ] Encryption in transit (HTTPS)
[ ] API key rotation policy
[ ] Incident response plan
```

### GDPR Compliance

```typescript
// Data minimization
const sanitizedPrompt = removePersonalData(prompt)

// Right to deletion
async function deleteUserData(userId: string): Promise<void> {
  await prisma.project.deleteMany({ where: { userId } })
  await prisma.auditLog.deleteMany({ where: { userId } })
  await prisma.user.delete({ where: { id: userId } })
}

// Data export
async function exportUserData(userId: string): Promise<UserExport> {
  const user = await prisma.user.findUnique({
    where: { id: userId },
    include: { projects: true }
  })

  return {
    user: sanitizeForExport(user),
    exportDate: new Date()
  }
}
```

---

## Deployment Checklist

```
Enterprise Deployment Checklist:

Infrastructure:
[ ] CI/CD pipelines configured
[ ] Monitoring dashboards deployed
[ ] Alert rules defined
[ ] Log aggregation setup

Security:
[ ] API keys in secret manager
[ ] Audit logging enabled
[ ] Access controls configured
[ ] Penetration testing completed

Governance:
[ ] Skill approval workflow
[ ] CODEOWNERS configured
[ ] Budget limits set
[ ] Usage policies documented

Compliance:
[ ] Data handling documented
[ ] Retention policies defined
[ ] Privacy review completed
[ ] Security review completed
```

---

## Cvičenia

### Cvičenie 1: CI Pipeline
```
Nastavte kompletný CI/CD pipeline s Claude Code review.
```

### Cvičenie 2: Team Dashboard
```
Vytvorte team dashboard s usage metrics a cost tracking.
```

### Cvičenie 3: Skill Governance
```
Implementujte skill approval workflow pre váš tím.
```

---

## Gratulujeme!

Dokončili ste Ultimate Claude Code Course. Teraz máte znalosti od základov po enterprise deployment.

[Späť na README](../README.md)
