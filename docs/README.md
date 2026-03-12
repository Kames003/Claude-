# Claude Code Workshop

Technicka dokumentacia pre interne AI workshopy. Materialy na pomoc kolegom s ucenim sa Claude Code - od zakladov po pokrocile techniky.

**Prakticky projekt:** UIGen - AI-powered React component generator

---

## Struktura dokumentacie

### Tier 1: Foundation (Zaklady)
*Prvy kontakt s Claude Code*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 01 | [Uvod do Claude Code](01-foundation/01-uvod.md) | Co je Claude Code, instalacia, zakladne pouzitie | Ziadne |
| 02 | [Skills - Core Concept](01-foundation/02-skills-concept.md) | Modularne znalostne balicky | Modul 01 |
| 03 | [Prva Session](01-foundation/03-prva-session.md) | Hands-on praca s realnym kodom | Modul 01-02 |
| 04 | [Plan Mode](01-foundation/04-plan-mode.md) | Planovanie komplexnych zmien | Modul 01-03 |

---

### Tier 2: Core (Jadro)
*Zakladne workflow a nastroje*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 05 | [Struktura Skillov](02-core/04-struktura-skillov.md) | Organizacia a pisanie SKILL.md | Foundation |
| 06 | [Priorita a Distribucia](02-core/05-priorita.md) | Hierarchia a zdielanie skills | Modul 05 |
| 07 | [Tool Use](02-core/06-tool-use.md) | Nastroje Claude Code a ich pouzitie | Modul 05-06 |
| 08 | [Limitacie a Troubleshooting](02-core/07-limitacie-troubleshooting.md) | Obmedzenia a riesenie problemov | Modul 05-07 |
| 09 | [Built-in Agents](02-core/08-built-in-agents.md) | Explorer, Plan, Verify agenti | Modul 07 |
| 10 | [Git Integration](02-core/09-git-integration.md) | Git workflow s Claude Code | Foundation |

---

### Tier 3: Intermediate (Stredne pokrocili)
*Real-world development patterns*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 11 | [Prakticke tipy](03-intermediate/08-prakticke-tipy.md) | Best practices a overene vzory | Core |
| 12 | [TDD a Testing](03-intermediate/09-tdd-testing.md) | Test-driven development s Claude | Modul 11 |
| 13 | [Context a State](03-intermediate/10-context-state.md) | React state management s Claude | Modul 11-12 |
| 14 | [Server Actions a API](03-intermediate/11-server-actions-api.md) | Backend s Next.js | Modul 11-13 |
| 15 | [Model Selection](03-intermediate/12-model-selection.md) | Sonnet vs Opus, optimalizacia | Core |
| 16 | [Memory & Persistence](03-intermediate/13-memory-persistence.md) | CLAUDE.md, sessions, dlhodoba pamat | Modul 11 |

---

### Tier 4: Advanced (Pokrocili)
*Komplexne patterns pre power users*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 17 | [AI Tool Implementation](04-advanced/13-ai-tools.md) | Vytvaranie vlastnych AI nastrojov | Intermediate |
| 18 | [Streaming](04-advanced/14-streaming.md) | Real-time AI odpovede | Modul 17 |
| 19 | [Code Transformations](04-advanced/15-transformations.md) | JSX/Babel transformacie | Modul 17-18 |
| 20 | [Virtual File System](04-advanced/16-virtual-filesystem.md) | In-memory suborovy system | Modul 17-19 |
| 21 | [MCP Servers](04-advanced/17-mcp-servers.md) | Model Context Protocol integracia | Intermediate |
| 22 | [Hooks & Automation](04-advanced/18-hooks-automation.md) | Event-driven automatizacia | Core |
| 23 | [IDE Integrations](04-advanced/19-ide-integrations.md) | VS Code, JetBrains, Vim, Emacs | Foundation |
| 24 | [Working Trees](04-advanced/20-working-trees.md) | Git worktrees pre paralelny vyvoj | Modul 10 |
| 25 | [Headless Mode](04-advanced/21-headless-mode.md) | CI/CD a automatizacia | Core |

---

### Tier 5: Mastery (Expert)
*Architektury enterprise urovne*

