# Sessions

Správa sessions v Claude Code - auto-save, export a resume.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Znalosť Memory & Persistence ([Memory](13-memory-persistence.md))
- Skúsenosti s Claude Code sessions

---

## Ako fungujú Sessions

```
┌─────────────────────────────────────────────────────────────┐
│  SESSION LIFECYCLE                                          │
│                                                             │
│  Start ──► Conversation ──► Auto-save ──► Exit             │
│              │                  │                           │
│              ▼                  ▼                           │
│         ~/.claude/         history.jsonl                    │
│         sessions/                                           │
└─────────────────────────────────────────────────────────────┘
```

---

## Auto-save

### Kde sa ukladá história

```
~/.claude/history.jsonl
```

Každá session sa automaticky ukladá do tohto súboru vo formáte JSON Lines.

### Čo sa ukladá

| Dáta | Ukladá sa? |
|------|-----------|
| Conversation history | Áno |
| Tool calls a výsledky | Áno |
| Timestamps | Áno |
| Project context | Áno |
| File contents | Nie (len cesty) |

---

## Export Session

### Príkaz /export

```bash
# Export aktuálnej session
/export

# Export do konkrétneho súboru
/export session-backup.json

# Export vo formáte markdown
/export --format md session-notes.md
```

### Formáty exportu

| Formát | Použitie |
|--------|----------|
| JSON | Kompletné dáta, programatické spracovanie |
| Markdown | Čitateľné poznámky, dokumentácia |
| Text | Jednoduchý plain text |

### Príklad exportovanej session

```json
{
  "sessionId": "session_abc123",
  "startTime": "2024-01-15T10:00:00Z",
  "endTime": "2024-01-15T11:30:00Z",
  "project": "/path/to/project",
  "messages": [
    {
      "role": "user",
      "content": "Pridaj dark mode",
      "timestamp": "2024-01-15T10:00:05Z"
    },
    {
      "role": "assistant",
      "content": "Implementujem dark mode...",
      "timestamp": "2024-01-15T10:00:10Z",
      "tools": ["Read", "Write", "Edit"]
    }
  ]
}
```

---

## Resume Session

### Pokračovanie v poslednej session

```bash
# Resume posledná session
claude --resume

# Skrátená forma
claude -r
```

### Výber konkrétnej session

```bash
# Zoznam sessions
/sessions

# Načítaj konkrétnu
/load <session-id>
```

### Čo sa obnoví

```
┌─────────────────────────────────────────────────────────────┐
│  PRI RESUME SA OBNOVÍ:                                      │
│  ✓ Conversation history                                     │
│  ✓ Kontext projektu                                         │
│  ✓ Predchádzajúce rozhodnutia                              │
│                                                             │
│  NEOBNOVÍ SA:                                               │
│  ✗ Otvorené súbory (nutné znovu načítať)                   │
│  ✗ Background tasks                                         │
│  ✗ Temporary state                                          │
└─────────────────────────────────────────────────────────────┘
```

---

## Session Management

### Zoznam sessions

```bash
/sessions

# Výstup:
# ID              | Started           | Project
# session_abc123  | 2024-01-15 10:00  | /path/to/project
# session_def456  | 2024-01-14 14:30  | /path/to/other
```

### Mazanie sessions

```bash
# Vymaž konkrétnu session
/session delete <session-id>

# Vymaž staré sessions
/session cleanup --older-than 30d
```

---

## Praktické Vzory

### Pattern: Dlhodobá práca na feature

```
Deň 1:
1. claude
2. Pracuj na feature
3. /export feature-auth-day1.md
4. Exit

Deň 2:
1. claude --resume
2. Pokračuj kde si skončil
3. /export feature-auth-day2.md
```

### Pattern: Dokumentácia session

```bash
# Po dokončení dôležitej práce
/export --format md docs/session-$(date +%Y%m%d).md
```

### Pattern: Záloha pred experimentom

```bash
# Pred rizikovými zmenami
/export backup-before-refactor.json

# Po neúspechu
/load backup-before-refactor.json
```

---

## Session Hooks

### Logging session štartu

```json
{
  "hooks": {
    "session-start": [
      {
        "command": "echo \"Session started: $(date)\" >> ~/.claude/session-audit.log"
      }
    ]
  }
}
```

### Auto-export pri ukončení

```json
{
  "hooks": {
    "session-end": [
      {
        "command": "claude export ~/.claude/backups/session-$(date +%Y%m%d-%H%M).json"
      }
    ]
  }
}
```

---

## UIGen Príklad

### Typický workflow

```
# Ráno - začni novú session
claude

# Pracuj na UIGen features
"Pridaj undo/redo do VFS"
[práca...]

# Pred obedom - ulož progres
/export uigen-undo-redo-wip.md

# Po obede - pokračuj
claude --resume

# Večer - finálny export
/export uigen-undo-redo-complete.md
```

---

## Troubleshooting

### Session sa nenačítava

```bash
# Skontroluj či existuje
ls ~/.claude/sessions/

# Skús manuálne načítať
/load <session-id>

# Skontroluj formát
cat ~/.claude/history.jsonl | jq '.' | head
```

### Stratená session

```bash
# História je v history.jsonl
grep "session_id" ~/.claude/history.jsonl

# Exporty ak existujú
ls ~/.claude/exports/
```

---

## Cvičenia

### Cvičenie 1: Export Workflow
```
Vytvor session, uloží ju, ukončí claude,
a znovu načítaj pomocou --resume.
```

### Cvičenie 2: Session Hooks
```
Nakonfiguruj hook pre automatický export
session pri ukončení.
```

### Cvičenie 3: Session Cleanup
```
Vytvor script pre automatické mazanie
sessions starších ako 7 dní.
```

---

## Ďalej

[Model Selection](12-model-selection.md) - Sonnet vs Opus, optimalizácia
