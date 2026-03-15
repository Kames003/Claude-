# Inštalácia a Setup

Kompletný návod na inštaláciu a konfiguráciu Claude Code.

---

## Predpoklady

- Node.js 18+ (odporúčané)
- Anthropic API kľúč

---

## Inštalácia

### NPM (Globálna inštalácia)

```bash
npm install -g @anthropic-ai/claude-code
```

### Overenie inštalácie

```bash
claude --version
```

---

## API Kľúč

### Nastavenie

```bash
# Environment variable (odporúčané)
export ANTHROPIC_API_KEY=sk-ant-...

# Alebo v .bashrc / .zshrc
echo 'export ANTHROPIC_API_KEY=sk-ant-...' >> ~/.zshrc
source ~/.zshrc
```

### Overenie

```bash
claude "Hello, are you working?"
```

---

## Konfiguračné Súbory

### Hierarchia

```
~/.claude/                    # Globálne nastavenia
├── settings.json             # Hlavná konfigurácia
├── memory.json               # Uložené spomienky
└── sessions/                 # Session data

~/project/                    # Projektové nastavenia
├── CLAUDE.md                 # Projektový kontext
├── .claude/
│   └── settings.json         # Lokálne nastavenia
└── .claudeignore             # Ignorované súbory
```

### Priorita (od najvyššej)

1. Command line flags
2. Environment variables
3. Projektové `.claude/settings.json`
4. Globálne `~/.claude/settings.json`
5. Defaults

---

## settings.json

### Základná konfigurácia

```json
{
  "model": {
    "default": "sonnet",
    "fallback": "haiku"
  },
  "behavior": {
    "autoApprove": false,
    "showToolCalls": true
  },
  "permissions": {
    "allowFileWrite": "ask",
    "allowBashCommands": "ask"
  }
}
```

### Možnosti modelu

| Model | Použitie |
|-------|----------|
| `haiku` | Rýchle, jednoduché úlohy |
| `sonnet` | Všeobecné programovanie (default) |
| `opus` | Komplexné architektonické úlohy |

### Permissions

| Hodnota | Význam |
|---------|--------|
| `"always"` | Vždy povoliť |
| `"ask"` | Pýtať sa (default) |
| `"never"` | Vždy zamietnuť |

---

## CLAUDE.md

Súbor v koreni projektu ktorý definuje kontext pre Claude Code.

### Vytvorenie

```bash
claude /init
```

### Minimálna šablóna

```markdown
# CLAUDE.md

## Project
[Krátky popis projektu]

## Commands
- `npm run dev` - Development server
- `npm run test` - Run tests
- `npm run build` - Production build

## Conventions
- Use TypeScript strict mode
- Prefer functional components
- Tests in __tests__ directories
```

### Kompletná šablóna

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview
[Detailný popis projektu, jeho účel a hlavné features]

## Tech Stack
- Framework: Next.js 15 / React 19
- Language: TypeScript (strict)
- Styling: Tailwind CSS
- Database: PostgreSQL / Prisma
- Testing: Vitest / React Testing Library

## Commands

### Development
- `npm run dev` - Start development server
- `npm run db:push` - Push schema changes

### Testing
- `npm run test` - Run all tests
- `npm run test:watch` - Watch mode

### Production
- `npm run build` - Build for production

## Architecture
src/
├── app/          # Next.js App Router
├── components/   # React components
├── lib/          # Utilities and helpers
└── actions/      # Server actions

## Conventions
- Use `@/` import alias
- Prefer named exports
- Use TypeScript strict mode

## Do Not
- Modify package-lock.json manually
- Use inline styles (use Tailwind)
- Skip TypeScript types
```

---

## .claudeignore

Súbor v koreni projektu pre ignorovanie súborov:

```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
.next/

# Secrets
.env*
*.pem
credentials.json

# Large files
*.zip
*.tar.gz
```

---

## Environment Variables

### Autentifikácia

```bash
ANTHROPIC_API_KEY=sk-ant-...
```

### Konfigurácia

```bash
# Predvolený model
CLAUDE_MODEL=sonnet

# Debug mód
CLAUDE_DEBUG=1

# Verbose output
CLAUDE_VERBOSE=1
```

### Cesty

```bash
# Konfiguračný priečinok
CLAUDE_CONFIG_DIR=~/.claude

# Cache priečinok
CLAUDE_CACHE_DIR=~/.claude/cache
```

---

## Prvé spustenie

### 1. Spusti Claude Code

```bash
cd /path/to/your/project
claude
```

### 2. Vytvor CLAUDE.md

```bash
/init
```

### 3. Overiť funkčnosť

```
Vysvetli štruktúru tohto projektu.
```

---

## Troubleshooting

### API kľúč nefunguje

```bash
# Skontroluj či je nastavený
echo $ANTHROPIC_API_KEY

# Skontroluj formát (má začínať sk-ant-)
```

### Permission errors

```bash
# Skontroluj permissions
claude /permissions
```

### Debug mód

```bash
CLAUDE_DEBUG=1 claude "..."
```

---

## Cvičenia

### Cvičenie 1: Základný setup
```
Nainštaluj Claude Code, nastav API kľúč
a over funkčnosť príkazom claude --version.
```

### Cvičenie 2: CLAUDE.md
```
Vytvor CLAUDE.md pre existujúci projekt
pomocou /init a uprav podľa potreby.
```

---

## Ďalej

[CLI Commands](03-cli-commands.md) - Slash príkazy a keyboard shortcuts
