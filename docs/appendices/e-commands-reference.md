# Appendix E: Commands & CLI Reference

Kompletna referencia Claude Code CLI.

---

## Spustenie

### Zakladne spustenie

```bash
# Interaktivny mod
claude

# S promptom
claude "Your question or task"

# S suborom ako vstupom
claude < file.txt
claude "Review this:" < code.ts
```

### Flags

| Flag | Skratka | Popis |
|------|---------|-------|
| `--help` | `-h` | Zobraz pomoc |
| `--version` | `-v` | Zobraz verziu |
| `--non-interactive` | `-n` | Headless mode |
| `--model <model>` | `-m` | Vyber model (haiku/sonnet/opus) |
| `--cwd <dir>` | | Pracovny priecinok |
| `--timeout <ms>` | | Timeout v milisekundach |
| `--output <file>` | `-o` | Vystup do suboru |
| `--format <fmt>` | `-f` | Format vystupu (text/json/markdown) |
| `--resume` | `-r` | Pokracuj v poslednej session |
| `--continue` | `-c` | Pokracuj v konverzacii |

---

## Slash Commands

### Session Management

| Command | Popis |
|---------|-------|
| `/help` | Zobraz pomoc |
| `/quit` alebo `/exit` | Ukonci session |
| `/clear` | Vycisti historiu konverzacie |
| `/reset` | Resetuj session uplne |
| `/compact` | Komprimuj kontext |

### Configuration

| Command | Popis |
|---------|-------|
| `/init` | Vytvor CLAUDE.md v projekte |
| `/config` | Zobraz/uprav konfiguraciu |
| `/model <name>` | Prepni model |
| `/permissions` | Spravuj permissions |

### Navigation

| Command | Popis |
|---------|-------|
| `/cd <path>` | Zmen pracovny priecinok |
| `/ls` | Zobraz obsah priecinka |
| `/pwd` | Zobraz aktualny priecinok |

### Memory

| Command | Popis |
|---------|-------|
| `/memory` | Zobraz ulozene spomienky |
| `/memory add <text>` | Pridaj novu spomienku |
| `/memory clear` | Vymaz spomienky |
| `/remember <text>` | Rychle pridanie spomienky |

### Context

| Command | Popis |
|---------|-------|
| `/context` | Zobraz aktualny kontext |
| `/context add <file>` | Pridaj subor do kontextu |
| `/context remove <file>` | Odstran subor z kontextu |

### Session

| Command | Popis |
|---------|-------|
| `/save` | Uloz aktualnu session |
| `/load <id>` | Nacitaj session |
| `/sessions` | Zobraz vsetky sessions |

### Plan Mode

| Command | Popis |
|---------|-------|
| `/plan` | Vstup do plan mode |
| `/plan exit` | Opusti plan mode |
| `/plan approve` | Schval plan a implementuj |

### Tools & Debug

| Command | Popis |
|---------|-------|
| `/tools` | Zobraz dostupne nastroje |
| `/debug` | Prepni debug mod |
| `/verbose` | Prepni verbose output |
| `/status` | Zobraz status session |

---

## Keyboard Shortcuts

### V Interactive Mode

| Shortcut | Akcia |
|----------|-------|
| `Ctrl+C` | Prerus aktualnu operaciu |
| `Ctrl+D` | Ukonci session |
| `Ctrl+L` | Vycisti obrazovku |
| `Ctrl+R` | Hladaj v historii |
| `Up/Down` | Naviguj v historii |
| `Tab` | Autocomplete |
| `Esc` | Zrus aktualny vstup |

### Pocas Tool Execution

| Shortcut | Akcia |
|----------|-------|
| `y` | Potvrd akciu |
| `n` | Zamietni akciu |
| `a` | Povol vsetky podobne akcie |
| `e` | Uprav pred potvrdenim |
| `?` | Zobraz viac informacii |

---

## Environment Variables

### Autentifikacia

```bash
# API kluc
ANTHROPIC_API_KEY=sk-ant-...

# Alternativny kluc
CLAUDE_API_KEY=sk-ant-...
```

### Konfiguracia

```bash
# Predvoleny model
CLAUDE_MODEL=sonnet

# Pracovny priecinok
CLAUDE_CWD=/path/to/project

# Debug mod
CLAUDE_DEBUG=1

# Verbose output
CLAUDE_VERBOSE=1

# Non-interactive mod
CLAUDE_NON_INTERACTIVE=1

# Hook debug
CLAUDE_HOOK_DEBUG=1
```

### Cesty

```bash
# Konfiguracny priecinok
CLAUDE_CONFIG_DIR=~/.claude

# Cache priecinok
CLAUDE_CACHE_DIR=~/.claude/cache

# Log priecinok
CLAUDE_LOG_DIR=~/.claude/logs
```

---

## Output Formats

### Text (default)

```bash
claude -n "Explain this" < code.ts
# Plain text output
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
# Formatted markdown output
```

---

## Piping & Redirection

### Input

```bash
# Z suboru
claude -n < prompt.txt

# Z pipe
cat code.ts | claude -n "Review this"

# Z heredoc
claude -n << EOF
Review this code for bugs:
$(cat src/auth.ts)
EOF
```

### Output

```bash
# Do suboru
claude -n "Generate README" > README.md

# Append
claude -n "Add section" >> README.md

# Do oboch (terminal + subor)
claude -n "Generate docs" | tee docs.md
```

### Kombinacie

```bash
# Input + output
claude -n < prompt.txt > response.md

# S error handling
claude -n "Generate tests" > tests.ts 2> errors.log
```

---

## Priklady Pouzitia

### Zakladne

```bash
# Interaktivna session
claude

# Jednoducha otazka
claude "What is recursion?"

# Code review
claude "Review this file" < src/auth.ts
```

### S modelom

```bash
# Opus pre komplexne ulohy
claude -m opus "Design a scalable auth system"

# Haiku pre rychle odpovede
claude -m haiku "What does this error mean?"
```

### V scripts

```bash
#!/bin/bash

# Review vsetkych zmenenych suborov
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

| Code | Vyznam |
|------|--------|
| 0 | Uspech |
| 1 | Vseobecna chyba |
| 2 | Neplatne argumenty |
| 3 | API chyba |
| 4 | Timeout |
| 5 | Permission denied |
| 130 | Prerusene (Ctrl+C) |

---

## Troubleshooting

### Bezne problemy

```bash
# API kluc nefunguje
echo $ANTHROPIC_API_KEY  # Skontroluj ci je nastaveny

# Permission errors
claude /permissions  # Skontroluj permissions

# Timeout
claude -n --timeout 120000 "..."  # Zvys timeout

# Debug mod
CLAUDE_DEBUG=1 claude "..."  # Zobraz debug info
```

### Logy

```bash
# Pozri logy
tail -f ~/.claude/logs/claude.log

# Vycisti cache
rm -rf ~/.claude/cache
```

---

[Spat na README](../README.md)
