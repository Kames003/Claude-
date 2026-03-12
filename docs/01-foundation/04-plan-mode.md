# Plan Mode

Kritická funkcia pre plánovanie komplexných zmien pred implementáciou.

---

## Čo je Plan Mode?

Plan Mode je špeciálny režim Claude Code kde:
- Claude **nesmie** robiť žiadne zmeny
- Môže len čítať a analyzovať kód
- Vytvára detailný plán implementácie
- Používateľ schvaľuje plán pred vykonaním

---

## Kedy použiť Plan Mode?

| Situácia | Použiť Plan Mode? |
|----------|-------------------|
| Nová feature s viacerými súbormi | Áno |
| Veľký refaktoring | Áno |
| Architektonické zmeny | Áno |
| Nejasné požiadavky | Áno |
| Jednoduchý bug fix | Nie |
| Pridanie importu | Nie |
| Formátovanie kódu | Nie |

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

---

## Dalej

**Pokracuj na Tier 2: Core**

[Struktura Skillov](../02-core/04-struktura-skillov.md) - Ako pisat a organizovat Skills
