# Code Transformations

JSX/Babel transformácie a live preview.

---

## Transformácie v UIGen

UIGen transformuje JSX kód pre live preview v iframe:

```
User Code (JSX)
    ↓
Babel Transform
    ↓
Import Map Generation
    ↓
Blob URL Creation
    ↓
iframe Rendering
```

---

## JSX Transformer

```typescript
// src/lib/transform/jsx-transformer.ts

import * as Babel from '@babel/standalone'

interface TransformResult {
  code: string
  imports: Map<string, string>
  errors: string[]
}

export function transformJSX(source: string): TransformResult {
  const imports = new Map<string, string>()
  const errors: string[] = []

  try {
    const result = Babel.transform(source, {
      presets: ['react'],
      plugins: [
        // Custom plugin to extract imports
        function extractImports() {
          return {
            visitor: {
              ImportDeclaration(path) {
                const source = path.node.source.value

                // External packages -> esm.sh
                if (!source.startsWith('.') && !source.startsWith('@/')) {
                  imports.set(source, `https://esm.sh/${source}`)
                }
              }
            }
          }
        }
      ]
    })

    return {
      code: result?.code || '',
      imports,
      errors
    }

  } catch (error) {
    errors.push(error instanceof Error ? error.message : 'Transform error')
    return { code: '', imports, errors }
  }
}
```

---

## Import Map Generation

```typescript
// Generovanie import map pre browser

function generateImportMap(imports: Map<string, string>): string {
  const importMap = {
    imports: Object.fromEntries(imports)
  }

  return JSON.stringify(importMap, null, 2)
}

// Príklad výstupu:
{
  "imports": {
    "react": "https://esm.sh/react@19",
    "react-dom": "https://esm.sh/react-dom@19",
    "lucide-react": "https://esm.sh/lucide-react"
  }
}
```

---

## Preview Frame

```typescript
// src/components/preview/PreviewFrame.tsx

import { useMemo } from 'react'
import { transformJSX, generateImportMap } from '@/lib/transform/jsx-transformer'

export function PreviewFrame({ files }) {
  const html = useMemo(() => {
    // Nájdi entry point
    const entryFile = files.find(f =>
      f.path === '/App.jsx' || f.path === '/App.tsx'
    )

    if (!entryFile) {
      return errorHTML('No entry point found')
    }

    // Transform code
    const { code, imports, errors } = transformJSX(entryFile.content)

    if (errors.length > 0) {
      return errorHTML(errors.join('\n'))
    }

    // Generate HTML
    return `
      <!DOCTYPE html>
      <html>
      <head>
        <script type="importmap">
          ${generateImportMap(imports)}
        </script>
        <script src="https://cdn.tailwindcss.com"></script>
      </head>
      <body>
        <div id="root"></div>
        <script type="module">
          import React from 'react'
          import { createRoot } from 'react-dom/client'

          ${code}

          const root = createRoot(document.getElementById('root'))
          root.render(React.createElement(App))
        </script>
      </body>
      </html>
    `
  }, [files])

  const blobUrl = useMemo(() => {
    const blob = new Blob([html], { type: 'text/html' })
    return URL.createObjectURL(blob)
  }, [html])

  return (
    <iframe
      src={blobUrl}
      className="w-full h-full border-0"
      sandbox="allow-scripts"
    />
  )
}
```

---

## Error Handling

```typescript
function errorHTML(message: string): string {
  return `
    <!DOCTYPE html>
    <html>
    <body style="
      font-family: monospace;
      background: #fee;
      color: #c00;
      padding: 20px;
    ">
      <h2>Preview Error</h2>
      <pre>${escapeHtml(message)}</pre>
    </body>
    </html>
  `
}

function escapeHtml(text: string): string {
  return text
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
}
```

---

## Multi-file Support

```typescript
// Podpora pre viacero súborov s @/ alias

function transformProject(files: VirtualFile[]): TransformResult {
  const fileMap = new Map(files.map(f => [f.path, f.content]))

  // Resolve @/ imports to actual files
  const resolveAlias = (importPath: string): string | null => {
    if (importPath.startsWith('@/')) {
      const realPath = importPath.replace('@/', '/')
      return fileMap.get(realPath) || fileMap.get(realPath + '.tsx') || null
    }
    return null
  }

  // Transform all files
  const transformedFiles = new Map<string, string>()

  for (const [path, content] of fileMap) {
    if (path.endsWith('.jsx') || path.endsWith('.tsx')) {
      const result = transformJSX(content, { resolveAlias })
      transformedFiles.set(path, result.code)
    }
  }

  return bundleFiles(transformedFiles)
}
```

---

## CSS Handling

```typescript
// Strip CSS imports and inject styles

function handleCSS(files: VirtualFile[]): string {
  const cssFiles = files.filter(f =>
    f.path.endsWith('.css')
  )

  return cssFiles
    .map(f => `<style>${f.content}</style>`)
    .join('\n')
}

// V HTML template:
const html = `
  <head>
    ${handleCSS(files)}
    <script src="https://cdn.tailwindcss.com"></script>
  </head>
`
```

---

## Hot Reload

```typescript
// Optimized re-rendering

function useHotReload(files: VirtualFile[]) {
  const [version, setVersion] = useState(0)
  const prevFilesRef = useRef(files)

  useEffect(() => {
    // Detect changes
    const changed = files.some((file, i) =>
      file.content !== prevFilesRef.current[i]?.content
    )

    if (changed) {
      setVersion(v => v + 1)
      prevFilesRef.current = files
    }
  }, [files])

  return version
}
```

---

## Cvičenia

### Cvičenie 1: TypeScript Support
```
Pridaj podporu pre TypeScript súbory v transformeri.
```

### Cvičenie 2: Error Overlay
```
Vytvor error overlay s line numbers a suggestions.
```

### Cvičenie 3: Source Maps
```
Implementuj source maps pre debugging v preview.
```

---

## Ďalej

[Virtual File System](16-virtual-filesystem.md)
