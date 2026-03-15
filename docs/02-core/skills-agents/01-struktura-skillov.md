# Struktura Skill-ov

Kompletny navod na pisanie a organizaciu Skills.

---

## Predpoklady

Pred citanim tohto modulu by ste mali mat:
- Zakladnu znalost Claude Code ([Uvod](../01-foundation/01-uvod.md))
- Pochopenie konceptu Skills ([Skills Concept](../01-foundation/02-skills-concept.md))
- Skusenost s prvou session ([Prva Session](../01-foundation/03-prva-session.md))

---

## Adresarova struktura

### Zakladna struktura

```
.claude/skills/<nazov-skillu>/
├── SKILL.md           # Hlavny subor (POVINNY)
├── scripts/           # Spustitelny kod
│   ├── build.sh
│   ├── deploy.py
│   └── test-runner.js
├── references/        # Dokumentacia a detaily
│   ├── api-docs.md
│   ├── conventions.md
│   └── examples.md
└── assets/            # Obrazky, sablony, data
    ├── template.json
    ├── config.yaml
    └── diagram.png
```

### Kedy pouzit jednotlive priecinky

| Priecinok | Ucel | Priklad |
|-----------|------|---------|
| `scripts/` | Automatizovane skripty | Build, deploy, testing |
| `references/` | Detailna dokumentacia | API specs, coding standards |
| `assets/` | Staticke subory | Templates, configs, diagrams |

---

## SKILL.md - Povinne komponenty

### Minimalny SKILL.md

```markdown
---
name: my-skill
description: Use this skill when [specific situation]
---

# My Skill

## Purpose
[Co tento skill robi]

## Usage
[Ako ho pouzit]
```

### Kompletny SKILL.md

```markdown
---
name: api-testing
description: Use this skill when writing or running API tests, testing endpoints, debugging API responses, or when user mentions "API test", "endpoint test", "REST test"
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep]
model: sonnet
---

# API Testing Skill

## Kedy pouzit
- Pisanie testov pre REST/GraphQL endpointy
- Debugging API responses
- Validacia request/response schem
- Load testing a performance testy

## Tech Stack
- pytest s pytest-asyncio
- httpx pre async HTTP
- respx pre mocking

## Testovacie konvencie
- Kazdy endpoint ma vlastny test file
- Fixtures pre auth a test data
- Mock external services

## Prikazy
- `pytest tests/api/` - spusti API testy
- `pytest --cov=src/api` - s coverage reportom
- `pytest -k "test_user"` - konkretne testy

## Struktura testov
```python
# tests/api/test_users.py
import pytest
from httpx import AsyncClient

@pytest.fixture
async def client():
    async with AsyncClient(app=app) as ac:
        yield ac

async def test_get_user(client):
    response = await client.get("/users/1")
    assert response.status_code == 200
    assert "email" in response.json()
```

## Chybove scenare
- 401: Skontroluj auth token
- 422: Validacna chyba - skontroluj schema
- 500: Server error - pozri logy
```

---

## Description - Najdolezitejsia cast

### Preco je description kriticky

```
┌─────────────────────────────────────────────────────────────┐
│  Description = TRIGGER pre aktivaciu skillu                 │
│                                                             │
│  Claude cita description a rozhoduje:                       │
│  "Je tento skill relevantny pre aktualnu ulohu?"            │
└─────────────────────────────────────────────────────────────┘
```

### Anatomia dobreho description

```yaml
description: Use this skill when [AKCIA] [KONTEXT] [TRIGGERY]
```

| Cast | Popis | Priklad |
|------|-------|---------|
| AKCIA | Co uzivatel robi | "writing tests", "deploying" |
| KONTEXT | Kde/kedy | "for API endpoints", "to production" |
| TRIGGERY | Klucove slova | "pytest", "jest", "deploy" |

### Priklady - Od zleho k dobremu

**Zly description:**
```yaml
description: Skill for testing
```
Problem: Prilis vseobecny, aktivuje sa vzdy

**Stredny description:**
```yaml
description: Use when writing tests
```
Problem: Stale vseobecny, chybaju triggery

**Dobry description:**
```yaml
description: Use when writing unit tests, integration tests,
running pytest, jest, vitest, or when user mentions "test",
"testing", "TDD", or "coverage"
```
Dobre: Konkretne akcie + nastroje + trigger slova

**Vynikajuci description:**
```yaml
description: Use when writing or debugging unit tests and
integration tests for Python code. Triggers on "pytest",
"unittest", "test_", "coverage", "TDD", "fixture", "mock".
NOT for E2E/browser tests (use e2e-testing skill instead).
```
Vyborne: Konkretne + triggery + negativne vymedzenie

