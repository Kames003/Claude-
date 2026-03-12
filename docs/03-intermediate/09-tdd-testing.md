# TDD a Testing s Claude Code

Test-driven development workflow s Claude ako coding partnerom.

---

## TDD Workflow

```
1. WRITE TEST   → Claude vytvorí test pre novú funkcionalitu
2. RUN TEST     → Test zlyhá (red)
3. IMPLEMENT    → Claude implementuje minimálny kód
4. RUN TEST     → Test prejde (green)
5. REFACTOR     → Claude vylepší kód
```

---

## Príklad: Pridanie copyFile metódy

### Krok 1: Napíšte test

```
Vytvor test pre novú metódu copyFile v VirtualFileSystem triede.
Metóda by mala skopírovať súbor z jednej cesty na druhú.
```

Claude vytvorí test:

```typescript
// src/lib/__tests__/file-system.test.ts

describe('VirtualFileSystem.copyFile', () => {
  it('should copy file to new location', () => {
    const fs = new VirtualFileSystem()
    fs.createFile('/original.txt', 'content')

    fs.copyFile('/original.txt', '/copy.txt')

    expect(fs.readFile('/copy.txt')).toBe('content')
    expect(fs.readFile('/original.txt')).toBe('content')
  })

  it('should throw if source does not exist', () => {
    const fs = new VirtualFileSystem()

    expect(() => {
      fs.copyFile('/missing.txt', '/copy.txt')
    }).toThrow('File not found')
  })

  it('should create parent directories if needed', () => {
    const fs = new VirtualFileSystem()
    fs.createFile('/file.txt', 'data')

    fs.copyFile('/file.txt', '/nested/deep/copy.txt')

    expect(fs.readFile('/nested/deep/copy.txt')).toBe('data')
  })
})
```

### Krok 2: Spustite test (red)

```
Spusti testy pre file-system.
```

```bash
npm run test -- src/lib/__tests__/file-system.test.ts

# Output: FAIL - copyFile is not a function
```

### Krok 3: Implementujte (green)

```
Implementuj metódu copyFile v src/lib/file-system.ts
aby všetky testy prešli.
```

Claude pridá:

```typescript
copyFile(sourcePath: string, destPath: string): void {
  const normalizedSource = this.normalizePath(sourcePath)
  const normalizedDest = this.normalizePath(destPath)

  const content = this.readFile(normalizedSource)
  if (content === null) {
    throw new Error('File not found')
  }

  this.createFile(normalizedDest, content)
}
```

### Krok 4: Overenie

```
Spusti testy znova.
```

```bash
npm run test -- src/lib/__tests__/file-system.test.ts

# Output: PASS - all tests passed
```

### Krok 5: Refaktoring

```
Analyzuj copyFile implementáciu a navrhni vylepšenia.
```

---

## UIGen Testing Patterns

### Testovanie komponentov

```typescript
// src/components/chat/__tests__/MessageInput.test.tsx

import { render, screen, fireEvent } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { MessageInput } from '../MessageInput'

describe('MessageInput', () => {
  it('calls onSubmit with input value', async () => {
    const onSubmit = vi.fn()
    render(<MessageInput onSubmit={onSubmit} />)

    const input = screen.getByRole('textbox')
    await userEvent.type(input, 'Hello Claude')
    await userEvent.keyboard('{Enter}')

    expect(onSubmit).toHaveBeenCalledWith('Hello Claude')
  })

  it('clears input after submit', async () => {
    render(<MessageInput onSubmit={vi.fn()} />)

    const input = screen.getByRole('textbox')
    await userEvent.type(input, 'message')
    await userEvent.keyboard('{Enter}')

    expect(input).toHaveValue('')
  })
})
```

### Testovanie kontextov

```typescript
// src/lib/contexts/__tests__/file-system-context.test.tsx

import { renderHook, act } from '@testing-library/react'
import { FileSystemProvider, useFileSystem } from '../file-system-context'

describe('FileSystemContext', () => {
  it('provides file operations', () => {
    const { result } = renderHook(() => useFileSystem(), {
      wrapper: FileSystemProvider
    })

    act(() => {
      result.current.createFile('/test.txt', 'content')
    })

    expect(result.current.readFile('/test.txt')).toBe('content')
  })
})
```

---

## Coverage Analysis

```
Analyzuj test coverage pre src/lib/file-system.ts a identifikuj chýbajúce testy.
```

Claude analyzuje a navrhne:

```
Chýbajúce testy:
1. rename() s neexistujúcim súborom
2. deleteFile() s nested directories
3. serialize/deserialize edge cases
4. Path normalization pre Windows paths
```

---

## Cvičenia

### Cvičenie 1: Test pre nový tool
```
Vytvor testy pre nový search tool ktorý vyhľadáva text v súboroch.
```

### Cvičenie 2: Integration test
```
Vytvor integration test ktorý overí celý flow:
vytvorenie projektu -> chat -> generovanie kódu -> uloženie.
```

### Cvičenie 3: Error handling
```
Pridaj testy pre error handling v api/chat/route.ts.
```

---

## Testing Skill Template

```yaml
---
name: tdd-testing
description: Use when writing tests, running tests, checking coverage,
or when user mentions "test", "TDD", "coverage", "vitest", "jest"
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep]
model: sonnet
---

# TDD Testing Skill

## Workflow
1. Always write test FIRST
2. Run test to verify it fails
3. Implement minimum code
4. Run test to verify it passes
5. Refactor if needed

## Commands
- `npm run test` - run all tests
- `npm run test -- --coverage` - with coverage
- `npm run test -- path/to/test.ts` - single file

## Conventions
- Tests in __tests__ directories
- Use describe/it blocks
- Mock external dependencies
- Test edge cases
```

---

## Ďalej

[Context a State Management](10-context-state.md)
