# Best Practices

Overené vzory a zlaté pravidlá pre prácu so Skills a Agents.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](01-struktura-skillov.md))
- Znalosť Skill Templates ([Skill Templates](04-skill-templates.md))

---

## Organizácia skills v tíme

### Odporúčaná štruktúra pre väčšie projekty

```
.claude/skills/
├── code-review/          # Review workflow
│   ├── SKILL.md
│   └── references/
│       └── checklist.md
├── deployment/           # CI/CD a deployment
│   ├── SKILL.md
│   └── scripts/
│       └── deploy.sh
├── testing/              # Testovacie konvencie
│   ├── SKILL.md
│   └── references/
│       └── patterns.md
├── api-design/           # API štandardy
│   └── SKILL.md
└── onboarding/           # Pre nových členov tímu
    ├── SKILL.md
    └── references/
        ├── setup.md
        └── conventions.md
```

### Naming conventions

| Typ | Konvencia | Príklad |
|-----|-----------|---------|
| Skill folder | kebab-case | `code-review/` |
| SKILL.md | VEĽKÝMI | `SKILL.md` |
| References | kebab-case.md | `api-conventions.md` |
| Scripts | kebab-case.sh/py | `deploy-staging.sh` |

---

## Písanie efektívnych descriptions

### Zlaté pravidlá

1. **Buďte konkrétni** - uveďte presné akcie a kľúčové slová
2. **Myslite ako používateľ** - aké frázy budú písať?
3. **Vyhýbajte sa prekrývaniu** - každý skill má unikátnu doménu
4. **Pridajte negatívne vymedzenie** - kedy skill NEPOUŽÍVAŤ

### Od zlého k vynikajúcemu

**Zlý:**
```yaml
description: Skill for testing
```
Problém: Aktivuje sa na všetko čo obsahuje "test"

**Lepší:**
```yaml
description: Use when writing tests for the application
```
Problém: Stále všeobecný

**Dobrý:**
```yaml
description: Use when writing unit tests, integration tests,
running pytest, jest, vitest, or when user mentions "test",
"testing", "TDD", or "coverage"
```
Dobre: Konkrétne akcie + nástroje + trigger slová

**Vynikajúci:**
```yaml
description: Use when writing or debugging unit tests and
integration tests for Python code. Triggers on "pytest",
"unittest", "test_", "coverage", "TDD", "fixture", "mock".
NOT for E2E/browser tests (use e2e-testing skill instead).
```
Výborne: Konkrétne + triggery + negatívne vymedzenie

### Template pre description

```yaml
description: Use when [AKCIA] for [KONTEXT].
Triggers on "[KEYWORD1]", "[KEYWORD2]", "[KEYWORD3]".
NOT for [VÝNIMKY] (use [INÝ_SKILL] instead).
```

---

## Decision Tree: CLAUDE.md vs Skills

```
Potrebujem uložiť informáciu pre Claude?
│
├── Je potrebná v KAŽDEJ konverzácii?
│   │
│   ├── ÁNO → Je to < 50 riadkov?
│   │   │
│   │   ├── ÁNO → CLAUDE.md
│   │   │
│   │   └── NIE → CLAUDE.md (stručne) + link na docs
│   │
│   └── NIE → Pokračuj ďalej...
│
├── Je to komplexný workflow s viacerými krokmi?
│   │
│   ├── ÁNO → Skill s references/
│   │
│   └── NIE → Pokračuj ďalej...
│
├── Potrebujem obmedziť tools?
│   │
│   ├── ÁNO → Skill (kvôli allowed-tools)
│   │
│   └── NIE → Pokračuj ďalej...
│
├── Je to doménovo špecifické (testing, deploy...)?
│   │
│   ├── ÁNO → Skill
│   │
│   └── NIE → CLAUDE.md
│
└── Ak pochybuješ → CLAUDE.md (jednoduchšie na začiatok)
```

---

## Verzionovanie skills

### Prečo verzionovanie

Skills sú kód - zaslúžia rovnaké praktiky:

