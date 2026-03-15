# Prvá Session s Claude Code

Hands-on tutorial kde vytvoríte prvé zmeny v reálnom projekte.

---

## Predpoklady

- Nainštalovaný Claude Code (`npm install -g @anthropic-ai/claude-code`)
- Stiahnutý UIGen projekt
- Terminál v priečinku projektu

---

## Krok 1: Spustenie Claude Code

```bash
cd /cesta/k/uigen
claude
```

Claude Code sa spustí a načíta kontext projektu vrátane `CLAUDE.md`.

---

## Krok 2: Pochopenie projektu

Prvá úloha - nechajte Claude vysvetliť projekt:

```
Vysvetli mi štruktúru tohto projektu a jeho hlavné komponenty.
```

Claude prečíta súbory a poskytne prehľad:
- Next.js 15 s App Router
- React komponenty v `src/components/`
- AI integrácia v `src/lib/tools/`
- Databáza s Prisma

---

## Krok 3: Čítanie kódu

Pozrime sa na jednoduchý komponent:

```
Ukáž mi obsah súboru src/components/ui/button.tsx
```

Claude použije **Read** tool a zobrazí kód. Všimnite si:
- CVA (class-variance-authority) pre varianty
- Tailwind CSS triedy
- TypeScript typy pre props

---

## Krok 4: Prvá editácia

Pridajme nový variant do Button komponenty:

```
Pridaj nový variant "warning" do Button komponenty s oranžovou farbou.
```

Claude použije **Edit** tool:

```typescript
// Pred
const buttonVariants = cva(
  "...",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        destructive: "bg-destructive text-destructive-foreground",
        // ...
      }
    }
  }
)

// Po - Claude pridá:
warning: "bg-orange-500 text-white hover:bg-orange-600",
```

---

## Krok 5: Vytvorenie testu

Teraz vytvoríme test pre nový variant:

```
Vytvor test pre Button komponent ktorý overí že warning variant má správne CSS triedy.
```

Claude vytvorí test v `src/components/ui/__tests__/button.test.tsx`:

```typescript
import { render, screen } from '@testing-library/react'
import { Button } from '../button'

describe('Button', () => {
  it('renders warning variant with orange background', () => {
    render(<Button variant="warning">Warning</Button>)
    const button = screen.getByRole('button')
    expect(button).toHaveClass('bg-orange-500')
  })
})
```

---

## Krok 6: Spustenie testov

Overte že test funguje:

```
Spusti testy pre Button komponent.
```

Claude použije **Bash** tool:

```bash
npm run test -- src/components/ui/__tests__/button.test.tsx
```

---

## Krok 7: Git operácie

Commitnite zmeny:

```
Commitni tieto zmeny s popisom "feat: add warning variant to Button"
```

Claude vykoná:

```bash
git add src/components/ui/button.tsx
git add src/components/ui/__tests__/button.test.tsx
git commit -m "feat: add warning variant to Button"
```

---

## Prehľad použitých nástrojov

| Nástroj | Účel | Príklad |
|---------|------|---------|
| **Read** | Čítanie súborov | Zobrazenie button.tsx |
| **Edit** | Úprava súborov | Pridanie variantu |
| **Write** | Vytvorenie súborov | Nový test súbor |
| **Bash** | Shell príkazy | npm run test, git commit |
| **Glob** | Vyhľadávanie súborov | Nájdenie testov |

---

## Cvičenia

### Cvičenie 1: Nový komponent
Vytvorte Input komponent s variantmi `default`, `error`, `success`:

```
Vytvor Input komponent v src/components/ui/input.tsx s tromi variantmi:
default, error (červený border), success (zelený border).
```

### Cvičenie 2: Dokumentácia
Pridajte JSDoc komentáre:

```
Pridaj JSDoc komentáre k Button komponente a jej props.
```

### Cvičenie 3: Refaktoring
Vylepšite existujúci kód:

```
Analyzuj MessageInput.tsx a navrhni vylepšenia pre lepšiu čitateľnosť.
```

---

## Tipy pre efektívnu prácu

1. **Buďte konkrétni** - "Pridaj variant warning" je lepšie ako "uprav button"
2. **Overujte zmeny** - Vždy si nechajte ukázať čo Claude zmenil
3. **Používajte testy** - Claude vie vytvoriť aj spustiť testy
4. **Commitujte priebežne** - Malé commity sú lepšie ako veľké

---

---

## Dalej

[Plan Mode](04-plan-mode.md) - Planovanie komplexnych zmien