| # | Modul | Popis | Predpoklady |
|---|-------|-------|-------------|
| 26 | [Multi-Agent Orchestration](05-mastery/18-multi-agent.md) | Koordinacia viacerych agentov | Advanced |
| 27 | [Performance Optimization](05-mastery/19-performance.md) | Token a cost optimalizacia | Modul 26 |
| 28 | [Security](05-mastery/20-security.md) | Bezpecnostne best practices | Advanced |
| 29 | [Debugging Agentic Failures](05-mastery/21-debugging.md) | Diagnostika komplexnych problemov | Modul 26-28 |
| 30 | [Enterprise Deployment](05-mastery/22-enterprise.md) | CI/CD, timova spolupraca | Mastery |

---

### Appendices (Prilohy)

| Priloha | Popis |
|---------|-------|
| [A: CLAUDE.md Templates](appendices/a-claude-md-templates.md) | Sablony pre projekty |
| [B: Skill Templates](appendices/b-skill-templates.md) | Hotove skills na pouzitie |
| [C: UIGen Reference](appendices/c-uigen-reference.md) | Kompletna referencia projektu |
| [D: Extended Troubleshooting](appendices/d-troubleshooting-extended.md) | Pokrocile riesenie problemov |
| [E: Commands & CLI Reference](appendices/e-commands-reference.md) | Kompletna CLI referencia |
| [F: Settings Configuration](appendices/f-settings-config.md) | Vsetky nastavenia a konfiguracia |

---

## Kde zacat?

```
Aka je tvoja skusenost s Claude Code?
│
├── Ziadna / Zaciatocnik
│   └── Zacni od Tier 1: Foundation (Modul 01)
│
├── Poznam zaklady, chcem ist hlbsie
│   └── Preskoc na Tier 2: Core (Modul 05)
│
├── Skuseny vyvojar, chcem best practices
│   └── Preskoc na Tier 3: Intermediate (Modul 11)
│
├── Power user, chcem pokrocile featury
│   └── Preskoc na Tier 4: Advanced (Modul 17)
│
└── Architekt / Tech lead
    └── Preskoc na Tier 5: Mastery (Modul 26)
```

---

## Quick Reference

### Najdolezitejsie prikazy

```bash
# Zakladne
claude              # Interaktivny mod
claude "prompt"     # Jednorazovy prompt
claude /init        # Vytvor CLAUDE.md

# Plan Mode
/plan               # Vstup do plan mode
/plan approve       # Schval plan

# Memory
/remember <text>    # Uloz do pamate
/memory             # Zobraz pamat

# Session
/save               # Uloz session
claude --resume     # Pokracuj v session

# Git
"commit this"       # Vytvor commit
"create PR"         # Vytvor pull request
```

### Klucove koncepty

| Koncept | Popis | Modul |
|---------|-------|-------|
| CLAUDE.md | Projektovy kontext | 01 |
| Skills | Modularne znalostne balicky | 02 |
| Plan Mode | Planovanie komplexnych zmien | 04 |
| Tool Use | Read, Write, Edit, Bash, Task | 07 |
| Agents | Explorer, Plan, Verify | 09 |
| Hooks | Event-driven automatizacia | 22 |
| MCP | Model Context Protocol | 21 |

---

## Prakticky projekt: UIGen

Dokumentacia pouziva UIGen ako prakticky priklad:

- **Next.js 15** + React 19 + TypeScript
- **Prisma** + SQLite databaza
- **Anthropic Claude** AI integracia s tool use
- **Virtual File System** pre in-memory operacie
- **Vitest** + React Testing Library

### Komplexnost podla Tier

```
FOUNDATION:   button.tsx, MessageInput.tsx
              Jednoduche komponenty (~110 riadkov)

CORE:         contexts/, auth.ts
              State management, autentifikacia (~500 riadkov)

INTERMEDIATE: testy, hooks
              Testing patterns (~500 riadkov)

ADVANCED:     api/chat, tools/, transformer
              AI integracia (~500 riadkov)

MASTERY:      file-system.ts + komplexne testy
              VFS implementacia (~1200 riadkov)
```

---

## Externe zdroje

- [Claude Code dokumentacia](https://docs.anthropic.com/claude-code)
- [Anthropic Discord](https://discord.gg/anthropic)
- [GitHub Issues](https://github.com/anthropics/claude-code/issues)

---

## Statistiky

| Metrika | Hodnota |
|---------|---------|
| Pocet modulov | 30 |
| Pocet priloh | 6 |
| Pocet prikladov | 280+ |
| Pocet cviceni | 75 |
| UIGen referencie | 55% modulov |

---

*Vytvorene: Marec 2026*

**Created by Tomáš Mucha**
