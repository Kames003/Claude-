# Memory & Persistence

Ako Claude Code ukladá a používa informácie medzi sessions, vrátane integrácie s hooks.

---

## Typy pamate

Claude Code ma tri urovne pamate:

```
┌─────────────────────────────────────────────────────────────┐
│  Session Memory (kratkodoba)                                │
│  - Aktualny context konverzacie                             │
│  - Stratena po ukonceni session                             │
├─────────────────────────────────────────────────────────────┤
│  Project Memory (strednodoba)                               │
│  - CLAUDE.md a projektove nastavenia                        │
│  - Perzistentna v ramci projektu                            │
├─────────────────────────────────────────────────────────────┤
│  Long-term Memory (dlhodoba)                                │
│  - ~/.claude/memory.json                                    │
│  - Perzistentna napriec vsetkymi projektami                 │
└─────────────────────────────────────────────────────────────┘
```

---

## Session Memory

### Kontext konverzacie

Pocas session Claude pamata:
- Vsetky spravy v konverzacii
- Precitane subory
- Vykonane operacie
- Chyby a ich riesenia

### Limity

```
┌───────────────────────────────────────────────┐
│  Context Window: ~200K tokens                 │
│                                               │
│  ████████████████░░░░░░░░░░░  65% used        │
│                                               │
│  Auto-compact at 80%                          │
└───────────────────────────────────────────────┘
```

### Kompakcia

Ked sa kontext zaplni:

```bash
# Manualna kompakcia
/compact

# Automaticka kompakcia (v settings)
{
  "context": {
    "autoCompact": true,
    "compactThreshold": 0.8
  }
}
```

### Co sa strati pri kompakcii

- Detaily starsich sprav
- Presny obsah precitanych suborov
- Medzivysledky

### Co zostane

- Klucove rozhodnutia
- Aktualne ulohy
- Dolezite kontextove informacie

---

## Project Memory (CLAUDE.md)

### Vytvorenie

```bash
claude /init
```

### Struktura

```markdown
# My Project

## Overview
E-commerce platforma s React frontendom.

## Tech Stack
- React 18 + TypeScript
- Node.js + Express backend
- PostgreSQL database

## Key Patterns
- Use functional components
- State management with Zustand
- API calls via React Query

## Important Context
- Auth uses JWT tokens
- All API endpoints require authentication
- Tests must pass before PR merge

## File Locations
- Components: src/components/
- API routes: src/api/
- Types: src/types/
```

### Best Practices

| Do | Don't |
|-----|--------|
| Strucne a relevantne | Dlhe opisy |
| Aktualne informacie | Zastarale udaje |
| Technicke detaily | Osobne poznamky |
| Jasne konvencie | Vague guidelines |

---

## Long-term Memory

### Ako funguje

```bash
# Pridaj spomienku
/remember User prefers Tailwind over CSS-in-JS

# Alebo
/memory add Always use pnpm in this workspace
```

### Ulozenie

```json
// ~/.claude/memory.json
{
  "memories": [
    {
      "id": "mem_abc123",
      "content": "User prefers Tailwind over CSS-in-JS",
      "created": "2024-01-15T10:00:00Z",
      "scope": "global"
    },
    {
      "id": "mem_def456",
      "content": "Always use pnpm in this workspace",
      "created": "2024-01-16T14:30:00Z",
      "scope": "project",
      "project": "/home/user/my-project"
    }
  ]
}
```

### Scope typy

| Scope | Popis | Pouzitie |
|-------|-------|----------|
| `global` | Vsetky projekty | Vseobecne preferencie |
| `project` | Konkretny projekt | Projektove specifika |
| `session` | Aktualna session | Docasne info |

### Sprava pamate

```bash
# Zobraz vsetky spomienky
/memory

# Pridaj novu
/memory add <text>

# Odstran konkretnu
/memory remove <id>

# Vymaz vsetky
/memory clear
```

---

## Sessions

### Ukladanie sessions

```bash
# Uloz aktualnu session
/save

# Uloz s nazvom
/save my-feature-work
```

### Nacitanie sessions

```bash
# Zobraz vsetky sessions
/sessions

# Nacitaj konkretnu
/load <session-id>

# Pokracuj v poslednej
claude --resume
# alebo
claude -r
```

### Session data

```
~/.claude/sessions/
├── session_abc123.json
├── session_def456.json
└── session_ghi789.json
```

Kazda session obsahuje:
- Historiu konverzacie
- Pouzite nastroje
- Kontextove informacie
- Timestamps

---

## Kontinuita prace

### Pattern: Dlhodoba praca

```
1. Start session
   └── claude

2. Pracuj na feature
   └── [multiple interactions]

3. Uloz pred odchodom
   └── /save feature-auth

4. Neskorsie pokracuj
   └── claude --resume
   └── /load feature-auth
```

