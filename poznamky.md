# Claude Code Workshop
## Kompletný sprievodca pre efektívnu prácu s Claude Code

---

## Obsah

1. [Úvod do Claude Code](#úvod-do-claude-code)
2. [Core Concepts - Skills](#core-concepts---skills)
3. [Štruktúra Skill-ov](#štruktúra-skill-ov)
4. [Priorita a Distribúcia](#priorita-a-distribúcia)
5. [Kritické Limitácie](#kritické-limitácie)
6. [Troubleshooting](#troubleshooting)
7. [Tool Use - Ako Claude pracuje s nástrojmi](#tool-use---ako-claude-pracuje-s-nástrojmi)
8. [Praktické tipy](#praktické-tipy)

---

## Úvod do Claude Code

Claude Code je CLI nástroj od Anthropic, ktorý umožňuje interaktívnu prácu s kódom priamo z terminálu. Na rozdiel od webového rozhrania Claude, Claude Code:

- Má prístup k súborovému systému
- Môže spúšťať bash príkazy
- Rozumie kontextu celého projektu
- Podporuje **Skills** - modulárne znalostné balíčky

---

## Core Concepts - Skills

### Čo sú Skills?

**Skills** sú task-špecifické znalostné moduly uložené v `.claude/skills/` priečinkoch.

### Skills vs CLAUDE.md - Kľúčový rozdiel

| Aspekt | CLAUDE.md | Skills |
|--------|-----------|--------|
| **Načítanie** | Vždy, v každej konverzácii | Kontextovo, len keď sú relevantné |
| **Umiestnenie** | Root projektu | `.claude/skills/<nazov>/` |
| **Účel** | Všeobecné pravidlá projektu | Špecifické úlohy a workflows |

### Pravidlo 500 riadkov

> **Best Practice:** "Keep SKILL.md under 500 lines. If you're exceeding that, consider whether content should be split into separate reference files."

Ak skill presahuje 500 riadkov, rozdeľte ho na:
- Hlavný `SKILL.md` s prehľadom
- Referencie v `references/` priečinku

---

## Štruktúra Skill-ov

### Odporúčaná adresárová štruktúra

```
.claude/skills/<nazov-skillu>/
├── SKILL.md           # Hlavný súbor (povinný)
├── scripts/           # Spustiteľný kód
│   ├── build.sh
│   └── deploy.py
├── references/        # Dokumentácia
│   ├── api-docs.md
│   └── conventions.md
└── assets/            # Obrázky, šablóny, dátové súbory
    ├── template.json
    └── diagram.png
```

### Povinné komponenty SKILL.md

Každý skill vyžaduje:

| Komponent | Popis | Príklad |
|-----------|-------|---------|
| **Name** | Identifikátor skillu | `deployment-skill` |
| **Description** | Určuje kedy sa skill aktivuje | `"Use this skill when deploying to production or staging environments"` |
| **Frontmatter** (voliteľné) | Konfigurácia | `allowed-tools`, `model` |

### Príklad SKILL.md

```markdown
---
name: api-testing
description: Use this skill when writing or running API tests, testing endpoints, or debugging API responses
allowed-tools: [Bash, Read, Write, Edit]
model: sonnet
---

# API Testing Skill

## Kedy použiť
- Písanie testov pre REST/GraphQL endpointy
- Debugging API responses
- Validácia request/response schém

## Konvencie
...
```

**Kritické:** Description je najdôležitejšia časť - určuje kedy Claude skill aktivuje!

---

## Priorita a Distribúcia

### Hierarchia priorít

Skills sa aplikujú v tomto poradí (od najvyššej priority):

```
1. Enterprise    (firemné nastavenia)
         ↓
2. Personal      (osobné nastavenia používateľa)
         ↓
3. Project       (.claude/skills/ v projekte)
         ↓
4. Plugins       (marketplace pluginy)
```

Ak existuje konflikt, vyhrá skill s vyššou prioritou.

### Metódy distribúcie

| Metóda | Použitie | Výhody |
|--------|----------|--------|
| **Repository commit** | `.claude/skills/` v repo | Verzionované s kódom, zdieľané v tíme |
| **Plugin marketplace** | Cross-repository zdieľanie | Znovupoužiteľné naprieč projektami |
| **Subagent frontmatter** | Explicitný listing | Presná kontrola nad dedením |

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

**Príklad správnej konfigurácie:**
```yaml
subagent:
  name: code-reviewer
  skills:
    - code-review-skill
    - testing-conventions
```

### Built-in agenti bez prístupu ku Skills

Tieto vstavané agenti **NEMÔŽU** pristupovať ku skills:

- **Explorer** - prieskum codebase
- **Plan** - plánovanie implementácie
- **Verify** - verifikácia zmien

Pri práci s týmito agentmi musíte relevantné informácie poskytnúť priamo v prompte.

---

## Troubleshooting

### Časté problémy a riešenia

| Problém | Príčina | Riešenie |
|---------|---------|----------|
| **Skill sa neaktivuje** | Nedostatočný description | Rozšírte description o konkrétne trigger frázy, ktoré používatelia bežne píšu |
| **Skill sa nenačíta** | Chybná štruktúra | Skontrolujte: pomenovaný priečinok + presný názov `SKILL.md` |
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

---

## Tool Use - Ako Claude pracuje s nástrojmi

### Princíp fungovania

Language modely generujú štruktúrované textové požiadavky na akcie. Systém potom prekladá tieto odpovede do skutočných operácií.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    Claude    │ ──► │   Systém     │ ──► │   Akcia      │
│  "ReadFile:  │     │  (preklad)   │     │  (skutočné   │
│   main.go"   │     │              │     │   čítanie)   │
└──────────────┘     └──────────────┘     └──────────────┘
```

### Dostupné nástroje v Claude Code

| Nástroj | Funkcia | Príklad použitia |
|---------|---------|------------------|
| **Read** | Čítanie súborov | Zobrazenie obsahu kódu |
| **Write** | Zápis súborov | Vytvorenie nových súborov |
| **Edit** | Editácia súborov | Úprava existujúceho kódu |
| **Bash** | Shell príkazy | `npm install`, `git status` |
| **Glob** | Vyhľadávanie súborov | `**/*.tsx` |
| **Grep** | Vyhľadávanie v obsahu | Nájdenie referencií |
| **WebFetch** | HTTP požiadavky | Stiahnutie dokumentácie |
| **Task** | Spustenie subagenta | Komplexné úlohy |

---

## Praktické tipy

### 1. Písanie efektívnych descriptions

**Zlé:**
```
description: Skill for testing
```

**Dobré:**
```
description: Use when writing unit tests, integration tests, running pytest,
jest, vitest, or when user mentions "test", "testing", "TDD", or "coverage"
```

### 2. Organizácia skills v tíme

```
.claude/skills/
├── code-review/          # Review workflow
├── deployment/           # CI/CD a deployment
├── testing/              # Testovacie konvencie
├── api-design/           # API štandardy
└── onboarding/           # Pre nových členov tímu
```

### 3. CLAUDE.md vs Skills - Kedy čo použiť

| Použite CLAUDE.md pre: | Použite Skills pre: |
|------------------------|---------------------|
| Všeobecné pravidlá projektu | Špecifické workflows |
| Import konvencie | Deployment procesy |
| Coding standards | Testing stratégie |
| Vždy-relevantné info | Občas-potrebné znalosti |

---

## Ďalšie zdroje

- [Claude Code dokumentácia](https://docs.anthropic.com/claude-code)
- [Anthropic Discord](https://discord.gg/anthropic) - komunita a podpora
- [GitHub Issues](https://github.com/anthropics/claude-code/issues) - bug reports a feature requests

---

*Posledná aktualizácia: Marec 2026*
