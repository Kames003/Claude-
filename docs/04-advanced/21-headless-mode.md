# Headless Mode

Non-interactive spúšťanie Claude Code pre automatizáciu.

---

## Čo je Headless Mode?

Headless mode umožňuje spúšťať Claude Code bez interaktívneho terminálu:
- CI/CD pipelines
- Scheduled tasks
- Automated scripts
- Batch processing

---

## Aktivácia

### Flag

```bash
claude --non-interactive "Your prompt here"
```

### Skratka

```bash
claude -n "Your prompt here"
```

### Environment Variable

```bash
CLAUDE_NON_INTERACTIVE=1 claude "Your prompt here"
```

---

## Základné použitie

### Jednoduchý prompt

```bash
claude -n "Explain the main function in src/index.ts"
```

### Prompt zo súboru

```bash
claude -n < prompt.txt
```

### Prompt s kontextom

```bash
claude -n "Review this code:" < src/auth.ts
```

### Output do súboru

```bash
claude -n "Generate a README" > README.md
```

---

## CLI Options

| Option | Popis |
|--------|-------|
| `--non-interactive` / `-n` | Headless mode |
| `--model <model>` | Vyber model (haiku/sonnet/opus) |
| `--max-tokens <n>` | Limit output tokenov |
| `--output <file>` | Zapíš output do súboru |
| `--format <fmt>` | Output formát (text/json/markdown) |
| `--cwd <dir>` | Pracovný priečinok |
| `--timeout <ms>` | Timeout v milisekundách |

### Príklady

```bash
# S modelom
claude -n --model opus "Complex architecture question"

# S output formátom
claude -n --format json "List all functions" > functions.json

# S timeoutom
claude -n --timeout 60000 "Long running analysis"

# V špecifickom priečinku
claude -n --cwd /path/to/project "Review the code"
```

---

## Automatizácia

### Code Review Script

```bash
#!/bin/bash
# review.sh

FILE=$1

if [ -z "$FILE" ]; then
  echo "Usage: review.sh <file>"
  exit 1
fi

claude -n --format markdown "
Review this code for:
- Bugs
- Security issues
- Performance problems
- Code style

$(cat $FILE)
" > "reviews/$(basename $FILE).md"

echo "Review saved to reviews/$(basename $FILE).md"
```

### Batch Processing

```bash
#!/bin/bash
# batch-review.sh

mkdir -p reviews

for file in src/**/*.ts; do
  echo "Reviewing $file..."
  claude -n "Review for bugs: $(cat $file)" > "reviews/$(basename $file).md"
done

echo "All reviews complete"
```

### Test Generation

```bash
#!/bin/bash
# generate-tests.sh

for file in src/lib/*.ts; do
  if [ ! -f "${file%.ts}.test.ts" ]; then
    echo "Generating tests for $file..."
    claude -n "Generate Vitest tests for: $(cat $file)" > "${file%.ts}.test.ts"
  fi
done
```

---

## CI/CD Integration

### GitHub Actions

```yaml
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

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Get changed files
        id: changed
        run: |
          echo "files=$(git diff --name-only origin/main | tr '\n' ' ')" >> $GITHUB_OUTPUT

      - name: Review changes
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          for file in ${{ steps.changed.outputs.files }}; do
            if [[ $file == *.ts || $file == *.tsx ]]; then
              echo "Reviewing $file..."
              claude -n --format markdown \
                "Review this PR change for issues:" \
                < "$file" >> review.md
            fi
          done

      - name: Post review
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs')
            const review = fs.readFileSync('review.md', 'utf8')
            if (review.trim()) {
              await github.rest.issues.createComment({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: context.issue.number,
                body: '## Claude Code Review\n\n' + review
              })
            }
```

### GitLab CI

```yaml
# .gitlab-ci.yml

claude-review:
  stage: review
  image: node:20
  before_script:
    - npm install -g @anthropic-ai/claude-code
  script:
    - |
      for file in $(git diff --name-only $CI_MERGE_REQUEST_TARGET_BRANCH_NAME); do
        claude -n "Review: $(cat $file)" >> review.md
      done
  artifacts:
    paths:
      - review.md
  only:
    - merge_requests
```

### Jenkins

```groovy
// Jenkinsfile

pipeline {
  agent any

  environment {
    ANTHROPIC_API_KEY = credentials('anthropic-api-key')
  }

  stages {
    stage('Claude Review') {
      steps {
        sh 'npm install -g @anthropic-ai/claude-code'
        sh '''
          git diff --name-only HEAD~1 | while read file; do
            claude -n "Review: $(cat $file)" >> review.md
          done
        '''
        archiveArtifacts artifacts: 'review.md'
      }
    }
  }
}
```

---

## Scheduled Tasks

### Cron: Daily Summary

```bash
# crontab -e
0 9 * * * /path/to/daily-summary.sh
```

```bash
#!/bin/bash
# daily-summary.sh

cd /path/to/project

SUMMARY=$(claude -n "
Summarize yesterday's git commits:
$(git log --since='1 day ago' --oneline)
")

echo "$SUMMARY" | mail -s "Daily Code Summary" team@company.com
```

### Cron: Security Scan

```bash
# Weekly security scan
0 0 * * 0 /path/to/security-scan.sh
```

```bash
#!/bin/bash
# security-scan.sh

cd /path/to/project

claude -n --model opus "
Perform security audit on this codebase.
Focus on OWASP Top 10 vulnerabilities.
" > /var/reports/security-$(date +%Y%m%d).md
```

---

## Error Handling

### Exit Codes

| Code | Význam |
|------|--------|
| 0 | Úspech |
| 1 | Všeobecná chyba |
| 2 | Invalid arguments |
| 3 | API error |
| 4 | Timeout |

### Error Handling v scripte

```bash
#!/bin/bash

set -e  # Exit on error

claude -n "Generate tests" > tests.ts || {
  echo "Claude failed with exit code $?"
  exit 1
}

if [ ! -s tests.ts ]; then
  echo "Empty output"
  exit 1
fi

echo "Tests generated successfully"
```

---

## Rate Limiting

### Batch s delay

```bash
#!/bin/bash

for file in src/**/*.ts; do
  claude -n "Review: $(cat $file)" > "reviews/$(basename $file).md"
  sleep 2  # Avoid rate limits
done
```

### Parallel s limit

```bash
#!/bin/bash

# Max 3 parallel jobs
find src -name "*.ts" | xargs -P 3 -I {} bash -c '
  claude -n "Review: $(cat {})" > "reviews/$(basename {}).md"
'
```

---

## Cvičenia

### Cvičenie 1: CI Pipeline
```
Vytvorte GitHub Action pre automatický code review.
```

### Cvičenie 2: Batch Script
```
Vytvorte script pre batch generovanie dokumentácie.
```

### Cvičenie 3: Scheduled Task
```
Nastavte cron job pre týždennú security analýzu.
```

---

---

## Dalej

**Pokracuj na Tier 5: Mastery**

[Multi-Agent Orchestration](../05-mastery/18-multi-agent.md) - Koordinacia viacerych agentov