### Pattern: Projekt handoff

```markdown
# CLAUDE.md

## Current State
Feature: User authentication
Status: 70% complete

## Completed
- Login form
- JWT token generation
- Password hashing

## TODO
- Password reset flow
- Email verification
- Session management

## Last Session Notes
Working on password reset email template.
See src/emails/reset-password.tsx
```

---

## Kontext Management

### Efektivne vyuzitie kontextu

```
Good:
"Look at the auth module in src/lib/auth.ts"

Bad:
"Here is all the code..." [paste 500 lines]
```

### Referencovanie suborov

```
# Claude automaticky cita subory ked su potrebne
"Fix the bug in src/components/Button.tsx"

# Nie je potrebne:
"Here is Button.tsx: [paste code]"
```

### Kontext ciste

```bash
# Ked kontext obsahuje prilis vela nepotrebneho
/clear

# Alebo zacinaj novu session pre novu ulohu
/reset
```

---

## UIGen Priklad

### CLAUDE.md pre UIGen

```markdown
# UIGen

## Project Type
AI-powered UI component generator.

## Architecture
- Next.js 15 with App Router
- React 19 with Server Components
- Prisma ORM with SQLite
- Claude API for AI generation

## Key Files
- `src/lib/file-system.ts` - Virtual file system
- `src/app/api/chat/route.ts` - AI endpoint
- `src/lib/tools/` - AI tool implementations

## Conventions
- Use @/ import alias
- Prefer server components
- Use Tailwind for styling
- Tests in __tests__ folders

## Development
- `npm run dev` - Start dev server
- `npm run test` - Run tests
- `npm run setup` - Initial setup
```

### Memory pre UIGen

```bash
/remember UIGen uses virtual file system, not real FS
/remember All components must support Tailwind classes
/remember Monaco editor for code editing
```

---

## Best Practices

### CLAUDE.md

| Do | Avoid |
|----|-------|
| Aktualizuj pri zmenach | Nechat zastarany |
| Strukurovane sekcie | Neorganizovany text |
| Technicke fakty | Opinie a wishlists |
| Kratke a relevantne | Prilis dlhe |

### Memory

| Do | Avoid |
|----|-------|
| Dolezite preferencie | Trivialne info |
| Opakovane patterny | Jednorazove veci |
| Specificke | Vague |

### Sessions

| Do | Avoid |
|----|-------|
| Ukladaj dlhsiu pracu | Ukladat kazdu session |
| Pouzivaj nazvy | Generic IDs |
| Cistit stare | Hromadit sessions |

---

## Troubleshooting

### Claude "zabudol" nieco

```bash
# Skontroluj CLAUDE.md
cat CLAUDE.md

# Skontroluj memory
/memory

# Pridaj do memory ak je dolezite
/remember <info>
```

### Prilis velky kontext

```bash
# Kompaktuj
/compact

# Alebo zacinaj cisto
/clear
```

### Session sa nenacitava

```bash
# Skontroluj sessions
ls ~/.claude/sessions/

# Manualne nacitaj
/load <session-id>
```

---

## Cvicenia

### Cvicenie 1: CLAUDE.md
```
Vytvor CLAUDE.md pre existujuci projekt
s vsetkymi relevantnymi informaciami.
```

### Cvicenie 2: Memory Management
```
Pouzi /remember pre ulozenie 3 dolezitych
preferencii pre tvoj projekt.
```

### Cvicenie 3: Session Workflow
```
Pracuj na feature, uloz session, zatvor claude,
znovu otvor a pokracuj kde si skoncil.
```

---

---

## Memory Hooks

### Automatické ukladanie do pamäte

```json
// ~/.claude/settings.json
{
  "hooks": {
    "post-response": [
      {
        "command": "echo \"$(date): Session note\" >> ~/.claude/session-log.txt"
      }
    ]
  }
}
```

### Hook pre sledovanie zmien

```json
{
  "hooks": {
    "post-tool-call": [
      {
        "tool": "Edit",
        "command": "echo \"Modified: $FILE_PATH at $(date)\" >> ~/.claude/changes.log"
      }
    ]
  }
}
```

### Integrácia s /remember

```bash
# Po dôležitom rozhodnutí
/remember Rozhodli sme sa použiť Zustand namiesto Redux

# Hook môže automaticky logovať
{
  "hooks": {
    "post-response": [
      {
        "pattern": "/remember*",
        "command": "echo \"Memory saved: $(date)\" >> ~/.claude/memory-audit.log"
      }
    ]
  }
}
```

---

## Ďalej

**Pokračuj na Tier 4: Advanced**

[AI Tool Implementation](../04-advanced/13-ai-tools.md) - Vytváranie vlastných AI nástrojov
