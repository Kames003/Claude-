# Debugging Agentic Failures

Diagnostika a riešenie komplexných AI problémov.

---

## Typy zlyhania

| Typ | Príčina | Symptóm |
|-----|---------|---------|
| **Tool Failure** | Nesprávne parametre | Error v tool execution |
| **Context Overflow** | Príliš veľa dát | Truncated responses |
| **Hallucination** | Chýbajúci kontext | Neexistujúci kód |
| **Loop** | Nejasné inštrukcie | Opakované akcie |
| **Skill Conflict** | Podobné descriptions | Zlý skill aktivovaný |

---

## Tool Debugging

### Symptóm: Tool vracia error

```
Error: File not found: /src/compnents/Button.tsx
```

### Diagnostika

1. **Skontrolujte path** - preklep v ceste
2. **Skontrolujte permissions** - tool má prístup?
3. **Skontrolujte parametre** - správny formát?

```typescript
// Debug logging pre tools
export function strReplaceTool(fs: VirtualFileSystem) {
  return tool({
    execute: async (params) => {
      console.log('[TOOL] str_replace_editor called:', {
        command: params.command,
        path: params.path,
        hasContent: !!params.content
      })

      try {
        const result = await executeCommand(params)
        console.log('[TOOL] Success:', result)
        return result
      } catch (error) {
        console.error('[TOOL] Error:', error)
        return { success: false, error: error.message }
      }
    }
  })
}
```

---

## Context Overflow

### Symptóm: Neúplné odpovede

```
Claude: "Tu je implementácia:

function calculate(
  // Response truncated
```

### Diagnostika

```typescript
function checkContextSize(messages: Message[]): void {
  const totalTokens = messages.reduce((sum, m) =>
    sum + estimateTokens(m.content), 0
  )

  console.log(`Context size: ${totalTokens} tokens`)

  if (totalTokens > 150000) {
    console.warn('WARNING: Approaching context limit')
  }

  if (totalTokens > 180000) {
    console.error('CRITICAL: Context overflow likely')
  }
}
```

### Riešenie

```typescript
// Redukcia kontextu
function reduceContext(messages: Message[]): Message[] {
  // 1. Summarize old messages
  const oldMessages = messages.slice(0, -10)
  const summary = summarize(oldMessages)

  // 2. Keep only recent messages
  const recentMessages = messages.slice(-10)

  return [
    { role: 'system', content: `Previous context: ${summary}` },
    ...recentMessages
  ]
}
```

---

## Hallucination Detection

### Symptóm: Neexistujúci kód

```
Claude: "Upravil som funkciu processData v utils/helpers.ts"
Realita: Tento súbor neexistuje
```

### Detekcia

```typescript
function verifyToolResults(
  toolCall: ToolCall,
  result: ToolResult
): VerificationResult {
  if (toolCall.name === 'str_replace_editor') {
    const { path, command } = toolCall.args

    // Pre 'view' - skontroluj či súbor existuje
    if (command === 'view' && result.content === null) {
      return {
        valid: false,
        issue: `File does not exist: ${path}`,
        suggestion: 'List available files first'
      }
    }

    // Pre 'str_replace' - skontroluj či old_str existuje
    if (command === 'str_replace') {
      const fileContent = fs.readFile(path)
      if (!fileContent?.includes(toolCall.args.old_str)) {
        return {
          valid: false,
          issue: 'old_str not found in file',
          suggestion: 'View file content first'
        }
      }
    }
  }

  return { valid: true }
}
```

---

## Loop Detection

### Symptóm: Opakované akcie

```
[Turn 1] Claude: Editing file...
[Turn 2] Claude: Editing same file...
[Turn 3] Claude: Editing same file again...
```

### Detekcia a prevencia

```typescript
interface ActionTracker {
  actions: Array<{
    tool: string
    args: any
    timestamp: number
  }>
}

function detectLoop(tracker: ActionTracker): boolean {
  const recent = tracker.actions.slice(-5)

  // Check for repeated identical actions
  const actionSignatures = recent.map(a =>
    JSON.stringify({ tool: a.tool, args: a.args })
  )

  const uniqueActions = new Set(actionSignatures)

  if (uniqueActions.size === 1 && recent.length >= 3) {
    console.warn('LOOP DETECTED: Same action repeated 3+ times')
    return true
  }

  return false
}

// V API route
if (detectLoop(tracker)) {
  return Response.json({
    error: 'Loop detected',
    suggestion: 'Please clarify your request'
  }, { status: 400 })
}
```

---

## Skill Debugging

### Symptóm: Zlý skill aktivovaný

```
User: "Review this PR"
Expected: code-review skill
Actual: documentation skill
```

### Diagnostika

```
Aké skills máš k dispozícii a ktorý bol práve aktivovaný?
```

### Riešenie

1. **Zlepšite description**:

```yaml
# Pred
description: Use for reviewing code

# Po
description: Use when reviewing pull requests, code review,
PR review, checking code quality, finding bugs in changes
```

2. **Pridajte negative triggers**:

```yaml
description: |
  Use for code review and PR review.
  NOT for documentation review or writing docs.
```

---

## Comprehensive Logging

```typescript
// src/lib/debug.ts

interface DebugContext {
  sessionId: string
  turnNumber: number
  timestamp: Date
}

export class AgentDebugger {
  private context: DebugContext
  private logs: DebugLog[] = []

  logTurn(turn: Turn): void {
    this.logs.push({
      ...this.context,
      type: 'turn',
      data: {
        userMessage: turn.userMessage,
        assistantResponse: turn.assistantResponse,
        toolCalls: turn.toolCalls,
        duration: turn.duration
      }
    })
  }

  logError(error: Error, context: any): void {
    this.logs.push({
      ...this.context,
      type: 'error',
      data: {
        message: error.message,
        stack: error.stack,
        context
      }
    })
  }

  export(): DebugReport {
    return {
      summary: this.generateSummary(),
      logs: this.logs,
      recommendations: this.generateRecommendations()
    }
  }

  private generateRecommendations(): string[] {
    const recommendations: string[] = []

    // Analyze logs for patterns
    const errors = this.logs.filter(l => l.type === 'error')
    if (errors.length > 3) {
      recommendations.push('High error rate - check tool configurations')
    }

    const avgDuration = this.calculateAvgDuration()
    if (avgDuration > 30000) {
      recommendations.push('Slow responses - consider reducing context')
    }

    return recommendations
  }
}
```

---

## Debug Checklist

```
Debugging Checklist:

[ ] Aký je presný error message?
[ ] Ktorý tool zlyhal?
[ ] Aké boli parametre?
[ ] Koľko je kontextu (tokens)?
[ ] Ktorý skill bol aktivovaný?
[ ] Opakuje sa akcia? (loop)
[ ] Existujú súbory ktoré Claude referencuje?
[ ] Sú tools povolené pre tento skill?
[ ] Funguje to s menším kontextom?
[ ] Funguje to s iným modelom?
```

---

## Cvičenia

### Cvičenie 1: Debug Tool
```
Vytvor debugging tool ktorý automaticky analyzuje failures.
```

### Cvičenie 2: Replay System
```
Implementuj replay systém pre reprodukciu bugov.
```

### Cvičenie 3: Health Check
```
Vytvor health check endpoint pre monitoring agenta.
```

---

## Ďalej

Po dokončení Core sekcie pokračujte na [Intermediate](../../03-intermediate/) pre real-world development patterns.
