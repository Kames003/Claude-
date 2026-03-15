# Claude Code Workshop

Technická dokumentácia pre interné AI workshopy. Materiály na pomoc kolegom s učením sa Claude Code - od základov po pokročilé techniky.

**Praktický projekt:** UIGen - AI-powered React component generator

---

## Štruktúra dokumentácie

### Tier 1: Foundation (Základy)
*Prvý kontakt s Claude Code*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 01 | [Úvod do Claude Code](01-foundation/01-uvod.md) | Čo je Claude Code, základné použitie | Žiadne |
| 02 | [Inštalácia a Setup](01-foundation/02-instalacia-setup.md) | API kľúče, settings.json, CLAUDE.md | Modul 01 |
| 03 | [CLI Commands](01-foundation/03-cli-commands.md) | Slash commands, flags, shortcuts | Modul 01-02 |
| 04 | [Skills - Core Concept](01-foundation/04-skills-concept.md) | Modulárne znalostné balíčky | Modul 01-03 |
| 05 | [Prvá Session](01-foundation/05-prva-session.md) | Hands-on práca s reálnym kódom | Modul 01-04 |

---

### Tier 2: Core (Jadro) - Skills & Agents
*Komplexný prehľad Skills a Agents systému*

#### Skills

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 06 | [Štruktúra Skillov](02-core/skills-agents/01-struktura-skillov.md) | Organizácia a písanie SKILL.md | Foundation |
| 07 | [Priorita a Distribúcia](02-core/skills-agents/02-priorita.md) | Hierarchia skills | Modul 06 |
| 08 | [Skills v Tíme a Firme](02-core/skills-agents/03-skills-team-firma.md) | Enterprise skills, pluginy | Modul 06-07 |
| 09 | [Skill Templates](02-core/skills-agents/05-skill-templates.md) | Hotové skills na použitie | Modul 06-08 |
| 10 | [Best Practices](02-core/skills-agents/06-best-practices.md) | Overené vzory a zlaté pravidlá | Modul 06-09 |

#### Agents

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 11 | [Built-in Agents](02-core/skills-agents/07-built-in-agents.md) | Explorer, Plan, Verify agenti | Modul 10 |
| 12 | [Subagents a Kontext](02-core/skills-agents/08-subagents-context.md) | Delegovanie úloh, formatted fields | Modul 11 |

#### Konfigurácia & Troubleshooting

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 13 | [Allowed Tools](02-core/skills-agents/09-allowed-tools.md) | Bezpečnostná konfigurácia nástrojov | Modul 06 |
| 14 | [Troubleshooting](02-core/skills-agents/10-troubleshooting.md) | Riešenie problémov so skills a agents | Modul 06-13 |
| 15 | [Performance](02-core/skills-agents/11-performance.md) | Optimalizácia výkonu a nákladov | Modul 06-14 |

---

### Tier 3: Intermediate (Stredne pokročilí)
*Real-world development patterns*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 16 | [Praktické tipy](03-intermediate/08-prakticke-tipy.md) | Best practices a overené vzory | Core |
| 17 | [TDD a Testing](03-intermediate/09-tdd-testing.md) | Test-driven development s Claude | Modul 16 |
| 18 | [Context a State](03-intermediate/10-context-state.md) | React state management s Claude | Modul 16-17 |
| 19 | [Server Actions a API](03-intermediate/11-server-actions-api.md) | Backend s Next.js | Modul 16-18 |
| 20 | [Model Selection](03-intermediate/12-model-selection.md) | Sonnet vs Opus, optimalizácia | Core |
| 21 | [Memory & Persistence](03-intermediate/13-memory-persistence.md) | CLAUDE.md, sessions, dlhodobá pamäť, hooks | Modul 16 |
| 22 | [Sessions](03-intermediate/14-sessions.md) | Auto-save, export, resume | Modul 21 |
| 23 | [Git Integration](02-core/09-git-integration.md) | Git workflow s Claude Code | Foundation |

---

### Tier 4: Advanced (Pokročilí)
*Komplexné patterns pre power users*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 24 | [AI Tool Implementation](04-advanced/13-ai-tools.md) | Vytváranie vlastných AI nástrojov | Intermediate |
| 25 | [Streaming](04-advanced/14-streaming.md) | Real-time AI odpovede | Modul 24 |
| 26 | [Code Transformations](04-advanced/15-transformations.md) | JSX/Babel transformácie | Modul 24-25 |
| 27 | [Virtual File System](04-advanced/16-virtual-filesystem.md) | In-memory súborový systém | Modul 24-26 |
| 28 | [MCP Servers](04-advanced/17-mcp-servers.md) | Model Context Protocol integrácia | Intermediate |
| 29 | [Hooks & Automation](04-advanced/18-hooks-automation.md) | Event-driven automatizácia | Core |
| 30 | [Plan Mode](04-advanced/22-plan-mode.md) | Plánovanie komplexných zmien | Modul 29 |
| 31 | [IDE Integrations](04-advanced/19-ide-integrations.md) | VS Code, JetBrains, Vim, Emacs | Foundation |
| 32 | [Working Trees](04-advanced/20-working-trees.md) | Git worktrees pre paralelný vývoj | Modul 23 |
| 33 | [Headless Mode](04-advanced/21-headless-mode.md) | CI/CD a automatizácia | Core |

---

