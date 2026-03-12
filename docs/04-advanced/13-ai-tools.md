# AI Tool Implementation

Vytváranie vlastných AI nástrojov pre Claude.

---

## Čo sú AI Tools?

Tools umožňujú Claude vykonávať akcie v reálnom svete:
- Čítanie/písanie súborov
- Spúšťanie príkazov
- Volanie API
- Manipulácia s dátami

---

## UIGen Tool Patterns

UIGen implementuje dva hlavné tools:

### str_replace_editor

```typescript
// src/lib/tools/str-replace.ts

import { tool } from 'ai'
import { z } from 'zod'
import { VirtualFileSystem } from '../file-system'

export function strReplaceTool(fs: VirtualFileSystem) {
  return tool({
    description: 'Create, view, or edit files in the virtual filesystem',
    parameters: z.object({
      command: z.enum(['create', 'view', 'str_replace', 'insert']),
      path: z.string().describe('File path'),
      content: z.string().optional().describe('File content for create'),
      old_str: z.string().optional().describe('String to replace'),
      new_str: z.string().optional().describe('Replacement string'),
      insert_line: z.number().optional().describe('Line to insert at')
    }),
    execute: async ({ command, path, content, old_str, new_str, insert_line }) => {
      switch (command) {
        case 'create':
          fs.createFile(path, content || '')
          return { success: true, message: `Created ${path}` }

        case 'view':
          const fileContent = fs.readFile(path)
          return { success: true, content: fileContent }

        case 'str_replace':
          const current = fs.readFile(path)
          if (!current) return { success: false, error: 'File not found' }
          const updated = current.replace(old_str!, new_str!)
          fs.updateFile(path, updated)
          return { success: true, message: 'File updated' }

        case 'insert':
          // Insert at specific line
          const lines = fs.readFile(path)?.split('\n') || []
          lines.splice(insert_line!, 0, content!)
          fs.updateFile(path, lines.join('\n'))
          return { success: true, message: 'Content inserted' }
      }
    }
  })
}
```

### file_manager

```typescript
// src/lib/tools/file-manager.ts

import { tool } from 'ai'
import { z } from 'zod'
import { VirtualFileSystem } from '../file-system'

export function fileManagerTool(fs: VirtualFileSystem) {
  return tool({
    description: 'Manage files: list, rename, delete',
    parameters: z.object({
      command: z.enum(['list', 'rename', 'delete']),
      path: z.string().describe('File or directory path'),
      newPath: z.string().optional().describe('New path for rename')
    }),
    execute: async ({ command, path, newPath }) => {
      switch (command) {
        case 'list':
          const files = fs.listFiles(path)
          return { success: true, files }

        case 'rename':
          fs.rename(path, newPath!)
          return { success: true, message: `Renamed to ${newPath}` }

        case 'delete':
          fs.deleteFile(path)
          return { success: true, message: `Deleted ${path}` }
      }
    }
  })
}
```

---

## Vytvorenie vlastného Tool

### Príklad: Search Tool

```
Vytvor search tool ktorý vyhľadáva text vo všetkých súboroch.
```

```typescript
// src/lib/tools/search.ts

import { tool } from 'ai'
import { z } from 'zod'
import { VirtualFileSystem } from '../file-system'

export function searchTool(fs: VirtualFileSystem) {
  return tool({
    description: 'Search for text across all files in the filesystem',
    parameters: z.object({
      query: z.string().describe('Text to search for'),
      caseSensitive: z.boolean().optional().default(false),
      filePattern: z.string().optional().describe('Glob pattern to filter files')
    }),
    execute: async ({ query, caseSensitive, filePattern }) => {
      const results: Array<{
        path: string
        line: number
        content: string
      }> = []

      const files = fs.listFiles('/')

      for (const file of files) {
        // Filter by pattern if provided
        if (filePattern && !matchPattern(file, filePattern)) {
          continue
        }

        const content = fs.readFile(file)
        if (!content) continue

        const lines = content.split('\n')
        const searchQuery = caseSensitive ? query : query.toLowerCase()

        lines.forEach((line, index) => {
          const searchLine = caseSensitive ? line : line.toLowerCase()
          if (searchLine.includes(searchQuery)) {
            results.push({
              path: file,
              line: index + 1,
              content: line.trim()
            })
          }
        })
      }

      return {
        success: true,
        matchCount: results.length,
        results: results.slice(0, 50) // Limit results
      }
    }
  })
}
```

---

## Tool Integration

### Registrácia v API Route

```typescript
// src/app/api/chat/route.ts

import { strReplaceTool } from '@/lib/tools/str-replace'
import { fileManagerTool } from '@/lib/tools/file-manager'
import { searchTool } from '@/lib/tools/search'

const result = await streamText({
  model: anthropic('claude-sonnet-4-20250514'),
  messages,
  tools: {
    str_replace_editor: strReplaceTool(fs),
    file_manager: fileManagerTool(fs),
    search: searchTool(fs)  // Nový tool
  }
})
```

---

## Zod Schemas

### Komplexné parametre

```typescript
const AnalyzeToolParams = z.object({
  files: z.array(z.string()).describe('Files to analyze'),
  options: z.object({
    complexity: z.boolean().optional(),
    dependencies: z.boolean().optional(),
    security: z.boolean().optional()
  }).optional(),
  outputFormat: z.enum(['json', 'markdown', 'text']).default('markdown')
})
```

### Validácia a error handling

```typescript
execute: async (params) => {
  try {
    // Validate params are semantically correct
    if (params.files.length === 0) {
      return { success: false, error: 'No files specified' }
    }

    // Execute logic
    const results = await analyzeFiles(params.files, params.options)

    return { success: true, results }

  } catch (error) {
    return {
      success: false,
      error: error instanceof Error ? error.message : 'Unknown error'
    }
  }
}
```

---

## Testing Tools

```typescript
// src/lib/tools/__tests__/search.test.ts

import { describe, it, expect, beforeEach } from 'vitest'
import { VirtualFileSystem } from '../../file-system'
import { searchTool } from '../search'

describe('searchTool', () => {
  let fs: VirtualFileSystem
  let search: ReturnType<typeof searchTool>

  beforeEach(() => {
    fs = new VirtualFileSystem()
    search = searchTool(fs)

    // Setup test files
    fs.createFile('/src/app.tsx', 'import React from "react"\nfunction App() {}')
    fs.createFile('/src/utils.ts', 'export function helper() {}')
  })

  it('finds matches across files', async () => {
    const result = await search.execute({
      query: 'function',
      caseSensitive: false
    })

    expect(result.success).toBe(true)
    expect(result.matchCount).toBe(2)
  })

  it('respects case sensitivity', async () => {
    const result = await search.execute({
      query: 'Function',
      caseSensitive: true
    })

    expect(result.matchCount).toBe(0)
  })
})
```

---

## Cvičenia

### Cvičenie 1: Undo Tool
```
Vytvor undo tool ktorý vracia posledné zmeny v súbore.
```

### Cvičenie 2: Diff Tool
```
Vytvor diff tool ktorý porovnáva dva súbory.
```

### Cvičenie 3: Code Analysis Tool
```
Vytvor tool ktorý analyzuje complexity kódu.
```

---

## Ďalej

[Streaming](14-streaming.md)
