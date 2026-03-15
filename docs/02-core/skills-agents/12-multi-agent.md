# Multi-Agent Orchestration

Koordinácia viacerých AI agentov pre komplexné úlohy.

---

## Prečo Multi-Agent?

Komplexné úlohy často vyžadujú:
- Rôzne perspektívy (architekt vs developer vs reviewer)
- Paralelné spracovanie
- Špecializované znalosti
- Check and balance systém

---

## Agent Types v Claude Code

| Agent | Účel | Skills Access |
|-------|------|---------------|
| **Main** | Primárny agent | Plný prístup |
| **Task** | Subagent pre úlohy | Explicitne špecifikované |
| **Explorer** | Read-only prieskum | Žiadny |
| **Plan** | Plánovanie | Žiadny |
| **Verify** | Verifikácia | Žiadny |

---

## Task Agent Pattern

### Základné použitie

```
Spusti subagenta ktorý preskúma všetky API endpointy.
```

Claude vytvorí Task agent:

```typescript
// Interné - Claude Code
{
  tool: "Task",
  args: {
    description: "Explore API endpoints",
    prompt: "Find and list all API endpoints in this project...",
    subagent_type: "Explore"
  }
}
```

### S explicitnými skills

```yaml
# V SKILL.md pre hlavného agenta
subagents:
  reviewer:
    skills:
      - code-review
      - testing-conventions
  deployer:
    skills:
      - deployment
      - ci-cd
```

---

## Multi-Agent Workflow

### Code Review Pipeline

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Developer  │ ──► │  Reviewer   │ ──► │  Approver   │
│   Agent     │     │   Agent     │     │   Agent     │
└─────────────┘     └─────────────┘     └─────────────┘
       │                  │                   │
       ▼                  ▼                   ▼
   Write Code      Find Issues         Final Check
```

### Implementácia

```
Vytvor code review pipeline s tromi agentmi:
1. Developer - implementuje feature
2. Reviewer - kontroluje kvalitu
3. Approver - finálne schválenie

Použij Task tool pre orchestráciu.
```

---

## Debate Pattern

Dva agenti debatujú o riešení:

```
┌─────────────┐         ┌─────────────┐
│   Agent A   │ ◄─────► │   Agent B   │
│  (Advocate) │         │  (Critic)   │
└─────────────┘         └─────────────┘
       │                       │
       └───────────┬───────────┘
                   ▼
            Best Solution
```

### Príklad

```
Navrhni dva prístupy k implementácii caching:
1. Agent A obhajuje Redis
2. Agent B obhajuje in-memory cache

Potom vyhodnoť oba prístupy a vyber lepší.
```

---

## Parallel Execution

```
Spusti paralelne:
1. Agent pre unit testy
2. Agent pre integration testy
3. Agent pre e2e testy

Počkaj na všetky výsledky a vytvor súhrn.
```

Interné:

```typescript
// Claude Code spustí paralelne
await Promise.all([
  runTask({ type: 'unit-tests', ... }),
  runTask({ type: 'integration-tests', ... }),
  runTask({ type: 'e2e-tests', ... })
])
```

---

## Agent Communication

### Shared Context

```typescript
// Agenti zdieľajú kontext cez:
// 1. Virtual File System
// 2. Conversation history
// 3. Explicit data passing

interface AgentResult {
  success: boolean
  data: any
  artifacts: string[]  // Paths to created files
  recommendations: string[]
}
```

### Handoff Pattern

```
Agent A vytvorí súbor /tmp/analysis.md
Agent B prečíta /tmp/analysis.md a pokračuje
```

---

## Error Handling

```typescript
// Robustná orchestrácia

async function runPipeline(tasks: Task[]): Promise<PipelineResult> {
  const results: TaskResult[] = []

  for (const task of tasks) {
    try {
      const result = await runTask(task)
      results.push(result)

      // Check for blocking errors
      if (result.severity === 'critical') {
        return {
          success: false,
          completedTasks: results,
          failedAt: task.name,
          error: result.error
        }
      }

    } catch (error) {
      // Retry logic
      if (task.retries > 0) {
        task.retries--
        tasks.unshift(task)  // Retry
        continue
      }

      return {
        success: false,
        completedTasks: results,
        failedAt: task.name,
        error
      }
    }
  }

  return { success: true, completedTasks: results }
}
```

---

## UIGen Multi-Agent Scenario

```
Implementuj generovanie komponentu s multi-agent pipeline:

1. PLANNER agent:
   - Analyzuje požiadavku
   - Navrhne štruktúru komponentov
   - Vytvorí plán

2. CODER agent:
   - Implementuje komponenty podľa plánu
   - Používa str_replace_editor tool

3. REVIEWER agent:
   - Kontroluje implementáciu
   - Navrhuje vylepšenia

4. TESTER agent:
   - Píše testy
   - Spúšťa testy

5. FINALIZER agent:
   - Aplikuje review feedback
   - Finalizuje kód
```

---

## Best Practices

| Practice | Popis |
|----------|-------|
| **Single Responsibility** | Každý agent má jednu úlohu |
| **Explicit Handoffs** | Jasné odovzdanie medzi agentmi |
| **Fail Fast** | Zastavte pri kritických chybách |
| **Logging** | Logujte všetky agent akcie |
| **Timeouts** | Nastavte limity pre agentov |
| **Retries** | Implementujte retry logiku |

---

## Cvičenia

### Cvičenie 1: Review Pipeline
```
Implementuj 3-agent code review pipeline.
```

### Cvičenie 2: Parallel Testing
```
Vytvor paralelnú test execution s agregáciou výsledkov.
```

### Cvičenie 3: Self-Improving
```
Implementuj agenta ktorý iteratívne vylepšuje kód na základe feedbacku.
```

---

## Ďalej

[Debugging Agents](13-debugging.md) - Diagnostika a riešenie problémov
