# Skills v Tíme a Firme

Distribúcia skills v tímovom prostredí a firemné pluginy.

---

## Predpoklady

Pred čítaním tohto modulu by ste mali mať:
- Pochopenie štruktúry Skills ([Štruktúra Skillov](04-struktura-skillov.md))
- Znalosť priority a hierarchie ([Priorita](05-priorita.md))

---

## Enterprise Skills

### Firemná hierarchia

```
┌─────────────────────────────────────────────────────────────┐
│  ENTERPRISE (Najvyššia priorita)                           │
│  ~/.claude/enterprise/                                      │
│  - Centrálne spravované IT oddelením                       │
│  - Povinné pre všetkých zamestnancov                       │
│  - Nemožno prepísať na projektovej úrovni                  │
├─────────────────────────────────────────────────────────────┤
│  TEAM                                                       │
│  Zdieľané cez Git repozitár                                │
│  - Code review pre zmeny                                    │
│  - Verzionované s projektom                                 │
├─────────────────────────────────────────────────────────────┤
│  PERSONAL                                                   │
│  ~/.claude/skills/                                          │
│  - Osobné preferencie                                       │
│  - Lokálne úpravy                                           │
└─────────────────────────────────────────────────────────────┘
```

### Príklad Enterprise Skill

```markdown
# ~/.claude/enterprise/coding-standards/SKILL.md
---
name: company-standards
description: Company coding standards for all projects.
priority: 1000
---

# Company Standards

## Pravidlá
- Použite TypeScript strict mode
- ESLint + Prettier povinné
- Testy pred merge
```

---

## Explicitné Načítanie Skills

### Manuálne načítanie

```bash
# Načítaj konkrétny skill
/skill load deployment

# Zoznam dostupných skills
/skill list
```

### Programatické načítanie

```
Použi skill "api-testing" a napíš testy pre /users endpoint.
```

### Skill inheritance v subagentoch

```yaml
---
name: specialized-agent
skills:
  - deployment
  - testing
inherit_skills: false  # Nedediť od parent agenta
---
```

---

## Tímová Spolupráca

### Zdieľanie cez Git

```
my-project/
├── .claude/
│   └── skills/
│       ├── deployment/
│       │   └── SKILL.md
│       └── testing/
│           └── SKILL.md
├── src/
└── package.json
```

**Výhody:**
- Každý člen tímu má rovnaké skills
- Verzionované spolu s kódom
- Code review pre zmeny

### Onboarding

1. **Clone repo** - Skills sú súčasťou
2. **Personal overrides** - ~/.claude/skills/ pre osobné
3. **Dokumentácia** - Vysvetlite čo každý skill robí

---

## Firemné Pluginy

### Inštalácia

```bash
# Community plugin
claude plugin install @community/react-patterns

# Firemný plugin
claude plugin install @company/internal-standards

# Zoznam nainštalovaných
claude plugin list

# Aktualizácia
claude plugin update @company/internal-standards
```

### Lokácia pluginov

```
~/.claude/plugins/
├── @community/
│   └── react-patterns/
│       └── SKILL.md
└── @company/
    └── internal-standards/
        └── SKILL.md
```

### Vytvorenie firemného pluginu

```bash
# Plugin manifest
cat > plugin.json << 'EOF'
{
  "name": "@company/dev-standards",
  "version": "1.0.0",
  "description": "Company development standards",
  "skills": ["skills/coding", "skills/testing"],
  "author": "IT Team"
}
EOF
```

### Bezpečnostná poznámka k pluginom

Pri používaní pluginov (najmä community) dbajte na:

| Aspekt | Odporúčanie |
|--------|-------------|
| **Zdroj** | Používajte overené zdroje |
| **Code review** | Prezrite SKILL.md pred inštaláciou |
| **allowed-tools** | Overte že plugin nemá zbytočné permissions |
| **Updates** | Sledujte changelogy pri aktualizáciách |

Pre enterprise prostredie:
```json
// ~/.claude/enterprise/settings.json
{
  "plugins": {
    "allowCommunityPlugins": false,
    "approvedSources": ["@company/*"]
  }
}
```

---

## Cvičenia

### Cvičenie 1: Tímový Skill
```
Vytvorte skill pre váš tím a commitnite ho do repozitára.
```

### Cvičenie 2: Plugin
```
Zabaľte vaše skills do pluginu s plugin.json.
```

---

## Ďalej

[Skill Templates](05-skill-templates.md) - Hotové skills na použitie