---

## Frontmatter konfiguracia

### Vsetky dostupne parametre

```yaml
---
name: my-skill                    # Povinne - identifikator
description: ...                   # Povinne - trigger
allowed-tools: [Read, Write]       # Volitelne - obmedzenie tools
model: sonnet                      # Volitelne - haiku/sonnet/opus
priority: 10                       # Volitelne - pri konflikte
enabled: true                      # Volitelne - zapnut/vypnut
---
```

### allowed-tools priklady

| Scenar | allowed-tools | Preco |
|--------|---------------|-------|
| Read-only analyza | `[Read, Glob, Grep]` | Bez modifikacie |
| Editacia kodu | `[Read, Write, Edit, Glob, Grep]` | Plny file access |
| Build & deploy | `[Read, Bash, Glob]` | Shell prikazy |
| Plny pristup | `[Read, Write, Edit, Bash, Glob, Grep, Task]` | Vsetko |

### model volba

```yaml
# Pre rychle, jednoduche tasky
model: haiku

# Pre vacsinu programovania (default)
model: sonnet

# Pre komplexne architektonicke rozhodnutia
model: opus
```

---

## UIGen Priklad: Testing Skill

### .claude/skills/testing/SKILL.md

```markdown
---
name: uigen-testing
description: Use when writing tests for UIGen components, testing
file system operations, or running Vitest. Triggers on "test",
"vitest", "coverage", "file-system.test"
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep]
model: sonnet
---

# UIGen Testing Skill

## Testing Framework
- Vitest pre unit testy
- React Testing Library pre komponenty
- Mock file system pre VFS testy

## Klucove test files
- `src/lib/__tests__/file-system.test.ts` - VFS testy (678 riadkov)
- `src/components/__tests__/` - Component testy

## Prikazy
- `npm run test` - spusti vsetky testy
- `npm run test -- --watch` - watch mode
- `npm run test -- --coverage` - s coverage

## Test patterns
```typescript
import { describe, it, expect, beforeEach } from 'vitest'
import { VirtualFileSystem } from '../file-system'

describe('VirtualFileSystem', () => {
  let fs: VirtualFileSystem

  beforeEach(() => {
    fs = new VirtualFileSystem()
  })

  it('should create a file', () => {
    fs.createFile('/test.txt', 'content')
    expect(fs.readFile('/test.txt')).toBe('content')
  })
})
```

## Co testovat
- CRUD operacie pre VFS
- Component rendering
- User interactions
- Error states
```

### .claude/skills/testing/references/conventions.md

```markdown
# Testing Conventions

## Naming
- Test files: `*.test.ts` alebo `*.test.tsx`
- Test suites: Popisne nazvy (`describe('VirtualFileSystem')`)
- Test cases: Zacinaju `should` (`it('should create a file')`)

## Structure
1. Arrange - setup
2. Act - vykonaj akciu
3. Assert - over vysledok

## Mocking
- Pouzivame vi.mock() pre externalities
- Fixtures pre test data
```

---

## Decision Tree: Skill Structure

```
Mam novy skill na vytvorenie?
│
├── Je to jednoduchy skill (< 100 riadkov)?
│   └── Vytvor len SKILL.md
│
├── Potrebujem externe scripty?
│   └── Pridaj scripts/ priecinok
│
├── Mam dlhu dokumentaciu?
│   └── Rozdel do references/
│
└── Potrebujem templates/configs?
    └── Pridaj assets/
```

---

## Bezne chyby

| Chyba | Problem | Riesenie |
|-------|---------|----------|
| Nazov suboru `skill.md` | Case-sensitive! | Premenuj na `SKILL.md` |
| Skill priamo v `.claude/skills/` | Chyba priecinok | Vytvor nazovany priecinok |
| Chybajuci description | Skill sa neaktivuje | Pridaj jasny description |
| Prilis dlhy SKILL.md | Pomaly, context overflow | Pouzi references/ |
| Vague description | Aktivuje sa ked nema | Budte specificejsi |

---

## Cvicenia

### Cvicenie 1: Zakladny Skill
```
Vytvorte deployment skill pre vas projekt s:
- Jasnym description
- Potrebnymi allowed-tools
- Zakladnymi prikazmi
```

### Cvicenie 2: Skill s References
```
Rozsirite skill o references/ priecinok
s API dokumentaciou a coding conventions.
```

### Cvicenie 3: UIGen Skill
```
Vytvorte skill pre pracu s VirtualFileSystem
v UIGen projekte.
```

---

## Ďalej

[Priorita a Distribúcia](02-priorita.md) - Ako sa Skills aplikujú a zdieľajú
