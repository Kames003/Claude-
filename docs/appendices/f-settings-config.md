# Appendix F: Settings Configuration

Kompletny prehlad nastaveni Claude Code.

---

## Konfiguracne subory

### Hierarchia

```
~/.claude/                    # Globalne nastavenia
├── settings.json             # Hlavna konfiguracia
├── memory.json               # Ulozene spomienky
├── permissions.json          # Permissions cache
└── sessions/                 # Session data

~/project/                    # Projektove nastavenia
├── CLAUDE.md                 # Projektovy kontext
├── .claude/
│   └── settings.json         # Lokalne nastavenia
└── .claudeignore             # Ignorovane subory
```

### Priorita

1. Command line flags (najvyssia)
2. Environment variables
3. Projektove `.claude/settings.json`
4. Globalne `~/.claude/settings.json`
5. Defaults (najnizsia)

---

## settings.json

### Kompletna struktura

```json
{
  "model": {
    "default": "sonnet",
    "fallback": "haiku"
  },

  "behavior": {
    "autoApprove": false,
    "confirmDestructive": true,
    "showToolCalls": true,
    "verboseOutput": false,
    "debugMode": false
  },

  "context": {
    "maxTokens": 100000,
    "autoCompact": true,
    "compactThreshold": 0.8,
    "includeGitDiff": true
  },

  "files": {
    "maxFileSize": 1048576,
    "excludePatterns": [
      "node_modules/**",
      ".git/**",
      "*.lock"
    ],
    "includeHidden": false
  },

  "permissions": {
    "allowFileWrite": "ask",
    "allowFileDelete": "ask",
    "allowBashCommands": "ask",
    "allowNetworkAccess": "ask",
    "trustedPaths": []
  },

  "hooks": {
    "pre-tool-call": [],
    "post-tool-call": [],
    "pre-submit": [],
    "post-response": [],
    "on-error": []
  },

  "mcp": {
    "servers": {}
  },

  "ui": {
    "theme": "auto",
    "showTimestamps": false,
    "truncateOutput": 1000
  }
}
```

---

## Model Settings

### Predvoleny model

```json
{
  "model": {
    "default": "sonnet",
    "fallback": "haiku"
  }
}
```

### Volby

| Model | Pouzitie |
|-------|----------|
| `haiku` | Rychle, jednoduche ulohy |
| `sonnet` | Vseobecne programovanie (default) |
| `opus` | Komplexne architektonicke ulohy |

---

## Behavior Settings

### Auto-approve

```json
{
  "behavior": {
    "autoApprove": false,
    "confirmDestructive": true
  }
}
```

| Setting | Popis |
|---------|-------|
| `autoApprove` | Automaticky schvaluj bezpecne operacie |
| `confirmDestructive` | Pytaj sa pred destructive operaciami |

### Output

```json
{
  "behavior": {
    "showToolCalls": true,
    "verboseOutput": false,
    "debugMode": false
  }
}
```

| Setting | Popis |
|---------|-------|
| `showToolCalls` | Zobrazuj volania nastrojov |
| `verboseOutput` | Detailny output |
| `debugMode` | Debug informacie |

---

## Context Settings

### Token management

```json
{
  "context": {
    "maxTokens": 100000,
    "autoCompact": true,
    "compactThreshold": 0.8
  }
}
```

| Setting | Popis |
|---------|-------|
| `maxTokens` | Max tokeny v kontexte |
| `autoCompact` | Automaticka kompakcia |
| `compactThreshold` | Kedy kompaktovat (0.0-1.0) |

### Git integrations

```json
{
  "context": {
    "includeGitDiff": true,
    "includeGitStatus": true,
    "includeGitLog": false
  }
}
```

---

## File Settings

### Limity

```json
{
  "files": {
    "maxFileSize": 1048576,
    "maxFilesInContext": 50
  }
}
```

### Exclude patterns

```json
{
  "files": {
    "excludePatterns": [
      "node_modules/**",
      ".git/**",
      "dist/**",
      "build/**",
      "*.lock",
      "*.log",
      ".env*"
    ]
  }
}
```

### .claudeignore

Subor v koreni projektu:

```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
.next/

# Secrets
.env*
*.pem
credentials.json

# Large files
*.zip
*.tar.gz
```

---

## Permission Settings

### Typy permissions

```json
{
  "permissions": {
    "allowFileWrite": "ask",
    "allowFileDelete": "ask",
    "allowBashCommands": "ask",
    "allowNetworkAccess": "ask"
  }
}
```

| Hodnota | Vyznam |
|---------|--------|
| `"always"` | Vzdy povol |
| `"ask"` | Pytaj sa (default) |
| `"never"` | Vzdy zamietni |

### Trusted paths

