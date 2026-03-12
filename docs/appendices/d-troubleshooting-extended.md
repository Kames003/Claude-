# Appendix D: Extended Troubleshooting

Pokročilé riešenie problémov.

---

## Error Reference

### Tool Errors

| Error | Príčina | Riešenie |
|-------|---------|----------|
| `File not found` | Neexistujúca cesta | Skontrolujte path, použite list najprv |
| `Permission denied` | Tool nie je povolený | Pridajte do allowed-tools |
| `Invalid parameters` | Zlý Zod schema | Skontrolujte typy parametrov |
| `Tool execution timeout` | Dlhá operácia | Zvýšte timeout alebo rozdeľte |

### Context Errors

| Error | Príčina | Riešenie |
|-------|---------|----------|
| `Context length exceeded` | Príliš veľa textu | Redukujte kontext, summarizujte |
| `Response truncated` | Výstup presiahol limit | Požiadajte o kratšiu odpoveď |
| `Rate limit exceeded` | Príliš veľa requestov | Počkajte a skúste znova |

### Skill Errors

| Error | Príčina | Riešenie |
|-------|---------|----------|
| `Skill not found` | Zlá štruktúra | Skontrolujte .claude/skills/<name>/SKILL.md |
| `Skill not activating` | Slabý description | Rozšírte trigger frázy |
| `Wrong skill activated` | Podobné descriptions | Diferencujte descriptions |

---

## Debug Scenarios

### Scenario 1: Claude ignoruje inštrukcie

**Symptóm:** Claude nepoužíva správny formát alebo štýl.

**Diagnostika:**
1. Skontrolujte či CLAUDE.md je v root
2. Overte že nie je príliš dlhý (>1000 riadkov)
3. Skontrolujte či inštrukcie nie sú v konflikte

**Riešenie:**
```markdown
# CLAUDE.md

## CRITICAL RULES (Claude must follow)
- Always use TypeScript
- Never use any type
- Always add tests

## Preferences (nice to have)
- Prefer functional components
- Use descriptive names
```

### Scenario 2: Tool zlyhá bez chyby

**Symptóm:** Tool vráti success ale nič sa nestalo.

**Diagnostika:**
```typescript
// Pridajte logging
execute: async (params) => {
  console.log('[DEBUG] Tool called with:', params)
  const result = await doOperation(params)
  console.log('[DEBUG] Result:', result)
  return result
}
```

**Riešenie:**
- Overte že operácia skutočne prebehla
- Skontrolujte návratovú hodnotu

### Scenario 3: Nekonečný loop

**Symptóm:** Claude opakuje rovnakú akciu.

**Diagnostika:**
```typescript
// Track actions
const actions: string[] = []

// V API route
const actionKey = `${toolName}:${JSON.stringify(args)}`
if (actions.slice(-3).every(a => a === actionKey)) {
  throw new Error('Loop detected')
}
actions.push(actionKey)
```

**Riešenie:**
- Buďte konkrétnejší v prompte
- Definujte jasné ukončovacie podmienky

### Scenario 4: Hallucinácia kódu

**Symptóm:** Claude referencuje neexistujúci kód.

**Diagnostika:**
1. Požiadajte o list súborov najprv
2. Vždy používajte view pred edit

**Riešenie:**
```
# V CLAUDE.md

## File Operations
Always list or view files before modifying them.
Never assume file content - always read first.
```

---

## Performance Issues

### Pomalé odpovede

| Príčina | Riešenie |
|---------|----------|
| Príliš veľa kontextu | Redukujte súbory, summarizujte |
| Komplexný prompt | Zjednodušte, rozdeľte na kroky |
| Opus model | Prepnite na Sonnet pre bežné úlohy |
| Veľa tool calls | Kombinujte operácie |

### Vysoké náklady

| Príčina | Riešenie |
|---------|----------|
| Opus pre všetko | Použite model selection |
| Opakované otázky | Implementujte caching |
| Veľké súbory v kontexte | Posielajte len relevantné časti |

---

## Common Mistakes

### 1. Príliš vague prompty

```
# Zlé
"Oprav bug"

# Dobré
"Oprav bug v src/lib/auth.ts kde JWT token expiruje
 predčasne. Problém je pravdepodobne v calculateExpiry funkcii."
```

### 2. Chýbajúci kontext

```
# Zlé
"Pridaj validáciu"

# Dobré
"Pridaj Zod validáciu do createProject server action v
 src/actions/create-project.ts. Validuj že name je string
 s dĺžkou 1-100 znakov."
```

### 3. Konfliktné inštrukcie

```
# Zlé (v CLAUDE.md)
"Vždy používaj any pre rýchlosť"
"Nikdy nepoužívaj any"

# Dobré
"Preferuj explicitné typy. Any je akceptovateľné len
 pre external library types bez definícií."
```

---

## Recovery Procedures

### Corrupt Virtual FS

```typescript
// Reset to empty state
await prisma.project.update({
  where: { id: projectId },
  data: {
    data: JSON.stringify({ files: [], version: 1 })
  }
})
```

### Stuck Conversation

```typescript
// Clear conversation history
await prisma.project.update({
  where: { id: projectId },
  data: {
    messages: JSON.stringify([])
  }
})
```

### API Key Issues

```bash
# Test API key
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "content-type: application/json" \
  -d '{"model":"claude-sonnet-4-20250514","max_tokens":10,"messages":[{"role":"user","content":"Hi"}]}'
```

---

## Getting Help

### Log Collection

```bash
# Collect debug info
claude --version
echo $ANTHROPIC_API_KEY | head -c 10
ls -la .claude/
cat .claude/settings.json
```

### Reporting Issues

1. GitHub Issues: https://github.com/anthropics/claude-code/issues
2. Include:
   - Claude Code version
   - OS and version
   - Steps to reproduce
   - Expected vs actual behavior
   - Relevant logs (redacted)

### Community Resources

- Anthropic Discord: https://discord.gg/anthropic
- Stack Overflow: tag `claude-code`
- Reddit: r/ClaudeAI

---

[Späť na README](../README.md)
