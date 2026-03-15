# Troubleshooting

Riešenie bežných problémov so Skills a Agents.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](01-struktura-skillov.md))
- Znalosť Built-in Agents ([Built-in Agents](07-built-in-agents.md))

---

## Kritické Limitácie

### Subagenti a Skills

```
┌─────────────────────────────────────────────────────────┐
│  POZOR: Subagenti NEDEDIA skills automaticky!          │
│                                                         │
│  Musíte ich explicitne uviesť v konfigurácii agenta.   │
└─────────────────────────────────────────────────────────┘
```

#### Problém

Keď hlavný agent spustí subagenta (pomocou Task tool), subagent nemá automaticky prístup k skills ktoré má hlavný agent.

#### Riešenie

Explicitne uviesť skills v konfigurácii subagenta:

```yaml
subagent:
  name: code-reviewer
  skills:
    - code-review-skill
    - testing-conventions
```

### Built-in agenti bez prístupu ku Skills

Tieto vstavané agenti **NEMÔŽU** pristupovať ku skills:

| Agent | Funkcia | Workaround |
|-------|---------|------------|
| **Explorer** | Prieskum codebase | Poskytnúť info priamo v prompte |
| **Plan** | Plánovanie implementácie | Poskytnúť info priamo v prompte |
| **Verify** | Verifikácia zmien | Poskytnúť info priamo v prompte |

#### Príklad workaround

Namiesto spoliehania sa na skill:
```
"Preskúmaj codebase a nájdi všetky API endpointy"
```

Poskytnite kontext priamo:
```
"Preskúmaj codebase a nájdi všetky API endpointy.
Naše API súbory sú v src/api/ a používame Express.js routing."
```

### Ďalšie limitácie

| Limitácia | Popis |
|-----------|-------|
| **Veľkosť SKILL.md** | Odporúčané max 500 riadkov |
| **Počet skills** | Príliš veľa skills môže spomaliť načítanie |
| **Cyklické závislosti** | Skills nemôžu referencovať iné skills |

---

## Debugging Checklist

```
[ ] Je skill v správnom priečinku? (.claude/skills/<nazov>/)
[ ] Volá sa súbor presne SKILL.md? (case-sensitive!)
[ ] Má SKILL.md správny frontmatter formát?
[ ] Je description dostatočne špecifický?
[ ] Sú povolené potrebné tools?
```

---

## Časté problémy a riešenia

| Problém | Príčina | Riešenie |
|---------|---------|----------|
| Skill sa neaktivuje | Vague description | Pridaj konkrétne trigger slová |
| Skill sa nenačíta | Chybná štruktúra | Skontroluj priečinok a názov súboru |
| Aktivuje sa zlý skill | Príliš podobné descriptions | Jasné vymedzenie domén |
| Skill funguje čiastočne | Chýbajúce allowed-tools | Pridaj potrebné tools |
| Pomalé načítavanie | Príliš veľký SKILL.md | Použi references/ |
| Zastaralý obsah | Skill neodráža aktuálny stav | Pravidelné review |
| Konflikt skills | Prekrývajúce sa descriptions | Pridaj "NOT for..." |

---

## Problém: Skill sa neaktivuje

### Diagnostika

1. Skontrolujte či Claude vidí skill:
   ```
   "Aké skills máš k dispozícii?"
   ```

2. Skontrolujte description - obsahuje slová ktoré používate?

### Riešenie

Rozšírte description o skutočné frázy ktoré používatelia píšu:

```yaml
# Pred
description: Skill for deployment

# Po
description: Use when deploying, pushing to production, releasing,
running CI/CD pipeline, or when user mentions "deploy", "release",
"production", "staging"
```

---

## Problém: Skill sa nenačíta

### Diagnostika

Skontrolujte štruktúru:
```
.claude/skills/my-skill/SKILL.md   # Správne
.claude/skills/my-skill/skill.md   # Chyba - case sensitive
.claude/skills/SKILL.md            # Chyba - chýba priečinok
```

### Riešenie

- Názov súboru musí byť presne `SKILL.md` (veľké písmená)
- Súbor musí byť v pomenovanom priečinku

---

## Problém: Aktivuje sa zlý skill

### Diagnostika

Porovnajte descriptions oboch skills - pravdepodobne sa prekrývajú.

### Riešenie

Urobte descriptions špecifickejšie:

```yaml
# Skill A - pred
description: Use for testing

# Skill A - po
description: Use for unit testing with Jest, testing React components.
NOT for E2E tests.

# Skill B - pred
description: Use for testing

# Skill B - po
description: Use for E2E testing with Playwright, browser automation.
NOT for unit tests.
```

---

## Problém: Skill funguje čiastočne

### Diagnostika

Claude nemôže vykonať niektoré akcie.

### Riešenie

Skontrolujte a pridajte potrebné tools:

```yaml
# Pred - chýba Bash
allowed-tools: [Read, Write, Edit]

# Po - s Bash pre testy
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep]
```

---

## Problém: Pomalé načítavanie

### Diagnostika

SKILL.md má viac ako 500 riadkov.

### Riešenie

Rozdeľte obsah do references/:

```
.claude/skills/my-skill/
├── SKILL.md              # Krátke, len hlavné info
└── references/
    ├── api-docs.md       # Detailná dokumentácia
    ├── examples.md       # Príklady
    └── conventions.md    # Konvencie
```

V SKILL.md odkážte:
```markdown
## Detaily
Pozri [API Docs](references/api-docs.md)
```

---

## Explicitné načítanie Skills

Ak sa skill neaktivuje automaticky, môžete ho načítať explicitne:

```bash
# Načítaj konkrétny skill
/skill load deployment

# Zoznam dostupných skills
/skill list
```

Alebo v prompte:
```
Použi skill "api-testing" a napíš testy pre /users endpoint.
```

---

## Cvičenia

### Cvičenie 1: Debugging
```
Vytvorte skill ktorý sa zámerne neaktivuje
(chybný description) a opravte ho.
```

### Cvičenie 2: Konflikt
```
Vytvorte dva skills s prekrývajúcimi sa
descriptions a vyriešte konflikt.
```

### Cvičenie 3: Veľký Skill
```
Rozdeľte skill s 800+ riadkami
do štruktúry s references/.
```

---

## Ďalej

[Performance](11-performance.md) - Optimalizácia výkonu a nákladov
