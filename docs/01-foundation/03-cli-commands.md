# CLI Commands

Kompletný prehľad príkazov a ovládania Claude Code.

---

## Spustenie

### Základné spustenie

```bash
# Interaktívny mód
claude

# S promptom
claude "Your question or task"

# So súborom ako vstupom
claude < file.txt
claude "Review this:" < code.ts
```

### Flags

| Flag | Skratka | Popis |
|------|---------|-------|
| `--help` | `-h` | Zobraz pomoc |
| `--version` | `-v` | Zobraz verziu |
| `--model <model>` | `-m` | Vyber model (haiku/sonnet/opus) |
| `--resume` | `-r` | Pokračuj v poslednej session |
| `--non-interactive` | `-n` | Headless mode |
| `--cwd <dir>` | | Pracovný priečinok |
| `--timeout <ms>` | | Timeout v milisekundách |
| `--output <file>` | `-o` | Výstup do súboru |
| `--format <fmt>` | `-f` | Formát výstupu (text/json/markdown) |

---

## Slash Commands

### Session Management

| Command | Popis |
|---------|-------|
| `/help` | Zobraz pomoc |
| `/quit` alebo `/exit` | Ukonči session |
| `/clear` | Vyčisti históriu konverzácie |
| `/reset` | Resetuj session úplne |
| `/compact` | Komprimuj kontext |

### Configuration

| Command | Popis |
|---------|-------|
| `/init` | Vytvor CLAUDE.md v projekte |
| `/config` | Zobraz/uprav konfiguráciu |
| `/model <name>` | Prepni model |
| `/permissions` | Spravuj permissions |

### Navigation

| Command | Popis |
|---------|-------|
| `/cd <path>` | Zmeň pracovný priečinok |
| `/ls` | Zobraz obsah priečinka |
| `/pwd` | Zobraz aktuálny priečinok |

### Memory

| Command | Popis |
|---------|-------|
| `/memory` | Zobraz uložené spomienky |
| `/memory add <text>` | Pridaj novú spomienku |
| `/memory clear` | Vymaž spomienky |
| `/remember <text>` | Rýchle pridanie spomienky |

### Context

| Command | Popis |
|---------|-------|
| `/context` | Zobraz aktuálny kontext |
| `/context add <file>` | Pridaj súbor do kontextu |
| `/context remove <file>` | Odstráň súbor z kontextu |

### Session

| Command | Popis |
|---------|-------|
| `/save` | Ulož aktuálnu session |
| `/load <id>` | Načítaj session |
| `/sessions` | Zobraz všetky sessions |

### Plan Mode

| Command | Popis |
|---------|-------|
| `/plan` | Vstup do plan mode |
| `/plan exit` | Opusti plan mode |
| `/plan approve` | Schváľ plán a implementuj |

### Tools & Debug

| Command | Popis |
|---------|-------|
| `/tools` | Zobraz dostupné nástroje |
| `/debug` | Prepni debug mód |
| `/verbose` | Prepni verbose output |
| `/status` | Zobraz status session |

---

## Keyboard Shortcuts

### V Interactive Mode

| Shortcut | Akcia |
|----------|-------|
| `Ctrl+C` | Preruš aktuálnu operáciu |
| `Ctrl+D` | Ukonči session |
| `Ctrl+L` | Vyčisti obrazovku |
| `Ctrl+R` | Hľadaj v histórii |
| `Up/Down` | Naviguj v histórii |
| `Tab` | Autocomplete |
| `Esc` | Zruš aktuálny vstup |

### Počas Tool Execution

| Shortcut | Akcia |
|----------|-------|
| `y` | Potvrď akciu |
| `n` | Zamietni akciu |
| `a` | Povoľ všetky podobné akcie |
| `e` | Uprav pred potvrdením |
| `?` | Zobraz viac informácií |

---

## Príklady použitia

### Základné

```bash
# Interaktívna session
claude

# Jednoduchá otázka
claude "What is recursion?"

# Code review
claude "Review this file" < src/auth.ts
```

### S modelom

```bash
# Opus pre komplexné úlohy
claude -m opus "Design a scalable auth system"

# Haiku pre rýchle odpovede
claude -m haiku "What does this error mean?"
```

### V skriptoch

```bash
#!/bin/bash

# Review všetkých zmenených súborov
for file in $(git diff --name-only); do
  claude -n "Review: $(cat $file)" > "reviews/$(basename $file).md"
done
```

### CI/CD

```bash
# V GitHub Actions
claude -n --timeout 60000 \
  --format markdown \
  "Review this PR: $(git diff main...HEAD)" \
  > review.md
```

---

## Exit Codes

| Code | Význam |
|------|--------|
| 0 | Úspech |
| 1 | Všeobecná chyba |
| 2 | Neplatné argumenty |
| 3 | API chyba |
| 4 | Timeout |
| 5 | Permission denied |
| 130 | Prerušené (Ctrl+C) |

---

## Output Formats

### Text (default)

```bash
claude -n "Explain this" < code.ts
```

### JSON

```bash
claude -n --format json "List functions" < code.ts

# Output:
{
  "response": "...",
  "tools_used": [...],
  "tokens": {
    "input": 150,
    "output": 200
  }
}
```

### Markdown

```bash
claude -n --format markdown "Generate docs" < code.ts
```

---

## Piping & Redirection

### Input

```bash
# Z súboru
claude -n < prompt.txt

# Z pipe
cat code.ts | claude -n "Review this"
```

### Output

```bash
# Do súboru
claude -n "Generate README" > README.md

# Do oboch (terminal + súbor)
claude -n "Generate docs" | tee docs.md
```

---

## Najčastejšie použité príkazy

```bash
# Začať interaktívnu session
claude

# Vytvoriť CLAUDE.md
/init

# Uložiť do pamäte
/remember User prefers Tailwind

# Pokračovať v session
claude --resume

# Code review
claude "Review this file" < src/component.tsx

# Rýchla otázka s Haiku
claude -m haiku "Fix this error: ..."
```

---

## Cvičenia

### Cvičenie 1: Základné príkazy
```
Vyskúšaj tieto príkazy:
1. claude --version
2. claude /init
3. /help
4. /memory
```

### Cvičenie 2: Code review
```
Použi claude na review súboru:
claude "Review this code" < src/app.ts
```

### Cvičenie 3: Session management
```
Pracuj na úlohe, ulož session pomocou /save,
ukonči a znovu načítaj pomocou claude --resume.
```

---

## Ďalej

[Skills - Core Concept](04-skills-concept.md) - Modulárne znalostné balíčky
