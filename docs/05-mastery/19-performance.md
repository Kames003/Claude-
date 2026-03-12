# Performance Optimization

Token a cost optimalizácia pre efektívne využitie Claude Code.

---

## Token Economics

### Približné náklady

| Model | Input (1M tokens) | Output (1M tokens) |
|-------|-------------------|-------------------|
| Haiku | $0.25 | $1.25 |
| Sonnet | $3.00 | $15.00 |
| Opus | $15.00 | $75.00 |

### Typická session

```
Priemerná coding session:
- Input: ~50K tokens (kontext, kód, prompty)
- Output: ~10K tokens (odpovede, kód)

Sonnet: ~$0.30 per session
Opus: ~$1.50 per session
```

---

## Optimalizačné stratégie

### 1. Správny model pre úlohu

```yaml
# Skill pre quick fixes - Haiku
---
name: quick-fix
model: haiku
---

# Skill pre architektúru - Opus
---
name: architecture
model: opus
---
```

### 2. Redukcia kontextu

```
# Zlé - posielame celý súbor
"Oprav bug v tomto súbore: [5000 riadkov kódu]"

# Dobré - len relevantná časť
"Oprav bug v funkcii calculateTotal na riadkoch 142-156:
[relevantný kód]"
```

### 3. Efektívne prompty

```
# Verbose (zbytočné tokeny)
"Mohol by si prosím ťa pozrieť na tento kód a povedať mi
či by si mohol nájsť nejaké problémy a opraviť ich?"

# Efektívne
"Nájdi a oprav bugy v tomto kóde:"
```

---

## Caching

### Response caching

```typescript
// src/lib/cache.ts

const cache = new Map<string, CachedResponse>()

interface CachedResponse {
  response: string
  timestamp: number
  tokens: number
}

function getCacheKey(prompt: string, context: string): string {
  return crypto
    .createHash('md5')
    .update(prompt + context)
    .digest('hex')
}

async function cachedQuery(
  prompt: string,
  context: string
): Promise<string> {
  const key = getCacheKey(prompt, context)
  const cached = cache.get(key)

  // Cache hit - return cached response
  if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
    console.log(`Cache hit: saved ${cached.tokens} tokens`)
    return cached.response
  }

  // Cache miss - query API
  const response = await queryAPI(prompt, context)

  cache.set(key, {
    response,
    timestamp: Date.now(),
    tokens: countTokens(prompt + context + response)
  })

  return response
}
```

### Semantic caching

```typescript
// Cache podobných otázok

function findSimilarCached(prompt: string): CachedResponse | null {
  for (const [key, cached] of cache.entries()) {
    if (similarity(prompt, cached.originalPrompt) > 0.9) {
      return cached
    }
  }
  return null
}
```

---

## Batch Operations

```
# Namiesto jednotlivých requestov

"Pridaj typ k premennej x"
"Pridaj typ k premennej y"
"Pridaj typ k premennej z"

# Batch request

"Pridaj TypeScript typy k týmto premenným:
- x na riadku 10
- y na riadku 15
- z na riadku 20"
```

---

## Context Window Management

### Sliding window

```typescript
function manageContext(
  messages: Message[],
  maxTokens: number
): Message[] {
  let totalTokens = 0
  const result: Message[] = []

  // Keep system message
  const systemMessage = messages.find(m => m.role === 'system')
  if (systemMessage) {
    totalTokens += countTokens(systemMessage.content)
    result.push(systemMessage)
  }

  // Add messages from newest to oldest
  for (let i = messages.length - 1; i >= 0; i--) {
    const message = messages[i]
    if (message.role === 'system') continue

    const tokens = countTokens(message.content)

    if (totalTokens + tokens > maxTokens) {
      break
    }

    totalTokens += tokens
    result.unshift(message)
  }

  return result
}
```

### Summarization

```typescript
async function summarizeOldContext(
  messages: Message[]
): Promise<string> {
  const oldMessages = messages.slice(0, -10)  // Keep recent 10

  const summary = await queryAPI(
    "Summarize this conversation in 3 sentences:",
    oldMessages.map(m => m.content).join('\n')
  )

  return summary
}
```

---

## Budget Tracking

```typescript
// src/lib/budget.ts

interface BudgetTracker {
  dailyLimit: number
  monthlyLimit: number
  currentDaily: number
  currentMonthly: number
}

class TokenBudget {
  private tracker: BudgetTracker

  async checkBudget(estimatedTokens: number): Promise<boolean> {
    const estimatedCost = this.calculateCost(estimatedTokens)

    if (this.tracker.currentDaily + estimatedCost > this.tracker.dailyLimit) {
      throw new Error('Daily budget exceeded')
    }

    if (this.tracker.currentMonthly + estimatedCost > this.tracker.monthlyLimit) {
      throw new Error('Monthly budget exceeded')
    }

    return true
  }

  recordUsage(inputTokens: number, outputTokens: number): void {
    const cost = this.calculateCost(inputTokens, outputTokens)
    this.tracker.currentDaily += cost
    this.tracker.currentMonthly += cost
    this.persist()
  }

  private calculateCost(input: number, output: number = 0): number {
    // Sonnet pricing
    return (input * 0.003 / 1000) + (output * 0.015 / 1000)
  }
}
```

---

## Monitoring Dashboard

```typescript
// src/components/TokenDashboard.tsx

export function TokenDashboard() {
  const { usage } = useTokenUsage()

  return (
    <div className="p-4 border rounded">
      <h3>Token Usage</h3>

      <div className="grid grid-cols-3 gap-4">
        <Stat
          label="Today"
          value={usage.daily.tokens}
          limit={usage.limits.daily}
        />
        <Stat
          label="This Month"
          value={usage.monthly.tokens}
          limit={usage.limits.monthly}
        />
        <Stat
          label="Estimated Cost"
          value={`$${usage.monthly.cost.toFixed(2)}`}
        />
      </div>

      <ProgressBar
        value={usage.monthly.tokens}
        max={usage.limits.monthly}
      />
    </div>
  )
}
```

---

## SKILL.md Optimization

```yaml
---
name: optimized-coding
description: General coding assistance
model: sonnet  # Default to cheaper model

# Reduce context
max_context_files: 5
max_file_lines: 500

# Caching
cache_responses: true
cache_ttl: 3600
---

# Coding Skill

## Context Rules
- Only include files directly relevant to the task
- Truncate large files to relevant sections
- Summarize unchanged context

## Response Rules
- Be concise
- No unnecessary explanations
- Code-focused responses
```

---

## Cvičenia

### Cvičenie 1: Token Counter
```
Vytvor komponent ktorý počíta tokeny v reálnom čase.
```

### Cvičenie 2: Budget Alerts
```
Implementuj alert systém pre blížiace sa budget limity.
```

### Cvičenie 3: Optimization Report
```
Vytvor reportovací nástroj pre analýzu token usage.
```

---

## Ďalej

[Security](20-security.md)
