# Working Trees

Git worktrees pre paralelný vývoj s Claude Code.

---

## Čo sú Working Trees?

Git worktrees umožňujú mať viacero pracovných kópií rovnakého repozitára súčasne.

```
my-project/
├── .git/                    # Hlavný git directory
├── src/                     # Main working tree
└── ...

my-project-feature/          # Linked worktree
├── src/                     # Samostatná kópia
└── ...

my-project-bugfix/           # Ďalší linked worktree
├── src/
└── ...
```

---

## Prečo Working Trees?

### Bez worktrees

```
1. Pracujem na feature
2. Príde urgentný bug
3. Stash zmeny
4. Prepni branch
5. Oprav bug
6. Prepni späť
7. Unstash
8. Pokračuj

Problém: Context switching, strata flow
```

### S worktrees

```
1. Pracujem na feature v /project-feature
2. Príde urgentný bug
3. Otvorím /project-bugfix (už existuje)
4. Opravím bug
5. Vrátim sa do /project-feature
6. Pokračujem kde som skončil

Výhoda: Žiadne stashing, čisté prostredie
```

---

## Základné operácie

### Vytvorenie worktree

```bash
# Vytvor worktree pre existujúci branch
git worktree add ../project-feature feature/dark-mode

# Vytvor worktree s novým branchom
git worktree add -b feature/auth ../project-auth

# Vytvor worktree pre konkrétny commit
git worktree add ../project-v1 v1.0.0
```

### Zoznam worktrees

```bash
git worktree list

# Output:
# /Users/me/project           abc1234 [main]
# /Users/me/project-feature   def5678 [feature/dark-mode]
# /Users/me/project-auth      ghi9012 [feature/auth]
```

### Odstránenie worktree

```bash
# Odstráň worktree
git worktree remove ../project-feature

# Force remove (ak má uncommitted changes)
git worktree remove --force ../project-feature

# Vyčisti stale worktrees
git worktree prune
```

---

## Claude Code + Worktrees

### Workflow

```
Terminal 1: ~/project (main)
$ claude "Review the codebase"

Terminal 2: ~/project-feature (feature/auth)
$ claude "Implement JWT authentication"

Terminal 3: ~/project-bugfix (hotfix/login)
$ claude "Fix the login bug"
```

### Výhody

1. **Izolované kontexty** - každý Claude má svoj CLAUDE.md
2. **Paralelná práca** - viacero features súčasne
3. **Čisté prostredie** - žiadne uncommitted zmeny z iných taskov
4. **Rýchle prepínanie** - len cd do iného priečinka

---

## Praktické Patterns

### Pattern 1: Feature Development

```bash
# Setup
git worktree add -b feature/dark-mode ../project-dark-mode

# Práca
cd ../project-dark-mode
claude "Implement dark mode"

# Po dokončení
git push -u origin feature/dark-mode
cd ../project
git worktree remove ../project-dark-mode
```

### Pattern 2: Code Review

```bash
# Review PR v izolovanom worktree
git worktree add ../project-review pr/123
cd ../project-review
claude "Review this PR for issues"

# Cleanup
cd ../project
git worktree remove ../project-review
```

### Pattern 3: Parallel Experiments

```bash
# Dva rôzne prístupy
git worktree add -b experiment/zustand ../project-zustand
git worktree add -b experiment/jotai ../project-jotai

# Terminal 1
cd ../project-zustand
claude "Migrate state management to Zustand"

# Terminal 2
cd ../project-jotai
claude "Migrate state management to Jotai"

# Porovnaj výsledky
```

### Pattern 4: Hotfix

```bash
# Urgentný fix
git worktree add -b hotfix/security ../project-hotfix main
cd ../project-hotfix
claude "Fix the SQL injection vulnerability"
git push -u origin hotfix/security

# Späť k feature
cd ../project
```

---

## CLAUDE.md per Worktree

Každý worktree môže mať vlastný kontext:

```
project/CLAUDE.md            # Všeobecné pravidlá
project-feature/CLAUDE.md    # Feature-specific pravidlá
project-bugfix/CLAUDE.md     # Bugfix-specific pravidlá
```

### Príklad

```markdown
# CLAUDE.md (v project-feature)

## Current Task
Implementing dark mode feature.

## Focus Areas
- ThemeContext
- Component updates
- CSS variables

## Do Not Touch
- Authentication code
- API routes
- Database schema
```

---

## Organizácia

### Štruktúra priečinkov

```
~/projects/
├── my-app/                  # Main repo (main branch)
├── my-app-features/         # Feature worktrees
│   ├── dark-mode/
│   ├── user-profile/
│   └── notifications/
├── my-app-fixes/            # Bugfix worktrees
│   ├── login-bug/
│   └── memory-leak/
└── my-app-experiments/      # Experimental worktrees
    ├── new-arch/
    └── performance/
```

### Naming Convention

```
{project}-{type}-{name}

Príklady:
- uigen-feature-dark-mode
- uigen-fix-memory-leak
- uigen-exp-zustand
```

---

## Automatizácia

### Script pre nový feature

```bash
#!/bin/bash
# new-feature.sh

FEATURE_NAME=$1
PROJECT_DIR=$(pwd)
WORKTREE_DIR="../$(basename $PROJECT_DIR)-feature-$FEATURE_NAME"

git worktree add -b "feature/$FEATURE_NAME" "$WORKTREE_DIR"
cd "$WORKTREE_DIR"
echo "# Feature: $FEATURE_NAME" > CLAUDE.md
echo "Ready to work in $WORKTREE_DIR"
```

### Cleanup script

```bash
#!/bin/bash
# cleanup-worktrees.sh

# Odstráň merged worktrees
for worktree in $(git worktree list --porcelain | grep worktree | cut -d' ' -f2); do
  branch=$(git -C "$worktree" branch --show-current)
  if git branch --merged main | grep -q "$branch"; then
    echo "Removing merged worktree: $worktree"
    git worktree remove "$worktree"
  fi
done

git worktree prune
```

---

## Best Practices

| Practice | Popis |
|----------|-------|
| **Jeden task = jeden worktree** | Izolujte prácu |
| **Čistite po sebe** | Odstráňte merged worktrees |
| **Používajte konvencie** | Konzistentné pomenovanie |
| **Zdieľajte node_modules** | Symlink alebo pnpm |
| **CLAUDE.md per worktree** | Špecifický kontext |

---

## Potenciálne problémy

### node_modules

```bash
# Riešenie 1: Symlink
ln -s ../project/node_modules node_modules

# Riešenie 2: pnpm (automaticky zdieľa)
pnpm install

# Riešenie 3: npm install v každom worktree
npm install
```

### IDE Support

VS Code automaticky rozpozná worktrees.

```
File → Open Folder → Vyberte worktree priečinok
```

---

## Cvičenia

### Cvičenie 1: Basic Worktree
```
Vytvorte worktree pre novú feature a implementujte ju.
```

### Cvičenie 2: Parallel Work
```
Vytvorte 2 worktrees a pracujte na dvoch features
súčasne s Claude Code.
```

### Cvičenie 3: Automation
```
Vytvorte script pre automatické vytváranie
a čistenie worktrees.
```

---

## Ďalej

[Headless Mode](21-headless-mode.md)
