# Priorita a Distribucia

Ako sa Skills aplikuju, zdielajia a riesia konflikty.

---

## Predpoklady

Pred citanim tohto modulu by ste mali mat:
- Znalost struktury Skills ([Struktura Skillov](04-struktura-skillov.md))
- Prakticky vytvoreny aspon jeden skill

---

## Hierarchia priorit

Skills sa aplikuju v tomto poradi (od najvyssej priority):

```
┌─────────────────────────────────────────────────────────────┐
│  1. ENTERPRISE                                              │
│     Firemne nastavenia (centralny config)                   │
│     Lokacia: ~/.claude/enterprise/                          │
├─────────────────────────────────────────────────────────────┤
│  2. PERSONAL                                                │
│     Osobne nastavenia pouzivatela                           │
│     Lokacia: ~/.claude/skills/                              │
├─────────────────────────────────────────────────────────────┤
│  3. PROJECT                                                 │
│     Projektove skills                                       │
│     Lokacia: .claude/skills/                                │
├─────────────────────────────────────────────────────────────┤
│  4. PLUGINS                                                 │
│     Marketplace pluginy                                     │
│     Lokacia: ~/.claude/plugins/                             │
└─────────────────────────────────────────────────────────────┘
```

### Pravidla aplikacie

1. **Vyssia priorita vyhrava** - Enterprise skill prebije Project skill
2. **Kaskadove dedenie** - Skills sa kombinuju, nie nahrazuju
3. **Explicitne prepinanie** - Mozes overridovat v prompte

---

## Priklady konfliktov

### Konflikt: Coding Style

```yaml
# Enterprise skill (~/.claude/enterprise/coding-style/)
description: Company coding standards
content: "Vzdy pouzivaj tabs pre indentaciu"

# Project skill (.claude/skills/coding-style/)
description: Project coding standards
content: "Pouzivaj 2 spaces pre indentaciu"

# Vysledok: TABS (Enterprise vyhrava)
```

### Konflikt: Testing Framework

```yaml
# Personal skill (~/.claude/skills/testing/)
description: My preferred testing setup
content: "Pouzi Jest pre vsetky testy"

# Project skill (.claude/skills/testing/)
description: Project testing framework
content: "Pouzi Vitest pre testy"

# Vysledok: JEST (Personal vyhrava)

# Workaround: Explicitne v prompte
"Pouzi Vitest (projekt standard) pre tieto testy"
```

### Konflikt: Model Selection

```yaml
# Enterprise skill
model: haiku  # Ustri naklady

# Project skill
model: opus  # Potrebujeme kvalitu

# Vysledok: HAIKU (Enterprise vyhrava)

# Workaround: Poziadaj o zmenu v settings
```

---

## Decision Tree: Riesenie konfliktov

```
Mam konflikt medzi skills?
│
├── Je to Enterprise vs Project?
│   └── Enterprise vyhrava (bez vynimky)
│
├── Je to Personal vs Project?
│   ├── Chces project behavior?
│   │   └── Explicitne to uvod v prompte
│   └── Chces personal behavior?
│       └── Nech to tak
│
└── Je to Project vs Plugin?
    └── Project vyhrava
```

---

## Metody distribucie

### 1. Repository commit (Najcastejsie)

Skills commitnute priamo v repo:

```
my-project/
├── .claude/
│   └── skills/
│       ├── deployment/
│       │   └── SKILL.md
│       ├── testing/
│       │   └── SKILL.md
│       └── code-review/
│           └── SKILL.md
├── src/
└── package.json
```

**Vyhody:**
- Kazdy clen timu ma rovnake skills
- Verzionovane spolu s kodom
- Code review pre zmeny v skills
- Branching pre experimenty

**Pouzitie:**
```bash
# Pridaj skill do repo
git add .claude/skills/my-skill/
git commit -m "feat(skills): add deployment skill"
git push
```

### 2. Personal skills (Pre jednotlivca)

Skills v domovskom priecinku:

```
~/.claude/
└── skills/
    ├── my-workflow/
    │   └── SKILL.md
    └── productivity/
        └── SKILL.md
```

**Vyhody:**
- Aplikuju sa na vsetky projekty
- Osobne preferencie
- Rychly prototyping

