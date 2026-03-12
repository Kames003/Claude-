# Core Concepts - Skills

---

## Predpoklady

Pred citanim tohto modulu by ste mali mat:
- Zakladnu znalost Claude Code ([Uvod](01-uvod.md))
- Nainstalovany Claude Code CLI

---

## Co su Skills?

**Skills** sú task-špecifické znalostné moduly uložené v `.claude/skills/` priečinkoch. Načítavajú sa kontextovo - len keď sú relevantné pre aktuálnu úlohu.

## Skills vs CLAUDE.md

| Aspekt | CLAUDE.md | Skills |
|--------|-----------|--------|
| **Načítanie** | Vždy, v každej konverzácii | Kontextovo, len keď sú relevantné |
| **Umiestnenie** | Root projektu | `.claude/skills/<nazov>/` |
| **Účel** | Všeobecné pravidlá projektu | Špecifické úlohy a workflows |
| **Veľkosť** | Stručné, základné info | Môže byť detailnejšie |

## Kedy použiť čo?

### Použite CLAUDE.md pre:
- Všeobecné pravidlá projektu
- Import konvencie
- Coding standards
- Informácie relevantné pre každú konverzáciu

### Použite Skills pre:
- Špecifické workflows (deployment, testing)
- Procesy ktoré sa používajú občas
- Komplexné postupy s viacerými krokmi
- Znalosti zdieľané naprieč projektami

## Pravidlo 500 riadkov

> **Best Practice:** "Keep SKILL.md under 500 lines. If you're exceeding that, consider whether content should be split into separate reference files."

Ak skill presahuje 500 riadkov:
- Hlavný `SKILL.md` obsahuje prehľad
- Detaily presunúť do `references/` priečinku

---

## Dalej

[Prva Session](03-prva-session.md) - Hands-on praca s realnym kodom
