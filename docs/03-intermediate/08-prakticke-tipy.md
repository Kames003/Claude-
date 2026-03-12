# Prakticke tipy

Best practices a overene vzory pre pracu s Claude Code.

---

## Predpoklady

Pred citanim tohto modulu by ste mali mat:
- Zvladnute zaklady z Tier 1 (Foundation)
- Pochopenie Tool Use ([Tool Use](../02-core/06-tool-use.md))
- Skusenost s niekolkymi sessions

---

## Organizacia skills v time

### Odporucana struktura pre vacsie projekty

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
├── api-design/           # API standardy
│   └── SKILL.md
└── onboarding/           # Pre novych clenov timu
    ├── SKILL.md
    └── references/
        ├── setup.md
        └── conventions.md
```

### Naming conventions

| Typ | Konvencia | Priklad |
|-----|-----------|---------|
| Skill folder | kebab-case | `code-review/` |
| SKILL.md | VELKYMI | `SKILL.md` |
| References | kebab-case.md | `api-conventions.md` |
| Scripts | kebab-case.sh/py | `deploy-staging.sh` |

---

## Pisanie efektivnych descriptions

### Zlate pravidla

1. **Budte konkretni** - uvedte presne akcie a klucove slova
2. **Myslite ako pouzivatel** - ake frazy budu pisat?
3. **Vyhybajte sa prekryvaniu** - kazdy skill ma unikatnu domenu
4. **Pridajte negativne vymedzenie** - kedy skill NEPOUZIVAT

### Od zleho k vynikajucemu

**Zly:**
```yaml
description: Skill for testing
```
Problem: Aktivuje sa na vsetko co obsahuje "test"

**Lepsi:**
```yaml
description: Use when writing tests for the application
```
Problem: Stale vseobecny

**Dobry:**
```yaml
description: Use when writing unit tests, integration tests,
running pytest, jest, vitest, or when user mentions "test",
"testing", "TDD", or "coverage"
```
Dobre: Konkretne akcie + nastroje + trigger slova

**Vynikajuci:**
```yaml
description: Use when writing or debugging unit tests and
integration tests for Python code. Triggers on "pytest",
"unittest", "test_", "coverage", "TDD", "fixture", "mock".
NOT for E2E/browser tests (use e2e-testing skill instead).
```
Vyborne: Konkretne + triggery + negativne vymedzenie

### Template pre description

```yaml
description: Use when [AKCIA] for [KONTEXT].
Triggers on "[KEYWORD1]", "[KEYWORD2]", "[KEYWORD3]".
NOT for [VYNIMKY] (use [INY_SKILL] instead).
```

---

## Decision Tree: CLAUDE.md vs Skills

```
Potrebujem ulozit informaciu pre Claude?
│
├── Je potrebna v KAZDEJ konverzacii?
│   │
│   ├── ANO → Je to < 50 riadkov?
│   │   │
│   │   ├── ANO → CLAUDE.md
│   │   │
│   │   └── NIE → CLAUDE.md (strucne) + link na docs
│   │
│   └── NIE → Pokracuj dalej...
│
├── Je to komplexny workflow s viacerymi krokmi?
│   │
│   ├── ANO → Skill s references/
│   │
│   └── NIE → Pokracuj dalej...
│
├── Potrebujem obmedzit tools?
│   │
│   ├── ANO → Skill (kvoli allowed-tools)
│   │
│   └── NIE → Pokracuj dalej...
│
├── Je to domenovo specificke (testing, deploy...)?
│   │
│   ├── ANO → Skill
│   │
│   └── NIE → CLAUDE.md
│
└── Ak pochybujes → CLAUDE.md (jednoduchsie na zaciatok)
```

---

## Verzionovanie skills

### Preco verzionovanie

Skills su kod - zasluzuju rovnake praktiky:

| Praktika | Ako aplikovat |
|----------|---------------|
| Code review | PR pre zmeny v skills |
| Semantic commits | `feat(skills): add deployment skill` |
| Changelog | Dokumentuj zmeny |
| Branching | Experimentuj na branchoch |

### Commit message konvencie

```bash
# Nova feature
feat(skills): add deployment skill for AWS

# Bugfix
fix(skills): correct trigger words in testing skill

# Dokumentacia
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

### Aktivacia
- [ ] Skill sa aktivuje na ocakavane frazy
- [ ] Skill sa NEaktivuje na nerelevantne frazy
- [ ] Neprekyva sa s inymi skills

### Obsah
- [ ] Informacie su aktualne
- [ ] Priklady funguju
- [ ] Links su validne

### Tools
- [ ] allowed-tools su dostatocne
- [ ] allowed-tools nie su prilis siroke
- [ ] Testovane s realnymi ulohami

### Format
- [ ] SKILL.md ma spravny frontmatter
- [ ] Description je dostatocne specificky
- [ ] Struktura je prehladna
```

### Testovanie aktivacie

```
1. Spusti claude v projekte
2. Skus rozne frazy:
   - "write a test" → mal by sa aktivovat testing skill
   - "deploy to prod" → mal by sa aktivovat deployment skill
   - "explain this code" → ziadny specialny skill
