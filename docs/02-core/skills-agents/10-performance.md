# Performance

Optimalizácia výkonu a nákladov pri práci so Skills a Agents.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](01-struktura-skillov.md))
- Znalosť allowed-tools ([Allowed Tools](08-allowed-tools.md))

---

## Model Selection v Skills

### Výber modelu podľa úlohy

```yaml
# Quick fixes - Haiku (najrýchlejší, najlacnejší)
---
name: quick-fix
description: Simple bug fixes, typos, small changes
model: haiku
---

# Bežné coding - Sonnet (vyvážený)
---
name: coding
description: General development tasks
model: sonnet
---

# Architektúra - Opus (najschopnejší)
---
name: architecture
description: System design, complex refactoring
model: opus
---
```

### Token Economics

| Model | Input (1M tokens) | Output (1M tokens) | Use case |
|-------|-------------------|-------------------|----------|
| Haiku | $0.25 | $1.25 | Quick fixes, simple tasks |
| Sonnet | $3.00 | $15.00 | General development |
| Opus | $15.00 | $75.00 | Complex architecture |

### Typická session

```
Priemerná coding session:
- Input: ~50K tokens (kontext, kód, prompty)
- Output: ~10K tokens (odpovede, kód)

Sonnet: ~$0.30 per session
Opus: ~$1.50 per session
```

---

## SKILL.md Optimalizácia

### Efektívna štruktúra

```yaml
---
name: optimized-coding
description: General coding assistance
model: sonnet  # Default to balanced model
allowed-tools: [Read, Edit, Glob, Grep]  # Only needed tools
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

### Veľkosť SKILL.md

| Veľkosť | Odporúčanie |
|---------|-------------|
| < 100 riadkov | Ideálne |
| 100-500 riadkov | Akceptovateľné |
| > 500 riadkov | Rozdeľ do references/ |

```
.claude/skills/my-skill/
├── SKILL.md              # Max 100 riadkov - hlavné info
└── references/
    ├── api-docs.md       # Detailná dokumentácia
    ├── examples.md       # Príklady
    └── conventions.md    # Konvencie
```

---

## Subagent Optimalizácia

### Model pre subagentov

```yaml
# V hlavnom agentovi
subagent_config:
  explorer:
    model: haiku      # Rýchle prehľadávanie
  code_writer:
    model: sonnet     # Vyvážené písanie kódu
  architect:
    model: opus       # Komplexné rozhodnutia
```

### Minimalizácia Task calls

```
# Neefektívne - 3 separate Task calls
Task(explore codebase)
Task(find tests)
Task(analyze structure)

# Efektívne - 1 Task call
Task(explore codebase, find tests, and analyze structure)
```

---

## Redukcia kontextu

### Efektívne prompty

```
# Verbose (zbytočné tokeny)
"Mohol by si prosím ťa pozrieť na tento kód a povedať mi
či by si mohol nájsť nejaké problémy a opraviť ich?"

# Efektívne
"Nájdi a oprav bugy v tomto kóde:"
```

### Cielený kontext

```
# Zlé - posielame celý súbor
"Oprav bug v tomto súbore: [5000 riadkov kódu]"

# Dobré - len relevantná časť
"Oprav bug v funkcii calculateTotal na riadkoch 142-156:
[relevantný kód]"
```

---

## Allowed-tools Optimalizácia

### Minimálne tools = Rýchlejšie vykonanie

```yaml
# Zbytočne široké
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep, Task, WebFetch, WebSearch]

# Optimalizované pre code review
allowed-tools: [Read, Glob, Grep]

# Optimalizované pre testing
allowed-tools: [Read, Bash, Glob, Grep]
```

### Impact na výkon

| Počet tools | Overhead |
|-------------|----------|
| 3-4 tools | Minimálny |
| 5-7 tools | Stredný |
| 8+ tools | Vysoký |

---

## Batch Operations

### Zlučovanie operácií

```
# Neefektívne - jednotlivé requesty
"Pridaj typ k premennej x"
"Pridaj typ k premennej y"
"Pridaj typ k premennej z"

# Efektívne - batch request
"Pridaj TypeScript typy k týmto premenným:
- x na riadku 10
- y na riadku 15
- z na riadku 20"
```

---

## Monitoring a Budget

### Budget tracking v Skill

```yaml
---
name: budget-aware-coding
description: Coding with cost awareness
model: sonnet
---

# Budget-Aware Coding

## Before Starting
- Estimate complexity
- Choose appropriate model
- Consider batch operations

## Cost Guidelines
- Simple fix: Use haiku (~$0.01)
- Feature: Use sonnet (~$0.30)
- Architecture: Use opus (~$1.50)
```

### Denné a mesačné limity

```
Odporúčané limity:
- Individual developer: $10-20/deň
- Team (5 devs): $50-100/deň
- Enterprise: Podľa potreby s alertmi
```

---

## Optimalizačná matica

| Aspekt | Optimalizácia |
|--------|---------------|
| **Model** | Haiku pre jednoduché, Opus pre komplexné |
| **SKILL.md** | < 100 riadkov, používaj references/ |
| **Tools** | Minimum potrebných |
| **Context** | Len relevantné časti kódu |
| **Prompty** | Stručné, konkrétne |
| **Batch** | Zlučuj podobné operácie |

---

## Cvičenia

### Cvičenie 1: Model Selection
```
Pre váš projekt určte optimálny model
pre 5 najčastejších úloh.
```

### Cvičenie 2: Skill Audit
```
Prejdite existujúce skills a optimalizujte:
- Veľkosť SKILL.md
- allowed-tools
- model selection
```

### Cvičenie 3: Cost Tracking
```
Sledujte náklady počas týždňa a
identifikujte optimalizačné príležitosti.
```

---

## Ďalej

[Model Selection](11-model-selection.md) - Výber správneho modelu (Haiku vs Sonnet vs Opus)

