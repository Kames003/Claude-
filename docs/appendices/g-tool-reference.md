# Tool Use - Ako Claude pracuje s nastrojmi

Kompletny prehlad vsetkych nastrojov a ich pouzitia.

---

## Predpoklady

Pred citanim tohto modulu by ste mali mat:
- Zakladnu znalost Claude Code ([Uvod](../01-foundation/01-uvod.md))
- Pochopenie Skills ([Priorita](05-priorita.md))

---

## Princip fungovania

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│    Vas prompt    │ ──► │   Claude         │ ──► │   Tool call      │
│  "Precitaj       │     │   analyzuje      │     │   Read(main.ts)  │
│   main.ts"       │     │   a rozhodne     │     │                  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
                                                           │
                                                           ▼
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│   Odpoved        │ ◄── │   Claude         │ ◄── │   Tool result    │
│   s vysvetlenim  │     │   interpretuje   │     │   (obsah suboru) │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

Claude **negeneruje kod priamo** - generuje strukturovane poziadavky na nastroje, ktore system vykonava.

---

## Vsetky dostupne nastroje

### Suborove operacie

| Nastroj | Funkcia | Bezne pouzitie |
|---------|---------|----------------|
| **Read** | Citanie suborov | Zobrazenie kodu, konfiguracii |
| **Write** | Zapis novych suborov | Vytvorenie novych komponentov |
| **Edit** | Editacia existujucich | Uprava existujuceho kodu |
| **Glob** | Vyhladavanie podla vzoru | `**/*.tsx`, `src/**/*.test.ts` |
| **Grep** | Vyhladavanie v obsahu | Najdenie referencii, definicii |

### Systemove operacie

| Nastroj | Funkcia | Bezne pouzitie |
|---------|---------|----------------|
| **Bash** | Shell prikazy | `npm install`, `git status`, `pytest` |
| **Task** | Spustenie subagenta | Delegovanie komplexnych uloh |

### Externe zdroje

| Nastroj | Funkcia | Bezne pouzitie |
|---------|---------|----------------|
| **WebFetch** | HTTP poziadavky | Stiahnutie dokumentacie |
| **WebSearch** | Web search | Hladanie aktualnych info |

---

## Detailny prehlad nastrojov

### Read

```
Pouzitie: Citanie obsahu suborov
Vstup: file_path, offset (volitelne), limit (volitelne)
```

**Priklady:**
```
"Precitaj src/components/Button.tsx"
"Ukaz mi prvu triedu v lib/auth.ts"
"Precitaj package.json a vysvetli dependencies"
```

**Kedy pouzit:**
- Analyza existujuceho kodu
- Pochopenie struktury
- Review pred editaciou

### Write

```
Pouzitie: Vytvaranie novych suborov
Vstup: file_path, content
```

**Priklady:**
```
"Vytvor novy komponent UserCard.tsx"
"Pridaj .env.example s potrebnymi premennymi"
"Vytvor README.md pre tento projekt"
```

**Kedy pouzit:**
- Nove komponenty
- Konfiguracne subory
- Dokumentacia

### Edit

```
Pouzitie: Modifikacia existujucich suborov
Vstup: file_path, old_string, new_string
```

**Priklady:**
```
"Pridaj novy prop 'disabled' do Button"
"Oprav bug v auth.ts na riadku 45"
"Refaktoruj tuto funkciu na async/await"
```

**Kedy pouzit:**
- Bugfixy
- Pridavanie features
- Refaktoring

### Glob

```
Pouzitie: Najdenie suborov podla vzoru
Vstup: pattern, path (volitelne)
```

**Priklady vzorov:**
```
**/*.tsx          # Vsetky TSX subory
src/**/*.test.ts  # Testy v src/
**/index.ts       # Vsetky index.ts
*.{js,ts}         # JS a TS v roote
```

**Kedy pouzit:**
- Najdenie vsetkych suborov urciteho typu
- Hladanie konfiguracie
- Analyza struktury projektu

### Grep

```
Pouzitie: Hladanie v obsahu suborov
Vstup: pattern, path (volitelne), options
```

**Priklady:**
```
"Najdi vsetky pouzitia funkcie getUser"
"Kde sa importuje prisma client?"
"Najdi TODO komentare"
```

**Regex priklady:**
```
function\s+\w+      # Vsetky funkcie
import.*from        # Vsetky importy
TODO|FIXME|HACK     # Komentare
```

**Kedy pouzit:**
- Hladanie definicii
- Najdenie referencii
- Code search

### Bash

```
Pouzitie: Spustenie shell prikazov
Vstup: command, timeout (volitelne)
```

**Bezne prikazy:**
```bash
# Package management
npm install
npm run build
npm run test

# Git
git status
git diff
git log --oneline -10

# Testing
pytest
vitest run

# Linting
npm run lint
eslint --fix src/
```