3. Over ze sa aktivoval spravny skill
4. Over ze obsah je relevantny
```

---

## UIGen Priklady

### Priklad: Component Development Skill

```markdown
---
name: uigen-components
description: Use when creating or modifying React components for UIGen.
Triggers on "component", "button", "form", "UI element", "Tailwind",
"shadcn", "Radix". NOT for API routes (use api-routes skill).
allowed-tools: [Read, Write, Edit, Glob, Grep]
model: sonnet
---

# UIGen Component Development

## Tech Stack
- React 19 + TypeScript strict
- Tailwind CSS v4
- Radix UI primitives (shadcn/ui)
- CVA pre varianty

## File locations
- Components: `src/components/`
- UI primitives: `src/components/ui/`

## Conventions
- Pouzi `@/` import alias
- Exportuj types s komponentom
- Pridaj displayName pre DevTools

## Example
```tsx
import { cn } from '@/lib/utils'

interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  variant?: 'default' | 'bordered'
}

export function Card({ className, variant = 'default', ...props }: CardProps) {
  return (
    <div
      className={cn(
        'rounded-lg bg-card p-4',
        variant === 'bordered' && 'border',
        className
      )}
      {...props}
    />
  )
}

Card.displayName = 'Card'
```
```

### Priklad: VFS Operations Skill

```markdown
---
name: uigen-vfs
description: Use when working with VirtualFileSystem, in-memory files,
or file operations in UIGen. Triggers on "VFS", "virtual file",
"file-system.ts", "createFile", "readFile", "deleteFile".
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

# UIGen Virtual File System

## Key file
`src/lib/file-system.ts` - Main implementation

## Core methods
- `createFile(path, content)` - vytvor subor
- `readFile(path)` - precitaj obsah
- `updateFile(path, content)` - aktualizuj
- `deleteFile(path)` - zmaz
- `listFiles(dir)` - zoznam suborov
- `exists(path)` - existuje?

## Testing
```bash
npm run test -- file-system
```

## Important
- VFS je IN-MEMORY - nezapisuje na disk
- Serializuje sa do DB pre perzistenciu
- Paths zacinaju s `/`
```

---

## Casste chyby a riesenia

| Chyba | Problem | Riesenie |
|-------|---------|----------|
| Skill sa neaktivuje | Vague description | Pridaj konkretne trigger slova |
| Skill sa aktivuje vzdy | Prilis vseobecny description | Zuz domenu, pridaj "NOT for..." |
| Konflikt skills | Prekryvajuce sa descriptions | Jasne vymedzenie domen |
| Chyba allowed-tools | Claude nemoze vykonat akciu | Pridaj potrebne tools |
| Pomale nacitavanie | Prilis velky SKILL.md | Pouzi references/ |
| Zastaraly obsah | Skill neodrazuje aktualny stav | Pravidelne review + changelog |

---

## Produktivitne tipy

### 1. Zacinajte jednoducho

```
1. Vytvor CLAUDE.md pre projekt
2. Pouzi Claude Code niekolko dni
3. Identifikuj opakovane patterny
4. Vytvor skills pre tieto patterny
```

### 2. Iterujte na descriptions

```
1. Zacni s jednoduchym description
2. Sleduj kedy sa skill aktivuje (a kedy nie)
3. Pridavaj trigger slova postupne
4. Pridaj negativne vymedzenie ak treba
```

### 3. Zdielaj v time

```
1. Skills commituj do repo
2. Code review pre zmeny
3. Dokumentuj v CHANGELOG
4. Onboarding docs pre novych
```

### 4. Meranie efektivity

| Metrika | Ako merat |
|---------|-----------|
| Aktivacia | Sleduj kolko krat sa skill aktivoval |
| Uspesnost | Boli vysledky relevantne? |
| Rychlost | Zrychlil sa workflow? |
| Feedback | Zbieraj feedback od timu |

---

## Cvicenia

### Cvicenie 1: Audit existujucich skills
```
Prezrite vase existujuce skills:
1. Maju specificke descriptions?
2. Prekryvaju sa?
3. Maju spravne allowed-tools?
```

### Cvicenie 2: Vytvorte testing skill
```
Vytvorte skill pre vas testing workflow:
1. Analyzujte ake testy pisete
2. Napiste description s triggers
3. Pridajte allowed-tools
4. Otestujte aktivaciu
```

### Cvicenie 3: Team skill audit
```
Pre vas tym:
1. Zozbirajte vsetky skills
2. Identifikujte prekryvy
3. Navrhnite reorganizaciu
4. Vytvorte PR s reorganizaciou
```

---

## Zhrnutie

1. **Skills** su kontextove znalostne moduly v `.claude/skills/`
2. **Description** je kriticky - urcuje aktivaciu
3. **Subagenti nededia** skills automaticky
4. **Organizujte** skills podla domen
5. **Verzionujte** ako kod
6. **Testujte** pred commitom

---

## Dalej

[TDD a Testing](09-tdd-testing.md) - Test-driven development s Claude
