# Server Actions a API Routes

Backend implementácia s Next.js a Claude Code.

---

## Server Actions v UIGen

UIGen používa Next.js Server Actions pre:

| Action | Súbor | Účel |
|--------|-------|------|
| `createProject` | `src/actions/create-project.ts` | Vytvorenie projektu |
| `getProjects` | `src/actions/get-projects.ts` | Zoznam projektov |
| `getProject` | `src/actions/get-project.ts` | Detail projektu |
| Auth actions | `src/actions/index.ts` | Prihlásenie, registrácia |

---

## Analýza Server Action

```
Ukáž mi create-project.ts a vysvetli ako funguje.
```

```typescript
// src/actions/create-project.ts
"use server"

import { prisma } from '@/lib/prisma'
import { getSession } from '@/lib/auth'

export async function createProject(name: string) {
  const session = await getSession()

  const project = await prisma.project.create({
    data: {
      name,
      userId: session?.userId || null,
      messages: JSON.stringify([]),
      data: JSON.stringify({})
    }
  })

  return { success: true, projectId: project.id }
}
```

**Kľúčové body:**
- `"use server"` - označuje server-side kód
- `getSession()` - overenie autentifikácie
- `prisma` - databázové operácie
- Nullable `userId` - podpora pre anonymných užívateľov

---

## Vytvorenie novej Server Action

```
Vytvor deleteProject server action s overením vlastníctva.
```

Claude vytvorí:

```typescript
// src/actions/delete-project.ts
"use server"

import { prisma } from '@/lib/prisma'
import { getSession } from '@/lib/auth'

export async function deleteProject(projectId: string) {
  const session = await getSession()

  if (!session) {
    return { success: false, error: 'Not authenticated' }
  }

  // Verify ownership
  const project = await prisma.project.findUnique({
    where: { id: projectId }
  })

  if (!project) {
    return { success: false, error: 'Project not found' }
  }

  if (project.userId !== session.userId) {
    return { success: false, error: 'Not authorized' }
  }

  await prisma.project.delete({
    where: { id: projectId }
  })

  return { success: true }
}
```

---

## API Routes

### Analýza Chat API

```
Analyzuj api/chat/route.ts a vysvetli streaming implementáciu.
```

```typescript
// src/app/api/chat/route.ts

import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

export async function POST(req: Request) {
  const { messages, projectId } = await req.json()

  // Load project context
  const project = await prisma.project.findUnique({
    where: { id: projectId }
  })

  // Deserialize virtual filesystem
  const fs = VirtualFileSystem.deserialize(
    JSON.parse(project?.data || '{}')
  )

  // Stream response with tool use
  const result = await streamText({
    model: anthropic('claude-sonnet-4-20250514'),
    messages,
    tools: {
      str_replace_editor: strReplaceTool(fs),
      file_manager: fileManagerTool(fs)
    },
    onFinish: async ({ response }) => {
      // Persist changes
      await prisma.project.update({
        where: { id: projectId },
        data: {
          messages: JSON.stringify([...messages, ...response.messages]),
          data: JSON.stringify(fs.serialize())
        }
      })
    }
  })

  return result.toDataStreamResponse()
}
```

---

## Validácia s Zod

```
Pridaj Zod validáciu do createProject action.
```

```typescript
// src/actions/create-project.ts
"use server"

import { z } from 'zod'
import { prisma } from '@/lib/prisma'
import { getSession } from '@/lib/auth'

const CreateProjectSchema = z.object({
  name: z.string()
    .min(1, 'Name is required')
    .max(100, 'Name too long')
    .regex(/^[a-zA-Z0-9-_ ]+$/, 'Invalid characters')
})

export async function createProject(name: string) {
  // Validate input
  const parsed = CreateProjectSchema.safeParse({ name })

  if (!parsed.success) {
    return {
      success: false,
      error: parsed.error.errors[0].message
    }
  }

  const session = await getSession()

  const project = await prisma.project.create({
    data: {
      name: parsed.data.name,
      userId: session?.userId || null,
      messages: JSON.stringify([]),
      data: JSON.stringify({})
    }
  })

  return { success: true, projectId: project.id }
}
```

---

## Error Handling

```
Pridaj robustné error handling do API route.
```

```typescript
export async function POST(req: Request) {
  try {
    const body = await req.json()

    // Validate request
    if (!body.messages || !body.projectId) {
      return Response.json(
        { error: 'Missing required fields' },
        { status: 400 }
      )
    }

    // ... implementation

  } catch (error) {
    console.error('Chat API error:', error)

    if (error instanceof z.ZodError) {
      return Response.json(
        { error: 'Invalid request', details: error.errors },
        { status: 400 }
      )
    }

    return Response.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

---

## Rate Limiting

```
Implementuj rate limiting pre chat API.
```

```typescript
// src/lib/rate-limit.ts

const rateLimitMap = new Map<string, { count: number; timestamp: number }>()

export function rateLimit(
  identifier: string,
  limit: number = 10,
  windowMs: number = 60000
): boolean {
  const now = Date.now()
  const record = rateLimitMap.get(identifier)

  if (!record || now - record.timestamp > windowMs) {
    rateLimitMap.set(identifier, { count: 1, timestamp: now })
    return true
  }

  if (record.count >= limit) {
    return false
  }

  record.count++
  return true
}

// V API route:
const ip = req.headers.get('x-forwarded-for') || 'unknown'
if (!rateLimit(ip)) {
  return Response.json({ error: 'Too many requests' }, { status: 429 })
}
```

---

## Cvičenia

### Cvičenie 1: Update Project
```
Vytvor updateProject action pre premenovanie projektu.
```

### Cvičenie 2: Pagination
```
Pridaj pagination do getProjects action.
```

### Cvičenie 3: Webhooks
```
Vytvor webhook endpoint pre notifikácie pri zmene projektu.
```

---

## Ďalej

[Model Selection](12-model-selection.md)