### Tier 5: Mastery (Expert)
*Architektúry enterprise úrovne*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 34 | [Multi-Agent Orchestration](05-mastery/18-multi-agent.md) | Koordinácia viacerých agentov | Advanced |
| 35 | [Security](05-mastery/20-security.md) | Bezpečnostné best practices | Advanced |
| 36 | [Debugging Agentic Failures](05-mastery/21-debugging.md) | Diagnostika komplexných problémov | Modul 34-35 |
| 37 | [Enterprise Deployment](05-mastery/22-enterprise.md) | CI/CD, tímová spolupráca | Mastery |

---

### Appendices (Prílohy)

| Príloha | Popis |
|---------|-------|
| [A: CLAUDE.md Templates](appendices/a-claude-md-templates.md) | Šablóny pre projekty |
| [B: Skill Templates](appendices/b-skill-templates.md) | Hotové skills na použitie |
| [C: UIGen Reference](appendices/c-uigen-reference.md) | Kompletná referencia projektu |
| [D: Extended Troubleshooting](appendices/d-troubleshooting-extended.md) | Pokročilé riešenie problémov |
| [E: Commands & CLI Reference](appendices/e-commands-reference.md) | Kompletná CLI referencia |
| [F: Settings Configuration](appendices/f-settings-config.md) | Všetky nastavenia a konfigurácia |

---

## Kde začať?

```
Aká je tvoja skúsenosť s Claude Code?
│
├── Žiadna / Začiatočník
│   └── Začni od Tier 1: Foundation (Modul 01)
│
├── Poznám základy, chcem Skills & Agents
│   └── Preskoč na Tier 2: Core (Modul 06)
│
├── Skúsený vývojár, chcem best practices
│   └── Preskoč na Tier 3: Intermediate (Modul 16)
│
├── Power user, chcem pokročilé featury
│   └── Preskoč na Tier 4: Advanced (Modul 24)
│
└── Architekt / Tech lead
    └── Preskoč na Tier 5: Mastery (Modul 34)
```

---

## Quick Reference

### Najdôležitejšie príkazy

```bash
# Základné
claude              # Interaktívny mód
claude "prompt"     # Jednorazový prompt
claude /init        # Vytvor CLAUDE.md

# Plan Mode
/plan               # Vstup do plan mode
/exit-plan          # Opusti plan mode

# Memory
/remember <text>    # Ulož do pamäte
/memory             # Zobraz pamäť

# Session
/export             # Exportuj session
claude --resume     # Pokračuj v session

# Git
"commit this"       # Vytvor commit
"create PR"         # Vytvor pull request
```

### Kľúčové koncepty

| Koncept | Popis | Modul |
|---------|-------|-------|
| CLAUDE.md | Projektový kontext | 01-02 |
| CLI Commands | Slash commands, flags | 03 |
| Skills | Modulárne znalostné balíčky | 04, 06-10 |
| Priorita | Enterprise > Personal > Project > Plugins | 07 |
| Agents | Explorer, Plan, Verify | 11 |
| Subagents | Delegovanie úloh | 12 |
| Allowed Tools | Bezpečnostná konfigurácia | 13 |
| Plan Mode | Plánovanie komplexných zmien | 30 |
| Tool Use | Read, Write, Edit, Bash, Task | 13 |
| Hooks | Event-driven automatizácia | 29 |
| Sessions | Auto-save, export, resume | 22 |
| MCP | Model Context Protocol | 28 |

---

## Core Learning Path: Skills & Agents

Pre úplné pochopenie Skills & Agents systému:

```
01-struktura-skillov.md    → Základná štruktúra SKILL.md
         │
         ▼
02-priorita.md             → Hierarchia a distribúcia
         │
         ▼
03-skills-team-firma.md    → Enterprise a pluginy
         │
         ▼
05-skill-templates.md      → Hotové šablóny na použitie
         │
         ▼
06-best-practices.md       → Overené vzory
         │
         ▼
07-built-in-agents.md      → Explorer, Plan, Verify
         │
         ▼
08-subagents-context.md    → Task tool, delegovanie
         │
         ▼
09-allowed-tools.md        → Bezpečnostná konfigurácia
         │
         ▼
10-troubleshooting.md      → Riešenie problémov
         │
         ▼
11-performance.md          → Optimalizácia nákladov
```

---

## Praktický projekt: UIGen

Dokumentácia používa UIGen ako praktický príklad:

- **Next.js 15** + React 19 + TypeScript
- **Prisma** + SQLite databáza
- **Anthropic Claude** AI integrácia s tool use
- **Virtual File System** pre in-memory operácie
- **Vitest** + React Testing Library

### Komplexnosť podľa Tier

```
FOUNDATION:   button.tsx, MessageInput.tsx
              Jednoduché komponenty (~110 riadkov)

CORE:         skills/, contexts/, auth.ts
              Skills, state management (~500 riadkov)

INTERMEDIATE: testy, hooks
              Testing patterns (~500 riadkov)

ADVANCED:     api/chat, tools/, transformer
              AI integrácia (~500 riadkov)

MASTERY:      file-system.ts + komplexné testy
              VFS implementácia (~1200 riadkov)
```

---

## Externé zdroje

- [Claude Code dokumentácia](https://docs.anthropic.com/claude-code)
- [Anthropic Discord](https://discord.gg/anthropic)
- [GitHub Issues](https://github.com/anthropics/claude-code/issues)

---

## Štatistiky

| Metrika | Hodnota |
|---------|---------|
| Počet modulov | 37 |
| Počet príloh | 6 |
| Počet príkladov | 300+ |
| Počet cvičení | 90+ |
| UIGen referencie | 55% modulov |

---

*Vytvorené: Marec 2026*

**Created by Tomáš Mucha**

