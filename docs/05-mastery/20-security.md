# Security Best Practices

Bezpečnostné vzory pre Claude Code v produkcii.

---

## API Key Management

### Nikdy v kóde

```typescript
// ZLE
const client = new Anthropic({
  apiKey: 'sk-ant-...'  // NIKDY!
})

// DOBRE
const client = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY
})
```

### Environment variables

```bash
# .env.local (gitignored)
ANTHROPIC_API_KEY=sk-ant-...
JWT_SECRET=your-secret-key

# .env.example (committed)
ANTHROPIC_API_KEY=
JWT_SECRET=
```

### Rotácia kľúčov

```typescript
// Podpora pre key rotation

const API_KEYS = [
  process.env.ANTHROPIC_API_KEY_PRIMARY,
  process.env.ANTHROPIC_API_KEY_SECONDARY
].filter(Boolean)

let currentKeyIndex = 0

function getNextKey(): string {
  const key = API_KEYS[currentKeyIndex]
  currentKeyIndex = (currentKeyIndex + 1) % API_KEYS.length
  return key!
}
```

---

## Input Validation

### Prompt sanitization

```typescript
function sanitizePrompt(input: string): string {
  // Remove potential injection attempts
  let sanitized = input

  // Remove system message overrides
  sanitized = sanitized.replace(/\[SYSTEM\]/gi, '')
  sanitized = sanitized.replace(/\[INST\]/gi, '')

  // Remove excessive whitespace
  sanitized = sanitized.replace(/\s+/g, ' ').trim()

  // Limit length
  if (sanitized.length > 10000) {
    sanitized = sanitized.slice(0, 10000)
  }

  return sanitized
}
```

### Request validation

```typescript
// src/app/api/chat/route.ts

import { z } from 'zod'

const ChatRequestSchema = z.object({
  messages: z.array(z.object({
    role: z.enum(['user', 'assistant']),
    content: z.string().max(50000)
  })).max(100),
  projectId: z.string().uuid()
})

export async function POST(req: Request) {
  const body = await req.json()

  const parsed = ChatRequestSchema.safeParse(body)
  if (!parsed.success) {
    return Response.json(
      { error: 'Invalid request', details: parsed.error.errors },
      { status: 400 }
    )
  }

  // Continue with validated data
  const { messages, projectId } = parsed.data
}
```

---

## Authentication & Authorization

### Session validation

```typescript
// src/lib/auth.ts

import { jwtVerify, SignJWT } from 'jose'
import { cookies } from 'next/headers'

const SECRET = new TextEncoder().encode(
  process.env.JWT_SECRET || 'dev-secret-change-in-production'
)

export async function getSession(): Promise<Session | null> {
  const cookieStore = await cookies()
  const token = cookieStore.get('auth-token')?.value

  if (!token) return null

  try {
    const { payload } = await jwtVerify(token, SECRET)
    return payload as Session
  } catch {
    return null
  }
}

export async function requireAuth(): Promise<Session> {
  const session = await getSession()

  if (!session) {
    throw new Error('Unauthorized')
  }

  return session
}
```

### Resource ownership

```typescript
// src/actions/get-project.ts

export async function getProject(projectId: string) {
  const session = await getSession()

  const project = await prisma.project.findUnique({
    where: { id: projectId }
  })

  if (!project) {
    throw new Error('Project not found')
  }

  // Check ownership
  if (project.userId && project.userId !== session?.userId) {
    throw new Error('Forbidden')
  }

  return project
}
```

---

## Tool Restrictions

### Allowed tools per skill

```yaml
---
name: read-only-review
description: Code review without modifications
allowed-tools: [Read, Glob, Grep]  # No Write, Edit, Bash
---
```

### Dangerous command blocking

```typescript
// src/lib/security/command-filter.ts

const BLOCKED_PATTERNS = [
  /rm\s+-rf/i,
  />\s*\/dev/,
  /curl.*\|.*sh/,
  /wget.*\|.*bash/,
  /chmod\s+777/,
  /sudo/,
  /DROP\s+TABLE/i,
  /DELETE\s+FROM.*WHERE\s+1/i
]

export function isCommandSafe(command: string): boolean {
  for (const pattern of BLOCKED_PATTERNS) {
    if (pattern.test(command)) {
      return false
    }
  }
  return true
}
```

---

## Audit Logging

```typescript
// src/lib/audit.ts

interface AuditLog {
  timestamp: Date
  userId: string | null
  action: string
  resource: string
  details: Record<string, any>
  ip: string
  userAgent: string
}

export async function logAudit(log: AuditLog): Promise<void> {
  await prisma.auditLog.create({
    data: {
      ...log,
      timestamp: new Date()
    }
  })

  // Alert on suspicious activity
  if (isSuspicious(log)) {
    await sendAlert(log)
  }
}

function isSuspicious(log: AuditLog): boolean {
  const suspiciousPatterns = [
    'DELETE_ALL',
    'EXPORT_DATA',
    'CHANGE_PERMISSIONS',
    'API_KEY_ACCESS'
  ]

  return suspiciousPatterns.includes(log.action)
}
```

---

## Rate Limiting

```typescript
// src/lib/rate-limit.ts

import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '1 m'),  // 10 requests per minute
  analytics: true
})

export async function checkRateLimit(
  identifier: string
): Promise<{ success: boolean; remaining: number }> {
  const { success, remaining } = await ratelimit.limit(identifier)

  if (!success) {
    throw new Error('Rate limit exceeded')
  }

  return { success, remaining }
}
```

---

## Sensitive Data Handling

### Redaction

```typescript
function redactSensitiveData(text: string): string {
  let redacted = text

  // API keys
  redacted = redacted.replace(
    /sk-[a-zA-Z0-9-_]{20,}/g,
    '[REDACTED_API_KEY]'
  )

  // Passwords in URLs
  redacted = redacted.replace(
    /:\/\/[^:]+:[^@]+@/g,
    '://[REDACTED]@'
  )

  // Email addresses
  redacted = redacted.replace(
    /[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/g,
    '[REDACTED_EMAIL]'
  )

  return redacted
}
```

### Secure file handling

```yaml
# CLAUDE.md
## Security Rules

Never read or modify these files:
- .env*
- *credentials*
- *secrets*
- *.pem
- *.key
```

---

## Checklist

```
Security Checklist:

[ ] API keys v environment variables
[ ] JWT secrets sú silné (min 32 chars)
[ ] Input validation na všetkých endpoints
[ ] Rate limiting implementované
[ ] Audit logging zapnuté
[ ] Ownership checks na resourceoch
[ ] Tool restrictions definované
[ ] Sensitive data redacted v logoch
[ ] HTTPS enforced
[ ] CORS správne nastavené
```

---

## Cvičenia

### Cvičenie 1: Security Audit
```
Vykonaj security audit UIGen projektu a nájdi vulnerabilities.
```

### Cvičenie 2: Audit Dashboard
```
Vytvor dashboard pre zobrazenie audit logov.
```

### Cvičenie 3: Pen Testing
```
Implementuj automatizované security testy.
```

---

## Ďalej

[Debugging Agentic Failures](21-debugging.md)
