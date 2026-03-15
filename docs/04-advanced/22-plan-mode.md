# Plan Mode

Kritická funkcia pre plánovanie komplexných zmien pred implementáciou.

---

## Predpoklady

Pred použitím Plan Mode by ste mali mať:
- Skúsenosti s Claude Code základmi ([Foundation](../01-foundation/01-uvod.md))
- Pochopenie Tool Use ([Tool Use](../02-core/06-tool-use.md))
- Znalosť Hooks & Automation ([Hooks](18-hooks-automation.md))

---

## Čo je Plan Mode?

Plan Mode je špeciálny režim Claude Code kde:
- Claude **nesmie** robiť žiadne zmeny
- Môže len čítať a analyzovať kód
- Vytvára detailný plán implementácie
- Používateľ schvaľuje plán pred vykonaním

```
┌─────────────────────────────────────────────────────────────┐
│  PLAN MODE - Read-Only Analysis                             │
│                                                             │
│  ✓ Read, Glob, Grep, WebSearch, WebFetch                   │
│  ✗ Write, Edit, Bash (ZAKÁZANÉ)                            │
└─────────────────────────────────────────────────────────────┘
```

---

## Token Consumption

**Dôležité:** Plan Mode je náročnejší na tokeny:

```
┌─────────────────────────────────────────────────────────────┐
│  Plan Mode vs Normal Mode - Token Usage                     │
│                                                             │
│  Normal Mode:  ████░░░░░░  ~1x baseline                     │
│  Plan Mode:    ████████████████████████████  ~7x baseline   │
│                                                             │
│  Dôvod: Rozsiahla analýza + plán + iterácie                │
└─────────────────────────────────────────────────────────────┘
```

Preto používajte Plan Mode strategicky pre komplexné úlohy.

---

## Kedy použiť Plan Mode?

| Situácia | Použiť Plan Mode? | Dôvod |
|----------|-------------------|-------|
| Nová feature s viacerými súbormi | Áno | Potrebná koordinácia |
| Veľký refaktoring | Áno | Riziko regresií |
| Architektonické zmeny | Áno | Dlhodobý dopad |
| Nejasné požiadavky | Áno | Validácia pred implementáciou |
| Jednoduchý bug fix | Nie | Overhead sa neoplatí |
| Pridanie importu | Nie | Triviálna zmena |
| Formátovanie kódu | Nie | Automatizovateľné |

---

## Aktivácia Plan Mode

### Automaticky

Claude Code automaticky vstúpi do Plan Mode keď:
- Úloha je komplexná
- Vyžaduje zmeny vo viacerých súboroch
- Architektonické rozhodnutia sú potrebné

### Manuálne

```
Vstúp do plan mode a navrhni ako implementovať [feature].
```

Alebo použite príkaz:
```
/plan Implementuj user authentication
```

---

## Plan Mode Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                     PLAN MODE                                │
├─────────────────────────────────────────────────────────────┤
│  1. EXPLORE     Claude číta a analyzuje kód                 │
│       ↓                                                      │
│  2. DESIGN      Claude navrhuje riešenie                    │
│       ↓                                                      │
│  3. PLAN FILE   Claude píše plán do súboru                  │
│       ↓                                                      │
│  4. REVIEW      Používateľ kontroluje plán                  │
│       ↓                                                      │
│  5. APPROVE     Používateľ schvaľuje / upravuje             │
│       ↓                                                      │
│  6. EXIT        Claude opúšťa Plan Mode                     │
│       ↓                                                      │
│  7. IMPLEMENT   Claude implementuje podľa plánu             │
└─────────────────────────────────────────────────────────────┘
```

---

## Príklad: Plan Mode Session

### 1. Vstup do Plan Mode

```
User: Potrebujem pridať dark mode do aplikácie. Použi plan mode.

Claude: Vstupujem do Plan Mode. Budem analyzovať kód a vytvorím
implementačný plán.

[Claude používa Read, Glob, Grep - žiadne Write/Edit]
```

### 2. Analýza

```
Claude: Analyzujem súčasnú štruktúru...

Našiel som:
- Tailwind konfigurácia v tailwind.config.js
- Komponenty v src/components/
- Žiadny existujúci theme systém
- 45 komponentov používa hardcoded farby
```

### 3. Plán

```
Claude: Tu je môj implementačný plán:

## Dark Mode Implementation Plan

### Fáza 1: Infraštruktúra
1. Vytvoriť ThemeContext v src/lib/contexts/theme-context.tsx
2. Pridať ThemeProvider do src/app/layout.tsx
3. Aktualizovať tailwind.config.js pre dark mode

