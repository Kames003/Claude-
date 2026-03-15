# Úvod do Claude Code

Claude Code je CLI nástroj od Anthropic, ktorý umožňuje interaktívnu prácu s kódom priamo z terminálu.

## Čo je Claude Code?

Na rozdiel od webového rozhrania Claude, Claude Code:

- **Má prístup k súborovému systému** - číta a zapisuje súbory priamo
- **Môže spúšťať bash príkazy** - build, test, git operácie
- **Rozumie kontextu celého projektu** - analyzuje štruktúru a závislosti
- **Podporuje Skills** - modulárne znalostné balíčky pre špecifické úlohy

## Základné použitie

```bash
# Spustenie v aktuálnom priečinku
claude

# Spustenie s konkrétnou úlohou
claude "vysvetli mi tento kód"

# Spustenie v špecifickom priečinku
claude --cwd /path/to/project
```

## Kedy použiť Claude Code?

| Situácia | Vhodnosť |
|----------|----------|
| Práca s existujúcim projektom | Ideálne |
| Refaktoring kódu | Ideálne |
| Debugging | Ideálne |
| Code review | Vhodné |
| Učenie sa nového codebase | Vhodné |
| Generovanie kódu od nuly | Možné, ale web UI môže byť jednoduchšie |

---

## Ďalej

[Inštalácia a Setup](02-instalacia-setup.md) - API kľúče, konfigurácia a CLAUDE.md
