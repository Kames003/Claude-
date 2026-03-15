# Allowed Tools

Konfigurácia a bezpečnostné aspekty allowed-tools v Skills.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](01-struktura-skillov.md))
- Znalosť Built-in Agents ([Built-in Agents](07-built-in-agents.md))

---

## Všetky dostupné nástroje

### Súborové operácie

| Nástroj | Funkcia | Bežné použitie |
|---------|---------|----------------|
| **Read** | Čítanie súborov | Zobrazenie kódu, konfigurácií |
| **Write** | Zápis nových súborov | Vytvorenie nových komponentov |
| **Edit** | Editácia existujúcich | Úprava existujúceho kódu |
| **Glob** | Vyhľadávanie podľa vzoru | `**/*.tsx`, `src/**/*.test.ts` |
| **Grep** | Vyhľadávanie v obsahu | Nájdenie referencií, definícií |

### Systémové operácie

| Nástroj | Funkcia | Bežné použitie |
|---------|---------|----------------|
| **Bash** | Shell príkazy | `npm install`, `git status`, `pytest` |
| **Task** | Spustenie subagenta | Delegovanie komplexných úloh |

### Externé zdroje

| Nástroj | Funkcia | Bežné použitie |
|---------|---------|----------------|
| **WebFetch** | HTTP požiadavky | Stiahnutie dokumentácie |
| **WebSearch** | Web search | Hľadanie aktuálnych info |

---

## Obmedzenie nástrojov v Skills

V SKILL.md môžete obmedziť ktoré nástroje má Claude k dispozícii:

```yaml
---
name: read-only-review
description: Use for code review without making changes
allowed-tools: [Read, Glob, Grep]  # Bez Write, Edit, Bash
---
```

---

## Bezpečnostná matica

| Účel | allowed-tools | Bezpečnosť |
|------|---------------|------------|
| **Read-only analýza** | `[Read, Glob, Grep]` | Najvyššia |
| **Code review** | `[Read, Glob, Grep, Bash]` | Vysoká (len testy) |
| **Editácia kódu** | `[Read, Write, Edit, Glob, Grep]` | Stredná |
| **Full development** | `[Read, Write, Edit, Bash, Glob, Grep]` | Nižšia |
| **Multi-agent** | `[Read, Write, Edit, Bash, Glob, Grep, Task]` | Najnižšia |

---

## Security Best Practices

### Read-only pre review

```yaml
# Pre code review - bez zápisu
---
name: code-review
description: Code review without modifications
allowed-tools: [Read, Glob, Grep]
---
```

### Testovanie bez editácie

```yaml
# Pre testovanie - len read + bash pre testy
---
name: test-runner
description: Run and analyze tests
allowed-tools: [Read, Bash, Glob, Grep]
---
```

### Deployment s obmedzeniami

```yaml
# Pre deployment - špecifické príkazy
---
name: deployment
description: Deploy to staging/production
allowed-tools: [Read, Bash]
---

# V obsahu SKILL.md definuj presne ktoré príkazy:
## Povolené príkazy
- npm run build
- npm run deploy:staging
- npm run deploy:production

## Zakázané
- rm -rf
- DROP TABLE
```

---

## Decision Tree: Výber nástrojov

```
Aký je účel skillu?
│
├── Len analýza/review?
│   └── [Read, Glob, Grep]
│
├── Spúšťanie testov?
│   └── [Read, Bash, Glob, Grep]
│
├── Editácia kódu?
│   └── [Read, Write, Edit, Glob, Grep]
│
├── Plný development?
│   └── [Read, Write, Edit, Bash, Glob, Grep]
│
└── Multi-agent orchestrácia?
    └── [Read, Write, Edit, Bash, Glob, Grep, Task]
```

---

## Príklady použitia

### Security Audit Skill

```yaml
---
name: security-audit
description: Security review - read only
allowed-tools: [Read, Glob, Grep]
model: opus
---

# Security Audit

## Scope
- Read source code
- Search for patterns
- NO modifications allowed

## Patterns to check
- Hardcoded credentials
- SQL injection
- XSS vulnerabilities
```

### Refactoring Skill

```yaml
---
name: refactoring
description: Code refactoring with full edit access
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
model: sonnet
---

# Refactoring Skill

## Capabilities
- Read and analyze code
- Edit existing files
- Create new files if needed
- Run tests to verify

## Process
1. Read code
2. Edit with small changes
3. Run tests
4. Repeat
```

---

## Tool Chaining Patterns

### Pattern 1: Explore → Understand → Modify

```
1. Glob - nájdi relevantné súbory
2. Read - prečítaj a pochop
3. Edit - uprav
4. Bash - testuj
```

### Pattern 2: Search → Analyze → Report

```
1. Grep - nájdi všetky výskyty
2. Read - prečítaj kontext
3. (output) - sumarizuj
```

### Pattern 3: Build → Test → Fix

```
1. Bash(npm run build) - buildni
2. (ak chyba) Read(error file) - analyzuj
3. Edit - oprav
4. Bash(npm run build) - znova
```

---

## Subagenti a allowed-tools

### Explicitné špecifikovanie

```yaml
---
name: specialized-agent
skills:
  - code-review     # Má svoje allowed-tools
  - testing         # Má svoje allowed-tools
inherit_skills: false
---
```

### Dôležité

- Subagenti **nededia** allowed-tools automaticky
- Musíte špecifikovať explicitne
- Alebo vytvoriť skill s potrebnými tools

---

## Cvičenia

### Cvičenie 1: Read-only Skill
```
Vytvorte skill pre analýzu kódu
ktorý nemôže nič modifikovať.
```

### Cvičenie 2: Minimálne Tools
```
Pre váš testovací workflow určte
minimálnu sadu potrebných tools.
```

### Cvičenie 3: Security Review
```
Vytvorte skill pre security audit
s najprísnejšími obmedzeniami.
```

---

## Ďalej

[Troubleshooting](10-troubleshooting.md) - Riešenie problémov so skills a agents