### Fáza 2: Komponenty
4. Aktualizovať Button.tsx - pridať dark: varianty
5. Aktualizovať Input.tsx - dark mode podporu
6. ... (ďalších 43 komponentov)

### Fáza 3: Persistencia
7. Uložiť preferenciu do localStorage
8. Pridať toggle do HeaderActions.tsx

### Odhad: 45 súborov, ~200 zmien

Súhlasíš s týmto plánom?
```

### 4. Schválenie

```
User: Áno, pokračuj.

Claude: Opúšťam Plan Mode a začínam implementáciu...

[Claude teraz môže používať Write/Edit]
```

---

## Plan File

Claude ukladá plán do súboru:

```
~/.claude/plans/[unique-id].md
```

Obsah:
```markdown
# Implementation Plan: Dark Mode

## Overview
Adding dark mode support to the application.

## Files to Modify
- tailwind.config.js
- src/app/layout.tsx
- src/lib/contexts/theme-context.tsx (new)
- src/components/ui/button.tsx
- ...

## Steps
1. [ ] Create ThemeContext
2. [ ] Update Tailwind config
3. [ ] Add ThemeProvider
4. [ ] Update components
5. [ ] Add toggle UI

## Risks
- Breaking existing styles
- SSR hydration mismatch

## Verification
- Visual check in both modes
- Check localStorage persistence
- Test SSR behavior
```

---

## Allowed Tools v Plan Mode

| Tool | Povolený? | Účel |
|------|-----------|------|
| Read | Áno | Čítanie súborov |
| Glob | Áno | Vyhľadávanie súborov |
| Grep | Áno | Vyhľadávanie v obsahu |
| WebSearch | Áno | Hľadanie informácií |
| WebFetch | Áno | Čítanie dokumentácie |
| **Write** | **NIE** | Zakázané v Plan Mode |
| **Edit** | **NIE** | Zakázané v Plan Mode |
| **Bash** | **NIE** | Zakázané v Plan Mode |

---

## Exit Plan Mode

### Automaticky

Claude automaticky opustí Plan Mode keď:
- Používateľ schváli plán
- Používateľ povie "pokračuj" / "implementuj"

### Manuálne

```
User: Opusti plan mode.
```

Alebo:
```
/exit-plan
```

---

## Best Practices

### 1. Požadujte Plan Mode pre veľké zmeny

```
# Dobré
"Vstúp do plan mode a navrhni refaktoring auth systému"

# Riskantné
"Refaktoruj auth systém" (Claude môže začať priamo)
```

### 2. Kontrolujte plán detailne

- Sú všetky potrebné súbory zahrnuté?
- Sú kroky v správnom poradí?
- Sú riziká identifikované?

### 3. Upravte plán ak treba

```
User: Plán vyzerá dobre, ale chcem začať s komponentmi,
nie s kontextom. Uprav poradie.
```

### 4. Rozdeľte veľké plány

```
User: Toto je príliš veľa. Rozdeľ na 3 menšie fázy,
každú schválim osobitne.
```

---

## Integrácia s Hooks

Plan Mode sa dá kombinovať s hooks pre automatizáciu:

```json
// ~/.claude/settings.json
{
  "hooks": {
    "planApproved": {
      "command": "echo 'Plan approved at $(date)' >> ~/.claude/plan-log.txt"
    }
  }
}
```

---

## UIGen Príklad

### Plan Mode pre VFS Refaktoring

```
User: Použi plan mode a navrhni rozšírenie VirtualFileSystem
o podporu symlinkov.

Claude: Vstupujem do Plan Mode...

[Analýza src/lib/file-system.ts]

Plán:
1. Pridať SymlinkNode typ do FileSystemNode union
2. Implementovať createSymlink() metódu
3. Upraviť readFile() pre sledovanie symlinkov
4. Pridať resolveSymlink() helper
5. Aktualizovať serializáciu pre DB
6. Napísať testy pre symlink scenáre

Riziká:
- Cyklické symlinky
- Serializácia/deserializácia komplexity

Súhlasíš?
```

---

## Cvičenia

### Cvičenie 1: Basic Plan
```
Vstúp do plan mode a navrhni pridanie pagination
do zoznamu projektov v UIGen.
```

### Cvičenie 2: Complex Plan
```
Použi plan mode pre návrh real-time collaboration
feature kde viacerí používatelia môžu editovať
rovnaký projekt súčasne.
```

### Cvičenie 3: Refactoring Plan
```
Vytvor plán pre migráciu z useState na Zustand
pre state management v UIGen.
```

---

## Ďalej

[IDE Integrations](19-ide-integrations.md) - VS Code, JetBrains, Vim, Emacs integrácie
