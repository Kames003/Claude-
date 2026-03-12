# Appendix A: CLAUDE.md Templates

Hotové šablóny pre rôzne typy projektov.

---

## Minimal Template

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

---

## Full-Featured Template

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
- `npm run db:seed` - Seed database

### Testing
- `npm run test` - Run all tests
- `npm run test:watch` - Watch mode
- `npm run test:coverage` - With coverage

### Production
- `npm run build` - Build for production
- `npm run start` - Start production server

## Architecture

### Directory Structure
src/
├── app/          # Next.js App Router
├── components/   # React components
├── lib/          # Utilities and helpers
├── hooks/        # Custom React hooks
└── actions/      # Server actions

### Key Patterns
- Server Components by default
- Client Components with "use client"
- Server Actions for mutations
- React Query for data fetching

## Conventions

### Code Style
- Use `@/` import alias
- Prefer named exports
- Use TypeScript strict mode
- No any types

### Components
- PascalCase for components
- Props interface above component
- Destructure props
- Use React.FC sparingly

### Testing
- Test files in __tests__/
- Use describe/it blocks
- Mock external dependencies
- Test user behavior, not implementation

## Security
- Never commit .env files
- Use environment variables for secrets
- Validate all user input
- Use parameterized queries

## Do Not
- Modify package-lock.json manually
- Use inline styles (use Tailwind)
- Skip TypeScript types
- Commit console.logs
```

---

## React/Next.js Template

```markdown
# CLAUDE.md

## Project
Next.js 15 application with React 19.

## Commands
- `npm run dev` - Development (localhost:3000)
- `npm run test` - Vitest tests
- `npm run lint` - ESLint check

## Structure
- `src/app/` - App Router pages
- `src/components/` - Reusable components
- `src/lib/` - Utilities

## Conventions
- Use "use client" only when needed
- Prefer Server Components
- Use @/ import alias
- Tailwind for styling
```

---

## Python Template

```markdown
# CLAUDE.md

## Project
Python application with FastAPI.

## Commands
- `python -m venv venv` - Create virtualenv
- `source venv/bin/activate` - Activate
- `pip install -r requirements.txt` - Install deps
- `uvicorn main:app --reload` - Run server
- `pytest` - Run tests

## Structure
- `app/` - Application code
- `tests/` - Test files
- `alembic/` - Database migrations

## Conventions
- Type hints required
- Use pydantic for validation
- Async functions preferred
- Tests with pytest
```

---

## Monorepo Template

```markdown
# CLAUDE.md

## Project
Turborepo monorepo with multiple packages.

## Commands
- `pnpm install` - Install all dependencies
- `pnpm dev` - Run all in development
- `pnpm build` - Build all packages
- `pnpm test` - Run all tests

## Structure
apps/
├── web/          # Next.js frontend
├── api/          # Express backend
└── docs/         # Documentation

packages/
├── ui/           # Shared components
├── config/       # Shared configs
└── types/        # Shared TypeScript types

## Conventions
- Use workspace dependencies
- Shared types in packages/types
- UI components in packages/ui
```

---

## UIGen Project Template

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview
UIGen is an AI-powered React component generator with live preview.

## Commands
- `npm run dev` - Development server (http://localhost:3000)
- `npm run test` - Vitest tests
- `npm run setup` - Install deps + Prisma setup
- `npm run db:reset` - Reset database

## Architecture

### Core Stack
- Next.js 15 + React 19 + TypeScript
- Tailwind CSS v4 + Radix UI
- SQLite + Prisma ORM
- Anthropic Claude AI with tool use

### Key Patterns
- Virtual File System (in-memory, no disk I/O)
- AI tools: str_replace_editor, file_manager
- Contexts: ChatContext, FileSystemContext
- Streaming responses via Vercel AI SDK

## Conventions
- Use @/ import alias
- "use client" for interactive components
- Tests in __tests__ directories
- Tailwind only, no inline styles
```

---

[Späť na README](../README.md)
