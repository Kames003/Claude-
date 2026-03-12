# Context a State Management

Práca s React state a context pomocou Claude Code.

---

## React Context v UIGen

UIGen používa dva hlavné kontexty:

| Context | Účel | Súbor |
|---------|------|-------|
| **FileSystemContext** | Virtual file system state | `src/lib/contexts/file-system-context.tsx` |
| **ChatContext** | Chat messages a AI state | `src/lib/contexts/chat-context.tsx` |

---

## FileSystemContext - Analýza

```
Vysvetli mi ako funguje FileSystemContext v tomto projekte.
```

Claude analyzuje 243 riadkov:

```typescript
// Kľúčové časti file-system-context.tsx

interface FileSystemContextType {
  files: VirtualFileSystem
  selectedFile: string | null
  createFile: (path: string, content: string) => void
  updateFile: (path: string, content: string) => void
  deleteFile: (path: string) => void
  selectFile: (path: string) => void
}

export function FileSystemProvider({ children }: Props) {
  const [fs] = useState(() => new VirtualFileSystem())
  const [selectedFile, setSelectedFile] = useState<string | null>(null)

  // Tool call handling - most critical part
  const handleToolCall = useCallback((toolCall: ToolCall) => {
    if (toolCall.name === 'str_replace_editor') {
      // Handle file operations from Claude
      const { command, path, content } = toolCall.args
      if (command === 'create') {
        fs.createFile(path, content)
      }
      // ...
    }
  }, [fs])

  return (
    <FileSystemContext.Provider value={{
      files: fs,
      selectedFile,
      createFile: (path, content) => fs.createFile(path, content),
      // ...
    }}>
      {children}
    </FileSystemContext.Provider>
  )
}
```

---

## Vytvorenie nového kontextu

### Príklad: ThemeContext

```
Vytvor ThemeContext pre prepínanie medzi light a dark mode.
```

Claude vytvorí:

```typescript
// src/lib/contexts/theme-context.tsx

import { createContext, useContext, useState, useCallback } from 'react'

type Theme = 'light' | 'dark' | 'system'

interface ThemeContextType {
  theme: Theme
  setTheme: (theme: Theme) => void
  resolvedTheme: 'light' | 'dark'
}

const ThemeContext = createContext<ThemeContextType | null>(null)

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setThemeState] = useState<Theme>('system')

  const resolvedTheme = useCallback((): 'light' | 'dark' => {
    if (theme === 'system') {
      return window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light'
    }
    return theme
  }, [theme])

  const setTheme = useCallback((newTheme: Theme) => {
    setThemeState(newTheme)
    localStorage.setItem('theme', newTheme)
  }, [])

  return (
    <ThemeContext.Provider value={{
      theme,
      setTheme,
      resolvedTheme: resolvedTheme()
    }}>
      {children}
    </ThemeContext.Provider>
  )
}

export function useTheme() {
  const context = useContext(ThemeContext)
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider')
  }
  return context
}
```

---

## Custom Hooks

### Analýza existujúceho hooku

```
Analyzuj use-auth.ts a vysvetli jeho fungovanie.
```

```typescript
// src/hooks/use-auth.ts

export function useAuth() {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    getUser().then(user => {
      setUser(user)
      setLoading(false)
    })
  }, [])

  const signIn = async (email: string, password: string) => {
    const result = await signInAction(email, password)
    if (result.success) {
      setUser(result.user)
    }
    return result
  }

  return { user, loading, signIn, signOut }
}
```

### Vytvorenie nového hooku

```
Vytvor useDebounce hook pre debouncing input hodnôt.
```

```typescript
// src/hooks/use-debounce.ts

import { useState, useEffect } from 'react'

export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value)

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(timer)
  }, [value, delay])

  return debouncedValue
}
```

---

## State Debugging

```
Analyzuj prečo sa FileTree komponent rerenderuje príliš často.
```

Claude diagnostikuje:

```
Problém: FileTree sa rerenderuje pri každej zmene v kontexte.

Príčina: Celý files objekt sa mení pri každej operácii.

Riešenie:
1. Použiť useMemo pre fileList
2. Memoizovať FileTree s React.memo
3. Rozdeliť kontext na menšie časti
```

---

## Optimalizácia

```
Optimalizuj FileSystemContext aby sa komponenty rerenderovali len keď je to potrebné.
```

Claude navrhne:

```typescript
// Rozdelenie na dva kontexty
const FileSystemStateContext = createContext<VirtualFileSystem | null>(null)
const FileSystemActionsContext = createContext<Actions | null>(null)

// Komponenty ktoré len čítajú použijú StateContext
// Komponenty ktoré len volajú akcie použijú ActionsContext
```

---

## Cvičenia

### Cvičenie 1: Notification Context
```
Vytvor NotificationContext pre zobrazovanie toast notifikácií.
```

### Cvičenie 2: Undo/Redo
```
Pridaj undo/redo funkcionalitu do FileSystemContext.
```

### Cvičenie 3: Persistence
```
Pridaj automatické ukladanie stavu do localStorage.
```

---

## Ďalej

[Server Actions a API](11-server-actions-api.md)
