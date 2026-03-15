# Claude Code Workshop

Technická dokumentácia pre interné AI workshopy. Materiály na pomoc kolegom s učením sa Claude Code - od základov po pokročilé techniky.

**Praktický projekt:** UIGen - AI-powered React component generator

---

## Štruktúra dokumentácie

### Tier 1: Foundation (Základy)
*Prvý kontakt s Claude Code*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 01 | [Úvod do Claude Code](01-foundation/01-uvod.md) | Čo je Claude Code, inštalácia, základné použitie | Žiadne |
| 02 | [Skills - Core Concept](01-foundation/02-skills-concept.md) | Modulárne znalostné balíčky | Modul 01 |
| 03 | [Prvá Session](01-foundation/03-prva-session.md) | Hands-on práca s reálnym kódom | Modul 01-02 |

---

### Tier 2: Core (Jadro)
*Základné workflow a nástroje*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 04 | [Štruktúra Skillov](02-core/04-struktura-skillov.md) | Organizácia a písanie SKILL.md | Foundation |
| 05 | [Priorita a Distribúcia](02-core/05-priorita.md) | Hierarchia a zdieľanie skills | Modul 04 |
| 06 | [Skills v Tíme + Pluginy](02-core/10-skills-team-plugins.md) | Enterprise skills, pluginy, bezpečnosť | Modul 04-05 |
| 07 | [Tool Use](02-core/06-tool-use.md) | Nástroje Claude Code a ich použitie | Modul 04-05 |
| 08 | [Limitácie a Troubleshooting](02-core/07-limitacie-troubleshooting.md) | Obmedzenia a riešenie problémov | Modul 04-07 |
| 09 | [Built-in Agents](02-core/08-built-in-agents.md) | Explorer, Plan, Verify agenti | Modul 07 |
| 10 | [Subagents a Kontext](02-core/11-subagents-context.md) | Delegovanie úloh, kontext, formatted fields | Modul 09 |
| 11 | [Git Integration](02-core/09-git-integration.md) | Git workflow s Claude Code | Foundation |

---

### Tier 3: Intermediate (Stredne pokročilí)
*Real-world development patterns*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 12 | [Praktické tipy](03-intermediate/08-prakticke-tipy.md) | Best practices a overené vzory | Core |
| 13 | [TDD a Testing](03-intermediate/09-tdd-testing.md) | Test-driven development s Claude | Modul 12 |
| 14 | [Context a State](03-intermediate/10-context-state.md) | React state management s Claude | Modul 12-13 |
| 15 | [Server Actions a API](03-intermediate/11-server-actions-api.md) | Backend s Next.js | Modul 12-14 |
| 16 | [Model Selection](03-intermediate/12-model-selection.md) | Sonnet vs Opus, optimalizácia | Core |
| 17 | [Memory & Persistence](03-intermediate/13-memory-persistence.md) | CLAUDE.md, sessions, dlhodobá pamäť, hooks | Modul 12 |
| 18 | [Sessions](03-intermediate/14-sessions.md) | Auto-save, export, resume | Modul 17 |

---

### Tier 4: Advanced (Pokročilí)
*Komplexné patterns pre power users*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 19 | [AI Tool Implementation](04-advanced/13-ai-tools.md) | Vytváranie vlastných AI nástrojov | Intermediate |
| 20 | [Streaming](04-advanced/14-streaming.md) | Real-time AI odpovede | Modul 19 |
| 21 | [Code Transformations](04-advanced/15-transformations.md) | JSX/Babel transformácie | Modul 19-20 |
| 22 | [Virtual File System](04-advanced/16-virtual-filesystem.md) | In-memory súborový systém | Modul 19-21 |
| 23 | [MCP Servers](04-advanced/17-mcp-servers.md) | Model Context Protocol integrácia | Intermediate |
| 24 | [Hooks & Automation](04-advanced/18-hooks-automation.md) | Event-driven automatizácia | Core |
| 25 | [Plan Mode](04-advanced/22-plan-mode.md) | Plánovanie komplexných zmien | Modul 24 |
| 26 | [IDE Integrations](04-advanced/19-ide-integrations.md) | VS Code, JetBrains, Vim, Emacs | Foundation |
| 27 | [Working Trees](04-advanced/20-working-trees.md) | Git worktrees pre paralelný vývoj | Modul 11 |
| 28 | [Headless Mode](04-advanced/21-headless-mode.md) | CI/CD a automatizácia | Core |

---

### Tier 5: Mastery (Expert)
*Architektúry enterprise úrovne*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 29 | [Multi-Agent Orchestration](05-mastery/18-multi-agent.md) | Koordinácia viacerých agentov | Advanced |
| 30 | [Performance Optimization](05-mastery/19-performance.md) | Token a cost optimalizácia | Modul 29 |
| 31 | [Security](05-mastery/20-security.md) | Bezpečnostné best practices | Advanced |
| 32 | [Debugging Agentic Failures](05-mastery/21-debugging.md) | Diagnostika komplexných problémov | Modul 29-31 |
| 33 | [Enterprise Deployment](05-mastery/22-enterprise.md) | CI/CD, tímová spolupráca | Mastery |

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
├── Poznám základy, chcem ísť hlbšie
│   └── Preskoč na Tier 2: Core (Modul 04)
│
├── Skúsený vývojár, chcem best practices
│   └── Preskoč na Tier 3: Intermediate (Modul 12)
│
├── Power user, chcem pokročilé featury
│   └── Preskoč na Tier 4: Advanced (Modul 19)
│
└── Architekt / Tech lead
    └── Preskoč na Tier 5: Mastery (Modul 29)
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
| CLAUDE.md | Projektový kontext | 01 |
| Skills | Modulárne znalostné balíčky | 02-06 |
| Subagents | Delegovanie úloh | 10 |
| Plan Mode | Plánovanie komplexných zmien | 25 |
| Tool Use | Read, Write, Edit, Bash, Task | 07 |
| Agents | Explorer, Plan, Verify | 09 |
| Hooks | Event-driven automatizácia | 24 |
| Sessions | Auto-save, export, resume | 18 |
| MCP | Model Context Protocol | 23 |

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

CORE:         contexts/, auth.ts
              State management, autentifikácia (~500 riadkov)

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
| Počet modulov | 33 |
| Počet príloh | 6 |
| Počet príkladov | 290+ |
| Počet cvičení | 80+ |
| UIGen referencie | 55% modulov |

---

*Vytvorené: Marec 2026*

**Created by Tomáš Mucha**