**Pouzitie:**
```bash
# Vytvor personal skill
mkdir -p ~/.claude/skills/my-workflow
cat > ~/.claude/skills/my-workflow/SKILL.md << 'EOF'
---
name: my-workflow
description: My personal workflow preferences
---

# My Workflow

## Preferences
- Vzdy pouzi git branching
- Commit messages v conventional commits
EOF
```

### 3. Plugin marketplace

Zdielane skills z komunity:

```bash
# Instalacia pluginu
claude plugin install @community/react-testing

# Lokacia
~/.claude/plugins/@community/react-testing/
```

**Vyhody:**
- Znovupouzitelne napriec projektami
- Community maintained
- Versioning a updates

### 4. Subagent frontmatter

Skills definovane pre konkretneho agenta:

```yaml
---
name: specialized-agent
skills:
  - deployment
  - testing
  - security-review
inherit_skills: false  # Nededit od parenta
---
```

**Pouzitie:**
- Multi-agent orchestracia
- Izolacia kontextu
- Presna kontrola

---

## UIGen Priklad: Skill Organization

### Odporucana struktura pre UIGen

```
uigen/
├── .claude/
│   └── skills/
│       ├── component-dev/        # Pre vyvoj komponentov
│       │   └── SKILL.md
│       ├── vfs-operations/       # Pre Virtual File System
│       │   └── SKILL.md
│       ├── ai-integration/       # Pre Claude API
│       │   └── SKILL.md
│       └── testing/              # Pre Vitest testy
│           └── SKILL.md
├── src/
└── package.json
```

### Priklad: Component Development Skill

```markdown
---
name: uigen-component-dev
description: Use when creating or modifying React components for UIGen.
Triggers on "component", "button", "form", "UI", "Tailwind", "shadcn"
allowed-tools: [Read, Write, Edit, Glob, Grep]
model: sonnet
---

# UIGen Component Development

## Stack
- React 19 s Server Components
- Tailwind CSS pre styling
- Radix UI primitives (cez shadcn/ui)
- TypeScript strict mode

## Conventions
- Komponenty v src/components/
- UI primitives v src/components/ui/
- Pouzi @/ import alias
- CVA pre varianty

## Example Component
```typescript
import { cn } from '@/lib/utils'
import { cva, type VariantProps } from 'class-variance-authority'

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground',
        destructive: 'bg-destructive text-destructive-foreground',
      },
    },
    defaultVariants: {
      variant: 'default',
    },
  }
)

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, className }))}
      {...props}
    />
  )
}
```
```

---

## Versioning Skills

### Semantic versioning pre skills

```yaml
---
name: deployment
version: 1.2.0
description: ...
---
```

### Changelog pattern

```markdown
# Changelog

## [1.2.0] - 2024-01-15
### Added
- Support for multi-region deployment

## [1.1.0] - 2024-01-10
### Changed
- Updated rollback procedure

## [1.0.0] - 2024-01-01
### Added
- Initial deployment skill
```

### Git workflow

```bash
# Feature branch pre skill update
git checkout -b skill/deployment-v1.2

# Zmeny
vim .claude/skills/deployment/SKILL.md

# Commit s conventional commit
git commit -m "feat(skills): add multi-region deployment support"

# Code review
git push -u origin skill/deployment-v1.2
# Vytvor PR

# Po merge
git checkout main
git pull
```

---

## Team Collaboration

### Onboarding noveho clena

1. **Clone repo** - Skills su sucastou
2. **Personal overrides** - Vysvetlite hierarchiu
3. **Skill dokumentacia** - Vysvetlite co kazdy skill robi

### Code review pre skills

```markdown
## Skill PR checklist

- [ ] Description je dostatocne specificky
- [ ] allowed-tools su minimalne potrebne
- [ ] Neprekryva sa s existujucimi skills
- [ ] Testovane na niekolkych scenaroch
- [ ] Dokumentacia je aktualna
```

---

## Cvicenia

### Cvicenie 1: Konflikt Resolution
```
Vytvorte dva konflikujuce skills (Personal a Project)
a experimentujte s prioritami.
```

### Cvicenie 2: Team Skills
```
Navrhnite sadu skills pre tym 5 vyvojarov
pracujucich na e-commerce projekte.
```

### Cvicenie 3: Versioning
```
Implementujte versioning a changelog
pre existujuci skill.
```

---

## Dalej

[Tool Use](06-tool-use.md) - Ako Claude pracuje s nastrojmi