```json
{
  "permissions": {
    "trustedPaths": [
      "/home/user/projects/my-app/src",
      "/home/user/projects/my-app/tests"
    ]
  }
}
```

### Session permissions

```json
{
  "permissions": {
    "sessionScope": {
      "allowedTools": ["Read", "Glob", "Grep"],
      "blockedTools": ["Write", "Bash"]
    }
  }
}
```

---

## Hooks Settings

### Struktura

```json
{
  "hooks": {
    "pre-tool-call": [
      {
        "tool": "Edit",
        "command": "cp $FILE_PATH $FILE_PATH.bak"
      }
    ],
    "post-tool-call": [
      {
        "tool": "Write",
        "pattern": "*.ts",
        "command": "prettier --write $FILE_PATH"
      }
    ]
  }
}
```

Detaily v [Hooks & Automation](../04-advanced/18-hooks-automation.md).

---

## MCP Settings

### Server konfiguracia

```json
{
  "mcp": {
    "servers": {
      "filesystem": {
        "command": "npx",
        "args": ["@anthropic/mcp-server-filesystem", "/path/to/dir"]
      },
      "postgres": {
        "command": "npx",
        "args": ["@anthropic/mcp-server-postgres"],
        "env": {
          "DATABASE_URL": "postgresql://..."
        }
      }
    }
  }
}
```

Detaily v [MCP Servers](../04-advanced/17-mcp-servers.md).

---

## UI Settings

### Theme

```json
{
  "ui": {
    "theme": "auto"
  }
}
```

| Hodnota | Popis |
|---------|-------|
| `"auto"` | Podla system preferences |
| `"light"` | Svetly tema |
| `"dark"` | Tmavy tema |

### Output formatting

```json
{
  "ui": {
    "showTimestamps": false,
    "truncateOutput": 1000,
    "colorOutput": true
  }
}
```

---

## CLAUDE.md

### Umiestnenie

```
projekt/
├── CLAUDE.md              # Projektovy kontext
├── src/
│   └── CLAUDE.md          # Modul-specificky kontext
└── tests/
    └── CLAUDE.md          # Test-specificky kontext
```

### Struktura

```markdown
# Project Name

## Overview
Kratky popis projektu.

## Tech Stack
- Framework: Next.js 15
- Language: TypeScript
- Database: PostgreSQL

## Architecture
Popis architektury.

## Coding Standards
- Use TypeScript strict mode
- Prefer functional components
- Tests required for all features

## Commands
- `npm run dev` - Development server
- `npm run test` - Run tests
- `npm run build` - Production build

## Important Files
- `src/lib/auth.ts` - Authentication logic
- `src/app/api/` - API routes

## Do Not Modify
- `src/generated/` - Auto-generated files
- `prisma/migrations/` - DB migrations
```

---

## Memory Settings

### memory.json

```json
{
  "memories": [
    {
      "id": "mem_1",
      "content": "User prefers Tailwind for styling",
      "created": "2024-01-15T10:00:00Z",
      "scope": "global"
    },
    {
      "id": "mem_2",
      "content": "This project uses pnpm",
      "created": "2024-01-16T14:30:00Z",
      "scope": "project",
      "project": "/path/to/project"
    }
  ]
}
```

### Scope

| Scope | Popis |
|-------|-------|
| `global` | Plati vsade |
| `project` | Len v konkretnom projekte |
| `session` | Len v aktualnej session |

---

## Priklad: Kompletna konfiguracia

### ~/.claude/settings.json

```json
{
  "model": {
    "default": "sonnet"
  },

  "behavior": {
    "autoApprove": false,
    "showToolCalls": true,
    "verboseOutput": false
  },

  "context": {
    "maxTokens": 100000,
    "autoCompact": true,
    "includeGitDiff": true
  },

  "files": {
    "excludePatterns": [
      "node_modules/**",
      ".git/**",
      "dist/**"
    ]
  },

  "permissions": {
    "allowFileWrite": "ask",
    "allowBashCommands": "ask"
  },

  "hooks": {
    "post-tool-call": [
      {
        "tool": "Write",
        "pattern": "*.{ts,tsx}",
        "command": "prettier --write $FILE_PATH"
      }
    ]
  },

  "ui": {
    "theme": "auto",
    "colorOutput": true
  }
}
```

---

## Reset & Cleanup

### Reset settings

```bash
# Backup a reset
mv ~/.claude/settings.json ~/.claude/settings.json.bak
claude  # Vytvori default settings
```

### Clear cache

```bash
rm -rf ~/.claude/cache
```

### Clear sessions

```bash
rm -rf ~/.claude/sessions
```

### Clear memory

```bash
rm ~/.claude/memory.json
# Alebo
claude /memory clear
```

---

[Spat na README](../README.md)
