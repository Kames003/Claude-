# Model Selection

Kedy použiť ktorý model a optimalizácia nákladov.

---

## Dostupné modely

| Model | Rýchlosť | Cena | Použitie |
|-------|----------|------|----------|
| **Haiku** | Najrýchlejší | Najlacnejší | Jednoduché úlohy, quick fixes |
| **Sonnet** | Rýchly | Stredná | Väčšina vývojových úloh |
| **Opus** | Pomalší | Najdrahší | Komplexné architektúry, ťažké problémy |

---

## Kedy použiť ktorý model

### Haiku - Rýchle operácie

```yaml
# V SKILL.md
model: haiku
```

Ideálne pre:
- Formátovanie kódu
- Jednoduché refaktoringy
- Generovanie boilerplate
- Quick code reviews
- Syntax opravy

**Príklad:**
```
Oprav syntax error na riadku 42.
Pridaj missing import pre useState.
Preformátuj tento súbor podľa Prettier.
```

### Sonnet - Default voľba

```yaml
# V SKILL.md (alebo bez špecifikácie)
model: sonnet
```

Ideálne pre:
- Väčšinu vývojových úloh
- Feature implementácie
- Bug fixing
- Test writing
- Code reviews s návrhmi

**Príklad:**
```
Implementuj nový endpoint pre user profile.
Napíš testy pre FileSystem triedu.
Refaktoruj tento komponent na použitie hooks.
```

### Opus - Komplexné úlohy

```yaml
# V SKILL.md
model: opus
```

Ideálne pre:
- Architektúrne rozhodnutia
- Komplexné debugging
- Performance optimalizácie
- Security audity
- Multi-file refaktoringy

**Príklad:**
```
Navrhni architektúru pre real-time collaboration.
Analyzuj celý codebase a nájdi security vulnerabilities.
Optimalizuj performance kritických ciest.
```

---

## Model Selection v Skills

### Code Review Skill (Haiku)

```yaml
---
name: quick-review
description: Quick code review for syntax and style issues
model: haiku
allowed-tools: [Read, Glob, Grep]
---

# Quick Review

Fast review for obvious issues:
- Syntax errors
- Missing imports
- Style violations
- Unused variables
```

### Architecture Skill (Opus)

```yaml
---
name: architecture
description: Complex architectural decisions and system design
model: opus
allowed-tools: [Read, Glob, Grep, WebSearch]
---

# Architecture Planning

For complex decisions:
- System design
- Database schema
- API design
- Performance architecture
```

---

## Cost Tracking

### Približné náklady (per 1M tokens)

| Model | Input | Output |
|-------|-------|--------|
| Haiku | $0.25 | $1.25 |
| Sonnet | $3.00 | $15.00 |
| Opus | $15.00 | $75.00 |

### Optimalizačné stratégie

1. **Použite správny model pre úlohu**
   - Nepoužívajte Opus na formátovanie
   - Nepoužívajte Haiku na architektúru

2. **Skráťte prompty**
   - Odstráňte zbytočný kontext
   - Buďte konkrétni

3. **Cachujte odpovede**
   - Rovnaké otázky = rovnaké odpovede
   - Ukladajte výsledky analýz

4. **Batch operácie**
   - Viac zmien v jednom prompte
   - Menej round-trips

---

## UIGen Provider Pattern

```typescript
// src/lib/provider.ts

import { anthropic } from '@ai-sdk/anthropic'

export function getProvider(modelType: 'haiku' | 'sonnet' | 'opus' = 'sonnet') {
  const modelMap = {
    haiku: 'claude-haiku-4-5-20250514',
    sonnet: 'claude-sonnet-4-20250514',
    opus: 'claude-opus-4-20250514'
  }

  return anthropic(modelMap[modelType])
}

// Použitie:
const result = await streamText({
  model: getProvider('haiku'), // Pre rýchle operácie
  // ...
})
```

---

## Automatický model selection

```
Vytvor funkciu ktorá automaticky vyberie model podľa complexity promptu.
```

```typescript
function selectModel(prompt: string, context: string): ModelType {
  const totalLength = prompt.length + context.length

  // Jednoduché heuristiky
  const complexKeywords = [
    'architecture', 'design', 'security', 'performance',
    'refactor entire', 'analyze all', 'optimize'
  ]

  const simpleKeywords = [
    'fix typo', 'add import', 'format', 'rename'
  ]

  const promptLower = prompt.toLowerCase()

  if (simpleKeywords.some(k => promptLower.includes(k))) {
    return 'haiku'
  }

  if (complexKeywords.some(k => promptLower.includes(k))) {
    return 'opus'
  }

  // Default to sonnet for most tasks
  return 'sonnet'
}
```

---

## Cvičenia

### Cvičenie 1: Cost Dashboard
```
Vytvor dashboard komponent ktorý zobrazuje estimated costs pre session.
```

### Cvičenie 2: Model Routing
```
Implementuj router ktorý automaticky vyberá model podľa typu úlohy.
```

### Cvičenie 3: Budget Limits
```
Pridaj budget limiting - zastav keď sa dosiahne limit.
```

---

---

## Ďalej

[Multi-Agent Orchestration](12-multi-agent.md) - Koordinácia viacerých agentov
