# Virtual File System

In-memory súborový systém pre izolované prostredia.

---

## Prečo Virtual FS?

UIGen potrebuje:
- Izolovať generovaný kód od reálneho FS
- Serializovať stav do databázy
- Rýchle operácie bez disk I/O
- Bezpečnosť - žiadny prístup k systému

---

## VirtualFileSystem Trieda

```typescript
// src/lib/file-system.ts

interface VirtualFile {
  path: string
  content: string
  createdAt: number
  modifiedAt: number
}

export class VirtualFileSystem {
  private files: Map<string, VirtualFile> = new Map()

  // === CRUD Operations ===

  createFile(path: string, content: string): void {
    const normalizedPath = this.normalizePath(path)

    // Create parent directories
    this.ensureDirectory(normalizedPath)

    const now = Date.now()
    this.files.set(normalizedPath, {
      path: normalizedPath,
      content,
      createdAt: now,
      modifiedAt: now
    })
  }

  readFile(path: string): string | null {
    const file = this.files.get(this.normalizePath(path))
    return file?.content ?? null
  }

  updateFile(path: string, content: string): void {
    const normalizedPath = this.normalizePath(path)
    const file = this.files.get(normalizedPath)

    if (!file) {
      throw new Error(`File not found: ${path}`)
    }

    this.files.set(normalizedPath, {
      ...file,
      content,
      modifiedAt: Date.now()
    })
  }

  deleteFile(path: string): void {
    const normalizedPath = this.normalizePath(path)

    // Delete file and all nested files
    for (const filePath of this.files.keys()) {
      if (filePath === normalizedPath || filePath.startsWith(normalizedPath + '/')) {
        this.files.delete(filePath)
      }
    }
  }
}
```

---

## Path Normalization

```typescript
private normalizePath(path: string): string {
  // Remove trailing slashes
  let normalized = path.replace(/\/+$/, '')

  // Ensure leading slash
  if (!normalized.startsWith('/')) {
    normalized = '/' + normalized
  }

  // Resolve . and ..
  const parts = normalized.split('/').filter(Boolean)
  const resolved: string[] = []

  for (const part of parts) {
    if (part === '.') {
      continue
    }
    if (part === '..') {
      resolved.pop()
      continue
    }
    resolved.push(part)
  }

  return '/' + resolved.join('/')
}
```

---

## Directory Operations

```typescript
ensureDirectory(filePath: string): void {
  const parts = filePath.split('/').filter(Boolean)
  parts.pop() // Remove filename

  let currentPath = ''
  for (const part of parts) {
    currentPath += '/' + part
    // Directories are implicit - no need to store them
  }
}

listFiles(directory: string = '/'): string[] {
  const normalizedDir = this.normalizePath(directory)
  const files: string[] = []

  for (const path of this.files.keys()) {
    if (normalizedDir === '/' || path.startsWith(normalizedDir + '/')) {
      files.push(path)
    }
  }

  return files.sort()
}

listDirectory(directory: string = '/'): string[] {
  const normalizedDir = this.normalizePath(directory)
  const entries = new Set<string>()

  for (const path of this.files.keys()) {
    if (path.startsWith(normalizedDir + '/') || normalizedDir === '/') {
      const relativePath = path.slice(normalizedDir.length + 1)
      const firstPart = relativePath.split('/')[0]
      if (firstPart) {
        entries.add(firstPart)
      }
    }
  }

  return Array.from(entries).sort()
}
```

---

## Rename Operation

```typescript
rename(oldPath: string, newPath: string): void {
  const normalizedOld = this.normalizePath(oldPath)
  const normalizedNew = this.normalizePath(newPath)

  // Collect all files to rename
  const filesToRename: Array<[string, VirtualFile]> = []

  for (const [path, file] of this.files.entries()) {
    if (path === normalizedOld || path.startsWith(normalizedOld + '/')) {
      const newFilePath = path.replace(normalizedOld, normalizedNew)
      filesToRename.push([newFilePath, { ...file, path: newFilePath }])
    }
  }

  // Delete old files
  for (const [path] of filesToRename) {
    const oldFilePath = path.replace(normalizedNew, normalizedOld)
    this.files.delete(oldFilePath)
  }

  // Add new files
  for (const [path, file] of filesToRename) {
    this.files.set(path, file)
  }
}
```

