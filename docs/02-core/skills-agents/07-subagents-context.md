# Subagents a Kontext

Delegovanie úloh na subagentov a správa kontextu.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Znalosť Built-in Agents ([Built-in Agents](08-built-in-agents.md))
- Pochopenie Tool Use ([Tool Use](06-tool-use.md))

---

## Čo sú Subagents?

Subagents sú samostatné inštancie Claude spustené hlavným agentom pre špecifické úlohy.

```
┌─────────────────────────────────────────────────────────────┐
│  HLAVNÝ AGENT (Parent)                                      │
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  Subagent 1 │  │  Subagent 2 │  │  Subagent 3 │         │
│  │  (Explore)  │  │  (Build)    │  │  (Test)     │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│                                                             │
│  Paralelné vykonávanie, izolácia kontextu                  │
└─────────────────────────────────────────────────────────────┘
```

---

## Task Tool

### Základné použitie

```typescript
{
  tool: "Task",
  args: {
    subagent_type: "Explore",      // Typ agenta
    prompt: "Nájdi všetky...",     // Úloha
    description: "Finding files",  // Krátky popis
    model: "haiku"                 // Voliteľný model
  }
}
```

### Dostupné typy subagentov

| Typ | Účel | Tools |
|-----|------|-------|
| `Explore` | Prieskum codebase | Read, Glob, Grep |
| `Plan` | Plánovanie | Read, Glob, Grep |
| `Bash` | Shell príkazy | Bash |
| `general-purpose` | Všeobecné úlohy | Všetky |

---

## Kontext Subagentov

### Čo subagent dedí

```
┌─────────────────────────────────────────────────────────────┐
│  DEDÍ:                                                      │
│  ✓ Prompt od parent agenta                                 │
│  ✓ Working directory                                        │
│  ✓ Environment variables                                    │
│                                                             │
│  NEDEDÍ:                                                    │
│  ✗ Conversation history                                     │
│  ✗ Skills (automaticky)                                     │
│  ✗ Predchádzajúce tool výsledky                            │
└─────────────────────────────────────────────────────────────┘
```

### Explicitné posielanie kontextu

```typescript
{
  tool: "Task",
  args: {
    subagent_type: "general-purpose",
    prompt: `
      Kontext: Pracujeme na Next.js 15 projekte s Prisma ORM.

      Úloha: Vytvor databázovú migráciu pre User model.

      Relevantné súbory:
      - prisma/schema.prisma
      - src/lib/prisma.ts
    `,
    description: "Create migration"
  }
}
```

### Skills v subagentoch

```yaml
# Explicitné špecifikovanie skills
---
name: specialized-agent
skills:
  - deployment
  - testing
inherit_skills: false
---
```

---

## Supported Formatted Fields

Pri práci s hooks a subagentmi sú dostupné tieto formátované polia:

### Hook Output Fields

| Field | Popis | Použitie |
|-------|-------|----------|
| `hookSpecificOutput` | Výstup z hook scriptu | Parsovanie výsledkov |
| `additionalContext` | Extra kontext | Rozšírenie promptu |
| `toolResults` | Výsledky nástrojov | Analýza operácií |
| `conversationHistory` | História konverzácie | Kontinuita |

### Príklad s hooks

```json
// ~/.claude/settings.json
{
  "hooks": {
    "afterToolUse": {
      "command": "node analyze-output.js",
      "outputFields": ["hookSpecificOutput", "additionalContext"]
    }
  }
}
```

### Formátovanie v promptoch

```typescript
// Štruktúrovaný prompt pre subagenta
const prompt = `
## Task
${taskDescription}

## Context
${additionalContext}

## Files
${relevantFiles.map(f => `- ${f}`).join('\n')}

## Expected Output
${outputFormat}
`;
```

---

## Paralelné Subagents

### Spustenie viacerých agentov

```typescript
// Claude automaticky paralelizuje nezávislé úlohy
[
  {
    tool: "Task",
    args: {
      subagent_type: "Explore",
      prompt: "Nájdi všetky API routes",
      description: "Find API routes"
    }
  },
  {
    tool: "Task",
    args: {
      subagent_type: "Explore",
      prompt: "Nájdi všetky React komponenty",
      description: "Find components"
    }
  }
]
// Oba bežia súčasne
```

### Komunikácia medzi subagentmi

```
┌─────────────────────────────────────────────────────────────┐
│  Subagents NEKOMUNIKUJÚ priamo medzi sebou                 │
│                                                             │
│  Workflow:                                                  │
│  1. Parent spustí subagent A                               │
│  2. Subagent A vráti výsledok                              │
│  3. Parent spracuje výsledok                               │
│  4. Parent spustí subagent B s výsledkom A                 │
└─────────────────────────────────────────────────────────────┘
```

---

## Background Agents

### Spustenie na pozadí

```typescript
{
  tool: "Task",
  args: {
    subagent_type: "Bash",
    prompt: "npm run build",
    description: "Building project",
    run_in_background: true
  }
}
```

### Kontrola stavu

```bash
# Zoznam bežiacich úloh
/tasks

# Výstup konkrétnej úlohy
TaskOutput { task_id: "task_abc123", block: false }
```

---

## Praktické Vzory

### Pattern 1: Explore → Act

```
1. Explore subagent analyzuje codebase
2. Parent spracuje výsledky
3. Parent implementuje zmeny
```

### Pattern 2: Parallel Research

```
1. Viacero Explore subagentov súčasne
2. Každý skúma inú časť codebase
3. Parent kombinuje výsledky
```

### Pattern 3: Build + Test

```
1. Bash subagent: npm run build
2. Bash subagent: npm run test (po úspešnom builde)
3. Parent reportuje výsledky
```

---

## UIGen Príklad

### Analýza s Explore subagentom

```
User: Analyzuj závislosti VirtualFileSystem.

Claude: Spúšťam Explore subagenta...

[Explore Subagent]
VirtualFileSystem závislosti:

Používa:
- FileSystemContext (wrapper)
- Prisma (perzistencia)
- serialize/deserialize helpers

Používaný v:
- api/chat/route.ts (tool operácie)
- FileTree component (zobrazenie)
- CodeEditor (čítanie/editovanie)

[Parent Agent]
Na základe analýzy, VirtualFileSystem je centrálny
pre celú aplikáciu. Zmeny si vyžadujú opatrnosť.
```

---

## Cvičenia

### Cvičenie 1: Parallel Explore
```
Spusti dva Explore subagentov paralelne -
jeden pre komponenty, druhý pre API routes.
```

### Cvičenie 2: Context Passing
```
Vytvor workflow kde prvý subagent
analyzuje a druhý implementuje na
základe analýzy.
```

---

## Ďalej

[Allowed Tools](08-allowed-tools.md) - Bezpečnostná konfigurácia nástrojov