**Kedy pouzit:**
- Build a test
- Git operacie
- System prikazy

### Task

```
Pouzitie: Delegovanie na subagenta
Vstup: prompt, subagent_type, model (volitelne)
```

**Typy subagentov:**
```
- Explorer: Prieskum codebase
- Plan: Planovanie implementacie
- Verify: Verifikacia zmien
- general-purpose: Vseobecny agent
```

**Priklady:**
```
"Pouzi Explorer agenta na analyzu architektury"
"Nech Plan agent navrhne refaktoring"
"Verify agent skontroluje ci zmeny nerozili testy"
```

**Kedy pouzit:**
- Komplexne analyzy
- Parallelne ulohy
- Izolovany kontext

---

## Decision Tree: Vyber nastroja

```
Potrebujem...
│
├── Vidiet obsah suboru?
│   └── Read
│
├── Vytvorit novy subor?
│   └── Write
│
├── Upravit existujuci subor?
│   └── Edit
│
├── Najst subory podla nazvu/vzoru?
│   └── Glob
│
├── Najst text v suboroch?
│   └── Grep
│
├── Spustit prikaz (npm, git, test)?
│   └── Bash
│
├── Delegovat komplexnu ulohu?
│   └── Task
│
├── Stiahnut web stranku?
│   └── WebFetch
│
└── Vyhladat na internete?
    └── WebSearch
```

---

## Allowed-tools v Skills

### Obmedzenie nastrojov

V SKILL.md mozete obmedzit ktore nastroje ma Claude k dispozicii:

```yaml
---
name: read-only-review
description: Use for code review without making changes
allowed-tools: [Read, Glob, Grep]  # Bez Write, Edit, Bash
---
```

### Bezne kombinacie

| Ucel | allowed-tools | Bezpecnost |
|------|---------------|------------|
| **Read-only analyza** | `[Read, Glob, Grep]` | Najvyssia |
| **Code review** | `[Read, Glob, Grep, Bash]` | Vysoka (len testy) |
| **Editacia kodu** | `[Read, Write, Edit, Glob, Grep]` | Stredna |
| **Full development** | `[Read, Write, Edit, Bash, Glob, Grep]` | Nizsia |
| **Multi-agent** | `[Read, Write, Edit, Bash, Glob, Grep, Task]` | Najnizsia |

### Security best practices

```yaml
# Pre code review - bez zapisu
allowed-tools: [Read, Glob, Grep]

# Pre testovanie - len read + bash pre testy
allowed-tools: [Read, Bash, Glob, Grep]

# Pre deployment - specificke prikazy
allowed-tools: [Read, Bash]
# + v SKILL.md definuj presne ktore prikazy
```

---

## UIGen Priklady

### Priklad 1: Analyza VirtualFileSystem

```
Prompt: "Vysvetli ako funguje VirtualFileSystem"

Claude pouzije:
1. Glob("src/**/*file-system*") - najde subory
2. Read("src/lib/file-system.ts") - precita implementaciu
3. Grep("class VirtualFileSystem") - najde definiciu
4. Read("src/lib/__tests__/file-system.test.ts") - precita testy
```

### Priklad 2: Pridanie novej featury

```
Prompt: "Pridaj rename metodu do VirtualFileSystem"

Claude pouzije:
1. Read("src/lib/file-system.ts") - pochopi existujucu strukturu
2. Edit("src/lib/file-system.ts", old, new) - prida metodu
3. Edit("src/lib/__tests__/file-system.test.ts") - prida testy
4. Bash("npm run test") - overi ze testy prechadazju
```

### Priklad 3: Code review

```
Prompt: "Review posledne zmeny v chat komponente"

Claude pouzije:
1. Bash("git diff HEAD~1 -- src/components/chat/") - zmeny
2. Read(zmenene_subory) - precita kontext
3. Grep("TODO|FIXME") - najde problemy
```

---

## Tool chaining patterns

### Pattern 1: Explore → Understand → Modify

```
1. Glob - najdi relevantne subory
2. Read - precitaj a pochop
3. Edit - uprav
4. Bash - testuj
```

### Pattern 2: Search → Analyze → Report

```
1. Grep - najdi vsetky vyskyty
2. Read - precitaj kontext
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

## Cvicenia

### Cvicenie 1: Tool Selection
```
Pre kazdy scenar urcite ktory nastroj pouzit:
1. Najst vsetky React komponenty
2. Pridat novu funkciu
3. Spustit testy
4. Najst kde sa volala funkcia
```

### Cvicenie 2: Tool Chaining
```
Navrhnite sekvenciu nastrojov pre:
"Najdi vsetky TODO komentare a vytvor report"
```

### Cvicenie 3: Security Skills
```
Vytvorte skill s obmedzenymi allowed-tools
pre bezpecny code review.
```

---

## Dalej

[Limitacie a Troubleshooting](07-limitacie-troubleshooting.md) - Dolezite obmedzenia
