# Appendix C: UIGen Reference

Kompletná referencia učebného projektu.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        UIGen                                 │
├─────────────────────────────────────────────────────────────┤
│  Frontend          │  Backend           │  AI                │
│  ─────────         │  ───────           │  ──                │
│  Next.js 15        │  API Routes        │  Claude API        │
│  React 19          │  Server Actions    │  Tool Use          │
│  Tailwind CSS      │  Prisma ORM        │  Streaming         │
│  Monaco Editor     │  SQLite            │                    │
└─────────────────────────────────────────────────────────────┘
```

---

## File Reference by Complexity

### Beginner Level

| File | Lines | Purpose |
|------|-------|---------|
| `src/components/ui/button.tsx` | 59 | Button component with variants |
| `src/components/chat/MessageInput.tsx` | 51 | Chat input textarea |
| `src/lib/utils.ts` | 10 | Utility functions (cn) |
| `src/actions/create-project.ts` | 29 | Server action example |

### Intermediate Level

| File | Lines | Purpose |
|------|-------|---------|
| `src/lib/contexts/file-system-context.tsx` | 243 | Virtual FS state management |
| `src/lib/contexts/chat-context.tsx` | 82 | Chat state with AI SDK |
| `src/lib/auth.ts` | 75 | JWT authentication |
| `src/components/editor/CodeEditor.tsx` | 80 | Monaco editor wrapper |
| `src/lib/__tests__/file-system.test.ts` | 678 | Comprehensive tests |

### Advanced Level

| File | Lines | Purpose |
|------|-------|---------|
| `src/app/api/chat/route.ts` | 86 | AI streaming endpoint |
| `src/lib/tools/str-replace.ts` | 49 | File editing tool |
| `src/lib/tools/file-manager.ts` | 53 | File management tool |
| `src/lib/transform/jsx-transformer.ts` | 291 | Babel JSX transformation |
| `src/components/preview/PreviewFrame.tsx` | 161 | Live preview in iframe |
| `src/lib/file-system.ts` | 518 | Virtual file system |

---

## Key Components

### ChatInterface

```typescript
// src/components/chat/ChatInterface.tsx
// Wrapper pre chat UI

<div className="flex flex-col h-full">
  <MessageList messages={messages} isLoading={status === 'pending'} />
  <MessageInput onSubmit={handleSubmit} />
</div>
```

### FileTree

```typescript
// src/components/editor/FileTree.tsx
// Zobrazuje virtuálny súborový systém

<div className="file-tree">
  {files.map(file => (
    <FileItem
      key={file.path}
      file={file}
      selected={selectedFile === file.path}
      onSelect={selectFile}
    />
  ))}
</div>
```

### PreviewFrame

```typescript
// src/components/preview/PreviewFrame.tsx
// Renderuje vygenerované komponenty v iframe

const html = generatePreviewHTML(files)
const blobUrl = URL.createObjectURL(new Blob([html], { type: 'text/html' }))

<iframe src={blobUrl} sandbox="allow-scripts" />
```

---

## Database Schema

```prisma
// prisma/schema.prisma

model User {
  id        String    @id @default(cuid())
  email     String    @unique
  password  String
  projects  Project[]
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
}

model Project {
  id        String   @id @default(cuid())
  name      String
  userId    String?
  user      User?    @relation(fields: [userId], references: [id])
  messages  String   // JSON stringified Message[]
  data      String   // JSON stringified VirtualFS
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

---

## API Endpoints

### POST /api/chat

**Request:**
```json
{
  "messages": [
    { "role": "user", "content": "Create a button component" }
  ],
  "projectId": "clx..."
}
```

**Response:** Streaming text/event-stream

### Server Actions

```typescript
// src/actions/index.ts
export { signUp, signIn, signOut, getUser } from './auth'

// src/actions/create-project.ts
export async function createProject(name: string)

// src/actions/get-projects.ts
export async function getProjects()

// src/actions/get-project.ts
export async function getProject(id: string)
```

---

## Virtual File System API

```typescript
class VirtualFileSystem {
  // CRUD
  createFile(path: string, content: string): void
  readFile(path: string): string | null
  updateFile(path: string, content: string): void
  deleteFile(path: string): void

  // Navigation
  listFiles(directory?: string): string[]
  listDirectory(directory?: string): string[]

  // Operations
  rename(oldPath: string, newPath: string): void

  // Persistence
  serialize(): SerializedFS
  static deserialize(data: SerializedFS): VirtualFileSystem
}
```

---

## AI Tools

### str_replace_editor

```typescript
{
  command: 'create' | 'view' | 'str_replace' | 'insert',
  path: string,
  content?: string,      // for create/insert
  old_str?: string,      // for str_replace
  new_str?: string,      // for str_replace
  insert_line?: number   // for insert
}
```

### file_manager

```typescript
{
  command: 'list' | 'rename' | 'delete',
  path: string,
  newPath?: string  // for rename
}
```

---

## Environment Variables

```bash
# .env
ANTHROPIC_API_KEY=sk-ant-...  # Required for AI
JWT_SECRET=your-secret        # Optional, has default
DATABASE_URL=file:./dev.db    # SQLite path
```

---

## NPM Scripts

```json
{
  "dev": "next dev --turbopack",
  "build": "next build",
  "start": "next start",
  "lint": "next lint",
  "test": "vitest",
  "setup": "npm install && npx prisma generate && npx prisma migrate dev",
  "db:reset": "npx prisma migrate reset --force"
}
```

---

## Common Modifications

### Adding a new UI component

1. Create in `src/components/ui/`
2. Use Radix primitives
3. Style with Tailwind + CVA
4. Export from component file

### Adding a new AI tool

1. Create in `src/lib/tools/`
2. Use `tool()` from 'ai' package
3. Define Zod schema for params
4. Add to tools object in `/api/chat/route.ts`

### Adding a new server action

1. Create in `src/actions/`
2. Add `"use server"` directive
3. Validate input with Zod
4. Check authentication if needed

---

[Späť na README](../README.md)
