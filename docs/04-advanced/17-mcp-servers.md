# MCP Servers

Model Context Protocol pre rozšírenie Claude Code.

---

## Čo je MCP?

Model Context Protocol (MCP) je štandard pre:
- Pripojenie externých dátových zdrojov ku Claude
- Rozšírenie schopností cez pluginy
- Zdieľanie kontextu medzi nástrojmi

---

## Architektúra

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Claude Code │ ──► │ MCP Server  │ ──► │  External   │
│   (Client)  │ ◄── │  (Bridge)   │ ◄── │   Service   │
└─────────────┘     └─────────────┘     └─────────────┘
```

---

## Konfigurácia

MCP servery sa konfigurujú v `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@anthropic/mcp-server-filesystem", "/path/to/allowed/dir"]
    },
    "git": {
      "command": "npx",
      "args": ["@anthropic/mcp-server-git"]
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
```

---

## Dostupné MCP Servery

| Server | Účel | Príklad použitia |
|--------|------|------------------|
| **filesystem** | Prístup k súborom | Čítanie/zápis mimo projektu |
| **git** | Git operácie | História, branching |
| **postgres** | PostgreSQL | DB queries |
| **sqlite** | SQLite | Lokálne databázy |
| **brave-search** | Web search | Vyhľadávanie informácií |
| **fetch** | HTTP requests | API volania |

---

## Filesystem Server

```bash
# Inštalácia
npm install -g @anthropic/mcp-server-filesystem

# Konfigurácia
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "@anthropic/mcp-server-filesystem",
        "/Users/me/documents",
        "/Users/me/projects"
      ]
    }
  }
}
```

**Dostupné operácie:**
- `read_file` - čítanie súboru
- `write_file` - zápis súboru
- `list_directory` - výpis priečinka
- `create_directory` - vytvorenie priečinka
- `move_file` - presun súboru
- `search_files` - vyhľadávanie

---

## Git Server

```json
{
  "mcpServers": {
    "git": {
      "command": "npx",
      "args": ["@anthropic/mcp-server-git"]
    }
  }
}
```

**Dostupné operácie:**
- `git_status` - stav repozitára
- `git_log` - história commitov
- `git_diff` - zmeny
- `git_branch` - správa vetiev
- `git_commit` - commit zmien

---

## Database Server

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["@anthropic/mcp-server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/db"
      }
    }
  }
}
```

**Dostupné operácie:**
- `query` - SELECT dotazy
- `describe_table` - schéma tabuľky
- `list_tables` - zoznam tabuliek

---

## Vytvorenie vlastného MCP Server

### Základná štruktúra

```typescript
// my-mcp-server/index.ts

import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'

const server = new Server({
  name: 'my-mcp-server',
  version: '1.0.0'
}, {
  capabilities: {
    tools: {}
  }
})

// Define tools
server.setRequestHandler('tools/list', async () => {
  return {
    tools: [
      {
        name: 'my_tool',
        description: 'Does something useful',
        inputSchema: {
          type: 'object',
          properties: {
            input: { type: 'string' }
          },
          required: ['input']
        }
      }
    ]
  }
})

// Handle tool calls
server.setRequestHandler('tools/call', async (request) => {
  const { name, arguments: args } = request.params

  if (name === 'my_tool') {
    const result = doSomething(args.input)
    return { content: [{ type: 'text', text: result }] }
  }

  throw new Error(`Unknown tool: ${name}`)
})

// Start server
const transport = new StdioServerTransport()
server.connect(transport)
```

### Package.json

```json
{
  "name": "my-mcp-server",
  "version": "1.0.0",
  "type": "module",
  "bin": {
    "my-mcp-server": "./dist/index.js"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^0.5.0"
  }
}
```

---

## UIGen s MCP

Príklad integrácie pre perzistentné ukladanie:

```json
{
  "mcpServers": {
    "uigen-storage": {
      "command": "node",
      "args": ["./mcp-servers/storage/index.js"],
      "env": {
        "STORAGE_PATH": "/Users/me/.uigen/projects"
      }
    }
  }
}
```

---

## Security Considerations

| Riziko | Mitigácia |
|--------|-----------|
| Prístup k citlivým súborom | Obmedzte cesty v konfigurácii |
| SQL injection | Použite prepared statements |
| API key exposure | Použite env variables |
| Neautorizované operácie | Implementujte access control |

---

## Debugging

```bash
# Spustenie s debug logmi
DEBUG=mcp:* npx @anthropic/mcp-server-filesystem /path

# Test connectivity
claude --mcp-debug
```

---

## Cvičenia

### Cvičenie 1: Custom Template Server
```
Vytvor MCP server ktorý poskytuje projektové šablóny.
```

### Cvičenie 2: API Integration
```
Vytvor MCP server pre integráciu s REST API.
```

### Cvičenie 3: Monitoring
```
Pridaj logging a metrics do MCP servera.
```

---

---

## Dalej

[Hooks & Automation](18-hooks-automation.md) - Event-driven automatizacia