| Praktika | Ako aplikovať |
|----------|---------------|
| Code review | PR pre zmeny v skills |
| Semantic commits | `feat(skills): add deployment skill` |
| Changelog | Dokumentuj zmeny |
| Branching | Experimentuj na branchoch |

### Commit message konvencie

```bash
# Nová feature
feat(skills): add deployment skill for AWS

# Bugfix
fix(skills): correct trigger words in testing skill

# Dokumentácia
docs(skills): update API conventions reference

# Refaktoring
refactor(skills): split large skill into modules
```

### Changelog pattern

```markdown
# skills/deployment/CHANGELOG.md

## [1.2.0] - 2024-01-15
### Added
- Multi-region deployment support
- Rollback procedures

## [1.1.0] - 2024-01-10
### Changed
- Updated staging environment config
### Fixed
- Timeout issues in health checks

## [1.0.0] - 2024-01-01
### Added
- Initial deployment skill
```

---

## Testovanie skills

### Checklist pred commitom

```markdown
## Skill Testing Checklist

### Aktivácia
- [ ] Skill sa aktivuje na očakávané frázy
- [ ] Skill sa NEaktivuje na nerelevantné frázy
- [ ] Neprekrýva sa s inými skills

### Obsah
- [ ] Informácie sú aktuálne
- [ ] Príklady fungujú
- [ ] Links sú validné

### Tools
- [ ] allowed-tools sú dostatočné
- [ ] allowed-tools nie sú príliš široké
- [ ] Testované s reálnymi úlohami

### Formát
- [ ] SKILL.md má správny frontmatter
- [ ] Description je dostatočne špecifický
- [ ] Štruktúra je prehľadná
```

### Testovanie aktivácie

```
1. Spusti claude v projekte
2. Skús rôzne frázy:
   - "write a test" → mal by sa aktivovať testing skill
   - "deploy to prod" → mal by sa aktivovať deployment skill
   - "explain this code" → žiadny špeciálny skill
3. Over že sa aktivoval správny skill
4. Over že obsah je relevantný
```

---

## Časté chyby a riešenia

| Chyba | Problém | Riešenie |
|-------|---------|----------|
| Skill sa neaktivuje | Vague description | Pridaj konkrétne trigger slová |
| Skill sa aktivuje vždy | Príliš všeobecný description | Zúž doménu, pridaj "NOT for..." |
| Konflikt skills | Prekrývajúce sa descriptions | Jasné vymedzenie domén |
| Chyba allowed-tools | Claude nemôže vykonať akciu | Pridaj potrebné tools |
| Pomalé načítavanie | Príliš veľký SKILL.md | Použi references/ |
| Zastaralý obsah | Skill neodráža aktuálny stav | Pravidelné review + changelog |

---

## Produktivitné tipy

### 1. Začínajte jednoducho

```
1. Vytvor CLAUDE.md pre projekt
2. Použi Claude Code niekoľko dní
3. Identifikuj opakované patterny
4. Vytvor skills pre tieto patterny
```

### 2. Iterujte na descriptions

```
1. Začni s jednoduchým description
2. Sleduj kedy sa skill aktivuje (a kedy nie)
3. Pridávaj trigger slová postupne
4. Pridaj negatívne vymedzenie ak treba
```

### 3. Zdieľaj v tíme

```
1. Skills commituj do repo
2. Code review pre zmeny
3. Dokumentuj v CHANGELOG
4. Onboarding docs pre nových
```

### 4. Meranie efektivity

| Metrika | Ako merať |
|---------|-----------|
| Aktivácia | Sleduj koľkokrát sa skill aktivoval |
| Úspešnosť | Boli výsledky relevantné? |
| Rýchlosť | Zrýchlil sa workflow? |
| Feedback | Zbieraj feedback od tímu |

---

## Zhrnutie

1. **Skills** sú kontextové znalostné moduly v `.claude/skills/`
2. **Description** je kritický - určuje aktiváciu
3. **Subagenti nededia** skills automaticky
4. **Organizujte** skills podľa domén
5. **Verzionujte** ako kód
6. **Testujte** pred commitom

---

## Ďalej

[Built-in Agents](06-built-in-agents.md) - Explorer, Plan, Verify agenti
