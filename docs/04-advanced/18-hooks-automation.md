# Hooks & Automation

Event-driven automatizácia v Claude Code.

---

## Čo sú Hooks?

Hooks sú užívateľom definované príkazy ktoré sa spúšťajú pri špecifických udalostiach v Claude Code.

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Event     │ ──► │    Hook     │ ──► │   Action    │
│  (trigger)  │     │  (command)  │     │  (result)   │
└─────────────┘     └─────────────┘     └─────────────┘
```

---

## Konfigurácia Hooks

### Umiestnenie

```
~/.claude/settings.json
```

### Štruktúra

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "command": "echo 'Editing file: $FILE_PATH'"
      }
    ],
    "post-tool-call": [
      {
        "tool": "Write",
        "command": "npm run lint --fix $FILE_PATH"
      }
    ],
    "pre-submit": [
      {
        "command": "npm run validate"
      }
    ]
  }
}
```

---

## Dostupné Hook Events

| Event | Kedy sa spustí | Use Cases |
|-------|----------------|-----------|
| `pre-tool-call` | Pred vykonaním tool | Validácia, logging |
| `post-tool-call` | Po vykonaní tool | Formátovanie, lint |
| `pre-submit` | Pred odoslaním promptu | Validácia vstupu |
| `post-response` | Po prijatí odpovede | Logging, notifikácie |
| `on-error` | Pri chybe | Error handling |

---

## Pre-tool-call Hooks

Spúšťajú sa **pred** vykonaním nástroja.

### Príklad: Backup pred editáciou

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "command": "cp $FILE_PATH $FILE_PATH.bak"
      }
    ]
  }
}
```

### Príklad: Validácia paths

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Write",
        "command": "test ! -f $FILE_PATH || (echo 'File exists!' && exit 1)"
      }
    ]
  }
}
```

### Blokovanie operácie

Ak hook vráti non-zero exit code, operácia sa zablokuje:

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "command": "echo $FILE_PATH | grep -v 'node_modules' || exit 1",
        "message": "Cannot edit files in node_modules"
      }
    ]
  }
}
```

---

## Post-tool-call Hooks

Spúšťajú sa **po** vykonaní nástroja.

### Príklad: Auto-format

```json
{
  "hooks": {
    "post-tool-call": [
      {
        "tool": "Write",
        "command": "prettier --write $FILE_PATH"
      },
      {
        "tool": "Edit",
        "command": "prettier --write $FILE_PATH"
      }
    ]
  }
}
```

### Príklad: Auto-lint

```json
{
  "hooks": {
    "post-tool-call": [
      {
        "tool": "Write",
        "command": "eslint --fix $FILE_PATH 2>/dev/null || true"
      }
    ]
  }
}
```

### Príklad: Git stage

```json
{
  "hooks": {
    "post-tool-call": [
      {
        "tool": "Write",
        "command": "git add $FILE_PATH"
      },
      {
        "tool": "Edit",
        "command": "git add $FILE_PATH"
      }
    ]
  }
}
```

---

## Pre-submit Hooks

Spúšťajú sa pred odoslaním používateľského promptu.

### Príklad: Validácia

```json
{
  "hooks": {
    "pre-submit": [
      {
        "command": "test -f package.json || (echo 'Not in project directory' && exit 1)"
      }
    ]
  }
}
```

### Príklad: Context enrichment

```json
{
  "hooks": {
    "pre-submit": [
      {
        "command": "git status --short > /tmp/git-status.txt"
      }
    ]
  }
}
```

---

## Post-response Hooks

Spúšťajú sa po prijatí odpovede od Claude.

### Príklad: Logging

```json
{
  "hooks": {
    "post-response": [
      {
        "command": "echo \"$(date): Response received\" >> ~/.claude/logs/responses.log"
      }
    ]
  }
}
```

### Príklad: Notifikácia

```json
{
  "hooks": {
    "post-response": [
      {
        "command": "osascript -e 'display notification \"Claude finished\" with title \"Claude Code\"'"
      }
    ]
  }
}
```

---

## On-error Hooks

Spúšťajú sa pri chybe.

### Príklad: Error logging

```json
{
  "hooks": {
    "on-error": [
      {
        "command": "echo \"$(date): Error - $ERROR_MESSAGE\" >> ~/.claude/logs/errors.log"
      }
    ]
  }
}
```

### Príklad: Recovery

```json
{
  "hooks": {
    "on-error": [
      {
        "command": "if [ -f $FILE_PATH.bak ]; then cp $FILE_PATH.bak $FILE_PATH; fi"
      }
    ]
  }
}
```

---

## Environment Variables

V hooks máte prístup k:

| Variable | Popis |
|----------|-------|
| `$FILE_PATH` | Cesta k súboru (pre file tools) |
| `$TOOL_NAME` | Názov nástroja |
| `$TOOL_ARGS` | Argumenty nástroja (JSON) |
| `$ERROR_MESSAGE` | Chybová správa (pre on-error) |
| `$PROJECT_DIR` | Koreňový priečinok projektu |
| `$CLAUDE_SESSION` | ID aktuálnej session |

---

## Podmienené Hooks

### Podľa file extension

```json
{
  "hooks": {
    "post-tool-call": [
      {
        "tool": "Write",
        "pattern": "*.tsx",
        "command": "prettier --write $FILE_PATH"
      },
      {
        "tool": "Write",
        "pattern": "*.py",
        "command": "black $FILE_PATH"
      }
    ]
  }
}
```

### Podľa priečinka

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "pattern": "src/generated/*",
        "command": "exit 1",
        "message": "Cannot edit generated files"
      }
    ]
  }
}
```

---

## Príklad: Complete Setup

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "command": "cp $FILE_PATH $FILE_PATH.bak 2>/dev/null || true"
      },
      {
        "tool": "Write",
        "pattern": "*.env*",
        "command": "exit 1",
        "message": "Cannot create/modify .env files"
      }
    ],
    "post-tool-call": [
      {
        "tool": "Write",
        "pattern": "*.{ts,tsx,js,jsx}",
        "command": "prettier --write $FILE_PATH && eslint --fix $FILE_PATH 2>/dev/null || true"
      },
      {
        "tool": "Edit",
        "pattern": "*.{ts,tsx,js,jsx}",
        "command": "prettier --write $FILE_PATH && eslint --fix $FILE_PATH 2>/dev/null || true"
      },
      {
        "tool": "Write",
        "command": "git add $FILE_PATH"
      }
    ],
    "on-error": [
      {
        "command": "if [ -f $FILE_PATH.bak ]; then mv $FILE_PATH.bak $FILE_PATH; fi"
      }
    ]
  }
}
```

---

## Debugging Hooks

### Verbose mode

```bash
CLAUDE_HOOK_DEBUG=1 claude
```

### Test hook manuálne

```bash
FILE_PATH="src/test.ts" bash -c 'prettier --write $FILE_PATH'
```

---

## Cvičenia

### Cvičenie 1: Auto-format Setup
```
Nakonfiguruj hooks pre automatické formátovanie
všetkých TypeScript súborov po editácii.
```

### Cvičenie 2: Protected Files
```
Vytvor hook ktorý zabráni editácii súborov
v priečinku "generated/".
```

### Cvičenie 3: Audit Trail
```
Implementuj hooks pre kompletný audit log
všetkých file operácií.
```

---

## Ďalej

[IDE Integrations](19-ide-integrations.md)