---

## Serialization

```typescript
// Serialize for database storage

serialize(): SerializedFS {
  const files: Array<{
    path: string
    content: string
    createdAt: number
    modifiedAt: number
  }> = []

  for (const file of this.files.values()) {
    files.push({
      path: file.path,
      content: file.content,
      createdAt: file.createdAt,
      modifiedAt: file.modifiedAt
    })
  }

  return { files, version: 1 }
}

static deserialize(data: SerializedFS): VirtualFileSystem {
  const fs = new VirtualFileSystem()

  for (const file of data.files) {
    fs.files.set(file.path, {
      path: file.path,
      content: file.content,
      createdAt: file.createdAt,
      modifiedAt: file.modifiedAt
    })
  }

  return fs
}
```

---

## Usage in API Route

```typescript
// src/app/api/chat/route.ts

export async function POST(req: Request) {
  const { messages, projectId } = await req.json()

  // Load project and deserialize FS
  const project = await prisma.project.findUnique({
    where: { id: projectId }
  })

  const fs = VirtualFileSystem.deserialize(
    JSON.parse(project?.data || '{"files":[],"version":1}')
  )

  // AI modifies the FS through tools
  const result = await streamText({
    model: anthropic('claude-sonnet-4-20250514'),
    tools: {
      str_replace_editor: strReplaceTool(fs),
      file_manager: fileManagerTool(fs)
    },
    onFinish: async () => {
      // Persist changes
      await prisma.project.update({
        where: { id: projectId },
        data: {
          data: JSON.stringify(fs.serialize())
        }
      })
    }
  })

  return result.toDataStreamResponse()
}
```

---

## Testing

```typescript
// src/lib/__tests__/file-system.test.ts

describe('VirtualFileSystem', () => {
  let fs: VirtualFileSystem

  beforeEach(() => {
    fs = new VirtualFileSystem()
  })

  describe('createFile', () => {
    it('creates file at path', () => {
      fs.createFile('/src/app.tsx', 'content')
      expect(fs.readFile('/src/app.tsx')).toBe('content')
    })

    it('normalizes paths', () => {
      fs.createFile('src/app.tsx', 'content')
      expect(fs.readFile('/src/app.tsx')).toBe('content')
    })
  })

  describe('deleteFile', () => {
    it('deletes nested files', () => {
      fs.createFile('/src/a.ts', 'a')
      fs.createFile('/src/b.ts', 'b')
      fs.createFile('/other/c.ts', 'c')

      fs.deleteFile('/src')

      expect(fs.readFile('/src/a.ts')).toBeNull()
      expect(fs.readFile('/src/b.ts')).toBeNull()
      expect(fs.readFile('/other/c.ts')).toBe('c')
    })
  })

  describe('serialize/deserialize', () => {
    it('preserves all data', () => {
      fs.createFile('/a.ts', 'content a')
      fs.createFile('/b.ts', 'content b')

      const serialized = fs.serialize()
      const restored = VirtualFileSystem.deserialize(serialized)

      expect(restored.readFile('/a.ts')).toBe('content a')
      expect(restored.readFile('/b.ts')).toBe('content b')
    })
  })
})
```

---

## Cvičenia

### Cvičenie 1: Copy Operation
```
Implementuj copyFile a copyDirectory metódy.
```

### Cvičenie 2: File History
```
Pridaj undo/redo podporu s históriou zmien.
```

### Cvičenie 3: Diff
```
Implementuj diff metódu pre porovnanie dvoch verzií súboru.
```

---

## Ďalej

[MCP Servers](17-mcp-servers.md)
