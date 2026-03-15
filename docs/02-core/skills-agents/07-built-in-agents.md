# Built-in Agents

Vstavané agenty Claude Code a ich efektívne využitie.

---

## Prehľad Built-in Agentov

| Agent | Účel | Kedy použiť |
|-------|------|-------------|
| **Explorer** | Prieskum codebase | Hľadanie súborov, pochopenie štruktúry |
| **Plan** | Plánovanie implementácie | Komplexné úlohy vyžadujúce plán |
| **Verify** | Verifikácia zmien | Kontrola po implementácii |

---

## Explorer Agent

### Účel

Rýchly prieskum codebase bez modifikácií.

### Schopnosti

- Vyhľadávanie súborov (Glob)
- Vyhľadávanie v obsahu (Grep)
- Čítanie súborov (Read)
- Analýza štruktúry

### Kedy použiť

```
# Pochopenie projektu
"Preskúmaj codebase a vysvetli architektúru"

# Hľadanie špecifického kódu
"Nájdi všetky miesta kde sa volá API endpoint /users"

# Analýza závislostí
"Zisti ktoré komponenty používajú FileSystemContext"
```

### Príklad použitia

```
User: Použi Explorer agenta na nájdenie všetkých API routes.

Claude: Spúšťam Explorer agenta...

[Explorer Agent]
Našiel som tieto API routes:

1. /api/chat (src/app/api/chat/route.ts)
   - POST: Streaming chat s Claude AI
   - Používa: streamText, strReplaceTool, fileManagerTool

2. /api/auth (implicitne cez server actions)
   - signUp, signIn, signOut v src/actions/index.ts

Žiadne ďalšie API routes v projekte.
```

### Limitations

```
┌────────────────────────────────────────────┐
│  Explorer NEMÔŽE:                          │
│  - Pristupovať ku Skills                   │
│  - Modifikovať súbory                      │
│  - Spúšťať bash príkazy                    │
└────────────────────────────────────────────┘
```

**Workaround:** Poskytnite kontext priamo:

```
# Namiesto spoliehania na skill
"Preskúmaj API routes. Naše API používa Next.js App Router
a routes sú v src/app/api/."
```

---

## Plan Agent

### Účel

Vytváranie implementačných plánov pre komplexné úlohy.

### Schopnosti

- Analýza požiadaviek
- Návrh architektúry
- Identifikácia rizík
- Rozdelenie na kroky

### Kedy použiť

```
# Nová feature
"Naplánuj implementáciu notifikačného systému"

# Veľký refaktoring
"Vytvor plán pre migráciu na TypeScript strict mode"

# Architektonické rozhodnutia
"Navrhni ako implementovať caching layer"
```

### Príklad použitia

```
User: Použi Plan agenta na návrh undo/redo systému.

Claude: Spúšťam Plan agenta...

[Plan Agent]
## Undo/Redo Implementation Plan

### Architektúra
- Command Pattern pre operácie
- History stack pre undo
- Redo stack pre opätovné vykonanie

### Komponenty
1. HistoryManager trieda
2. Command interface
3. Konkrétne commands (CreateFile, EditFile, DeleteFile)

### Integrácia
- Napojenie na FileSystemContext
- UI komponenty (undo/redo buttons)
- Keyboard shortcuts (Ctrl+Z, Ctrl+Y)

### Kroky
1. Vytvoriť HistoryManager
2. Implementovať Command interface
3. Refaktorovať file operácie na commands
4. Pridať UI
5. Pridať keyboard shortcuts

### Riziká
- Memory usage pri veľkej histórii
- Komplexita pri multi-file operáciách
```

### Limitations

```
┌────────────────────────────────────────────┐
│  Plan Agent NEMÔŽE:                        │
│  - Pristupovať ku Skills                   │
│  - Implementovať zmeny (len plánovať)      │
│  - Spúšťať testy                           │
└────────────────────────────────────────────┘
```

---

## Verify Agent

### Účel

Verifikácia že implementácia je správna a kompletná.

### Schopnosti

- Kontrola zmien
- Porovnanie s plánom
- Identifikácia chýb
- Návrh opráv

### Kedy použiť

```
# Po implementácii
"Over že všetky zmeny sú správne"

# Kontrola kvality
"Skontroluj či implementácia spĺňa požiadavky"

# Pre-commit check
"Over že kód je pripravený na commit"
```

### Príklad použitia

```
User: Použi Verify agenta na kontrolu mojich zmien.

Claude: Spúšťam Verify agenta...

[Verify Agent]
## Verification Report

### Zmeny
- src/lib/contexts/theme-context.tsx (nový)
- src/components/ui/button.tsx (upravený)
- src/app/layout.tsx (upravený)

### Kontrola
✓ ThemeContext správne implementovaný
✓ Button má dark mode varianty
✗ Layout chýba ThemeProvider wrapper

### Problémy
1. ThemeProvider nie je pridaný do layout.tsx
   Oprava: Wrap children s <ThemeProvider>

### Odporúčania
- Pridať testy pre ThemeContext
- Dokumentovať dark mode usage
```

### Limitations

```
┌────────────────────────────────────────────┐
│  Verify Agent NEMÔŽE:                      │
│  - Pristupovať ku Skills                   │
│  - Opraviť problémy (len reportovať)       │
│  - Spúšťať testy                           │
└────────────────────────────────────────────┘
```

---

## Kombinácia Agentov

### Workflow: Explore → Plan → Implement → Verify

```
User: Potrebujem pridať search functionality.

# Krok 1: Explorer
"Použi Explorer na analýzu existujúceho kódu"

# Krok 2: Plan
"Teraz použi Plan agenta na návrh implementácie"

# Krok 3: Implement (hlavný agent)
"Implementuj podľa plánu"

# Krok 4: Verify
"Použi Verify agenta na kontrolu"
```

---

## Explicitné spustenie

### Task Tool

```typescript
// Interné použitie v Claude Code
{
  tool: "Task",
  args: {
    subagent_type: "Explore",  // alebo "Plan"
    prompt: "Nájdi všetky React komponenty...",
    description: "Explore components"
  }
}
```

### V konverzácii

```
"Spusti Explorer agenta na [úloha]"
"Použi Plan agenta pre [úloha]"
"Nechaj Verify agenta skontrolovať [zmeny]"
```

---

## Porovnanie s Task Agentom

| Aspekt | Built-in Agents | Task Agent |
|--------|-----------------|------------|
| Skills | Nemajú prístup | Explicitne špecifikované |
| Účel | Špecifické úlohy | Všeobecné úlohy |
| Modifikácie | Zakázané | Povolené |
| Paralelizácia | Nie | Áno |

---

## Cvičenia

### Cvičenie 1: Explorer
```
Použi Explorer agenta na mapovanie všetkých
závislostí medzi kontextami v UIGen.
```

### Cvičenie 2: Plan
```
Použi Plan agenta na návrh implementácie
real-time preview refresh bez full reload.
```

### Cvičenie 3: Verify
```
Po implementácii zmien použi Verify agenta
na kompletnú kontrolu.
```

---

---

## Dalej

[Git Integration](09-git-integration.md) - Git workflow s Claude Code
