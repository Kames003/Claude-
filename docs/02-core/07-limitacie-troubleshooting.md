# Limitácie a Troubleshooting

Tento modul pokrýva kritické obmedzenia Claude Code a riešenie bežných problémov.

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

## Troubleshooting

### Časté problémy a riešenia

| Problém | Príčina | Riešenie |
|---------|---------|----------|
| **Skill sa neaktivuje** | Nedostatočný description | Rozšírte description o konkrétne trigger frázy |
| **Skill sa nenačíta** | Chybná štruktúra | Skontrolujte priečinok a názov súboru |
| **Aktivuje sa zlý skill** | Príliš podobné descriptions | Urobte descriptions výraznejšie odlíšené |
| **Skill funguje čiastočne** | Chýbajúce allowed-tools | Pridajte potrebné nástroje do frontmatter |

### Debugging checklist

```
[ ] Je skill v správnom priečinku? (.claude/skills/<nazov>/)
[ ] Volá sa súbor presne SKILL.md? (case-sensitive!)
[ ] Má SKILL.md správny frontmatter formát?
[ ] Je description dostatočne špecifický?
[ ] Sú povolené potrebné tools?
```

### Problém: Skill sa neaktivuje

#### Diagnostika

1. Skontrolujte či Claude vidí skill:
   ```
   "Aké skills máš k dispozícii?"
   ```

2. Skontrolujte description - obsahuje slová ktoré používate?

#### Riešenie

Rozšírte description o skutočné frázy ktoré používatelia píšu:

```yaml
# Pred
description: Skill for deployment

# Po
description: Use when deploying, pushing to production, releasing,
running CI/CD pipeline, or when user mentions "deploy", "release",
"production", "staging"
```

### Problém: Skill sa nenačíta

#### Diagnostika

Skontrolujte štruktúru:
```
.claude/skills/my-skill/SKILL.md   # Správne
.claude/skills/my-skill/skill.md   # Chyba - case sensitive
.claude/skills/SKILL.md            # Chyba - chýba priečinok
```

#### Riešenie

- Názov súboru musí byť presne `SKILL.md` (veľké písmená)
- Súbor musí byť v pomenovanom priečinku

### Problém: Aktivuje sa zlý skill

#### Diagnostika

Porovnajte descriptions oboch skills - pravdepodobne sa prekrývajú.

#### Riešenie

Urobte descriptions špecifickejšie:

```yaml
# Skill A - pred
description: Use for testing

# Skill A - po
description: Use for unit testing with Jest, testing React components

# Skill B - pred
description: Use for testing

# Skill B - po
description: Use for E2E testing with Playwright, browser automation
```

---

---

## Dalej

[Built-in Agents](08-built-in-agents.md) - Explorer, Plan, Verify agenti
