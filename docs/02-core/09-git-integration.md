# Git Integration

Komplexná práca s Git v Claude Code.

---

## Základné Git operácie

### Status a Diff

```
# Ukáž stav repozitára
"Aký je git status?"

# Ukáž zmeny
"Ukáž git diff pre posledné zmeny"

# História
"Ukáž posledných 10 commitov"
```

### Staging a Commit

```
# Stage konkrétne súbory
"Pridaj src/components/Button.tsx do staging"

# Stage všetko
"Stage všetky zmeny"

# Commit
"Commitni s message 'feat: add dark mode'"
```

### Branches

```
# Vytvor branch
"Vytvor novú branch feature/dark-mode"

# Prepni branch
"Prepni na branch develop"

# Merge
"Mergni feature/dark-mode do main"
```

---

## Commit Message Conventions

Claude Code automaticky generuje commit messages:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types

| Type | Kedy použiť |
|------|-------------|
| `feat` | Nová feature |
| `fix` | Bug fix |
| `docs` | Dokumentácia |
| `style` | Formátovanie |
| `refactor` | Refaktoring |
| `test` | Testy |
| `chore` | Maintenance |

### Príklady

```
feat(auth): add JWT token refresh

fix(ui): resolve button hover state on mobile

refactor(api): extract common validation logic

test(utils): add unit tests for formatDate
```

---

## Pull Request Workflow

### Vytvorenie PR

```
"Vytvor pull request pre aktuálnu branch"
```

Claude vykoná:

1. Push branch na remote
2. Vytvorí PR cez `gh pr create`
3. Pridá popis s:
   - Summary zmien
   - Test plan
   - Checklist

### PR Template

```markdown
## Summary
- Added dark mode support
- Updated 15 components
- Added ThemeContext

## Test plan
- [ ] Toggle works correctly
- [ ] Preference persists in localStorage
- [ ] SSR renders correctly

## Checklist
- [x] Tests pass
- [x] No TypeScript errors
- [ ] Documentation updated
```

### Review PR

```
# Zobraz PR
"Ukáž detaily PR #123"

# Pridaj komentár
"Pridaj review komentár k PR #123"

# Schváľ PR
"Schváľ PR #123"
```

---

## Branching Strategies

### Feature Branch

```
main
  └── feature/dark-mode
        ├── commit 1: Add ThemeContext
        ├── commit 2: Update components
        └── commit 3: Add tests
```

```
# Workflow
"Vytvor branch feature/user-profile"
"... implementácia ..."
"Commitni zmeny"
"Vytvor PR do main"
```

### Git Flow

```
main (production)
  └── develop
        ├── feature/auth
        ├── feature/dashboard
        └── release/1.0
```

```
# Release workflow
"Vytvor release branch z develop"
"Bumpni verziu na 1.0.0"
"Mergni do main a develop"
"Vytvor tag v1.0.0"
```

---

## Konflikt Resolution

### Detekcia

```
User: Mergni feature branch do main.

Claude: Detekoval som merge konflikt v:
- src/components/Button.tsx
- src/lib/utils.ts

Chceš aby som:
1. Ukázal konflikty
2. Navrhol riešenie
3. Manuálne vyriešil
```

### Riešenie

```
"Ukáž konflikty v Button.tsx"

Claude:
<<<<<<< HEAD
const Button = ({ variant = 'primary' }) => {
=======
const Button = ({ variant = 'default', size = 'md' }) => {
>>>>>>> feature/sizes

Navrhujem kombinovať obe zmeny:
const Button = ({ variant = 'default', size = 'md' }) => {
```

---

## Rebase vs Merge

### Kedy Rebase

```
# Udržiavanie čistej histórie
"Rebasni feature branch na main"

# Pred PR
"Squashni commity do jedného"
```

### Kedy Merge

```
# Zachovanie histórie
"Mergni s merge commitom"

# Veľké features
"Mergni feature branch (bez squash)"
```

---

## Stash

```
# Uloženie zmien
"Stashni aktuálne zmeny"

# Aplikovanie
"Aplikuj posledný stash"

# Zoznam
"Ukáž všetky stashe"

# Drop
"Zmaž posledný stash"
```

---

## Git Hooks Integration

Claude Code rešpektuje git hooks:

```
# .git/hooks/pre-commit
#!/bin/bash
npm run lint
npm run test
```

```
User: Commitni zmeny.

Claude: Spúšťam commit...
Pre-commit hook bežal:
✓ Lint passed
✓ Tests passed
Commit vytvorený: abc1234
```

---

## Bezpečnostné pravidlá

Claude Code dodržiava:

```
┌────────────────────────────────────────────────────────────┐
│  NIKDY automaticky:                                        │
│  - force push na main/master                               │
│  - git reset --hard bez potvrdenia                         │
│  - zmazanie remote branches bez potvrdenia                 │
│  - commit súborov s credentials                            │
└────────────────────────────────────────────────────────────┘
```

### Dangerous Operations

```
User: Force pushni na main.

Claude: Toto je nebezpečná operácia ktorá môže
prepísať históriu. Naozaj chceš pokračovať?

Odporúčam namiesto toho:
1. Vytvor novú branch
2. Cherry-pick potrebné commity
3. Vytvor PR
```

---

## GitHub CLI (gh)

Claude Code používa `gh` pre GitHub operácie:

```
# Issues
"Vytvor issue pre bug v login"
"Zatvor issue #42"

# PRs
"Ukáž otvorené PR"
"Mergni PR #15"

# Releases
"Vytvor release v1.0.0"

# Actions
"Ukáž status CI pre tento commit"
```

---

## Cvičenia

### Cvičenie 1: Feature Branch
```
Vytvor feature branch, implementuj zmenu,
commitni a vytvor PR.
```

### Cvičenie 2: Konflikt Resolution
```
Simuluj merge konflikt a vyrieš ho.
```

### Cvičenie 3: Release
```
Vytvor release workflow: branch, version bump,
changelog, tag, merge.
```

---

---

## Dalej

**Pokracuj na Tier 3: Intermediate**

[Prakticke tipy](../03-intermediate/08-prakticke-tipy.md) - Best practices a overene vzory
