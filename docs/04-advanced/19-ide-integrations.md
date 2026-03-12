# IDE Integrations

Integrácia Claude Code s vývojovými prostrediami.

---

## VS Code Extension

### Inštalácia

1. Otvorte VS Code
2. Extensions (Ctrl+Shift+X)
3. Vyhľadajte "Claude Code"
4. Install

Alebo:
```bash
code --install-extension anthropic.claude-code
```

### Konfigurácia

```json
// .vscode/settings.json
{
  "claude-code.enabled": true,
  "claude-code.model": "sonnet",
  "claude-code.autoSuggest": true,
  "claude-code.inlineChat": true
}
```

---

## VS Code Features

### Inline Chat

Vyberte kód a stlačte `Ctrl+I` (alebo `Cmd+I` na Mac):

```
┌─────────────────────────────────────────┐
│  function calculateTotal(items) {       │
│    // selected code                     │
│  }                                      │
├─────────────────────────────────────────┤
│  Ask Claude: [Pridaj TypeScript typy]   │
└─────────────────────────────────────────┘
```

### Code Actions

Kliknite na žiarovku alebo `Ctrl+.`:

- "Explain with Claude"
- "Refactor with Claude"
- "Add tests with Claude"
- "Fix with Claude"

### Command Palette

`Ctrl+Shift+P`:

```
> Claude: Open Chat
> Claude: Explain Selection
> Claude: Generate Tests
> Claude: Refactor Selection
> Claude: Fix Errors
> Claude: Generate Documentation
```

### Side Panel

Otvorte Claude panel:
- Click na Claude ikonu v Activity Bar
- Alebo `Ctrl+Shift+C`

---

## VS Code Keyboard Shortcuts

| Shortcut | Akcia |
|----------|-------|
| `Ctrl+I` | Inline chat na výber |
| `Ctrl+Shift+C` | Otvoriť Claude panel |
| `Ctrl+K Ctrl+C` | Vysvetli kód |
| `Ctrl+K Ctrl+R` | Refaktoruj |
| `Ctrl+K Ctrl+T` | Generuj testy |

### Customizácia

```json
// keybindings.json
[
  {
    "key": "ctrl+shift+e",
    "command": "claude-code.explainSelection"
  },
  {
    "key": "ctrl+shift+r",
    "command": "claude-code.refactorSelection"
  }
]
```

---

## VS Code Tasks

### Claude ako Task

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Claude: Review File",
      "type": "shell",
      "command": "claude",
      "args": [
        "--non-interactive",
        "Review ${file} for bugs and improvements"
      ],
      "presentation": {
        "reveal": "always",
        "panel": "new"
      }
    },
    {
      "label": "Claude: Generate Tests",
      "type": "shell",
      "command": "claude",
      "args": [
        "--non-interactive",
        "Generate tests for ${file}"
      ]
    }
  ]
}
```

### Spustenie

`Ctrl+Shift+B` → Vybrať task

---

## JetBrains IDEs

### IntelliJ / WebStorm / PyCharm

1. Settings → Plugins
2. Marketplace → "Claude Code"
3. Install → Restart IDE

### Features

- **Tool Window**: View → Tool Windows → Claude
- **Context Actions**: Alt+Enter na kóde
- **Generate**: Code → Generate → With Claude

### Konfigurácia

```
Settings → Tools → Claude Code
├── API Key: [configured via CLI]
├── Model: Sonnet
├── Auto-suggest: Enabled
└── Inline hints: Enabled
```

---

## Vim / Neovim

### Plugin (vim-claude)

```vim
" .vimrc alebo init.vim
Plug 'anthropics/vim-claude'

" Konfigurácia
let g:claude_model = 'sonnet'
let g:claude_auto_suggest = 1
```

### Keybindings

```vim
" Explain selection
vnoremap <leader>ce :ClaudeExplain<CR>

" Refactor selection
vnoremap <leader>cr :ClaudeRefactor<CR>

" Generate tests
nnoremap <leader>ct :ClaudeTests<CR>

" Open chat
nnoremap <leader>cc :ClaudeChat<CR>
```

### Commands

```vim
:ClaudeChat          " Otvorí chat buffer
:ClaudeExplain       " Vysvetlí výber
:ClaudeRefactor      " Refaktoruje výber
:ClaudeTests         " Generuje testy
:ClaudeFix           " Opraví chyby
```

---

## Emacs

### Package (claude-mode)

```elisp
;; init.el
(use-package claude-mode
  :ensure t
  :config
  (setq claude-model "sonnet")
  (global-claude-mode 1))
```

### Keybindings

```elisp
(global-set-key (kbd "C-c c c") 'claude-chat)
(global-set-key (kbd "C-c c e") 'claude-explain-region)
(global-set-key (kbd "C-c c r") 'claude-refactor-region)
(global-set-key (kbd "C-c c t") 'claude-generate-tests)
```

---

## Terminal Integration

### tmux

```bash
# .tmux.conf
# Claude v novom pane
bind-key C split-window -h "claude"

# Claude s kontextom
bind-key c send-keys "claude" Enter
```

### Zsh/Bash Aliases

```bash
# .zshrc / .bashrc

# Rýchly chat
alias cc="claude"

# Review aktuálneho súboru
alias ccr="claude 'Review this file' < "

# Generuj testy
alias cct="claude 'Generate tests for this file' < "

# Explain
alias cce="claude 'Explain this code' < "
```

---

## Project-specific IDE Config

### VS Code Workspace

```json
// .vscode/settings.json
{
  "claude-code.projectContext": {
    "framework": "Next.js",
    "language": "TypeScript",
    "testFramework": "Vitest"
  },
  "claude-code.customInstructions": [
    "Use @/ import alias",
    "Prefer server components",
    "Use Tailwind for styling"
  ]
}
```

### EditorConfig Integration

```ini
# .editorconfig
[*]
claude_enabled = true
claude_model = sonnet

[*.test.ts]
claude_prompt_prefix = "When generating tests, use Vitest"
```

---

## Workflow Examples

### VS Code + Claude Code

```
1. Otvorte súbor
2. Vyberte funkciu
3. Ctrl+I → "Add error handling"
4. Review → Accept
5. Ctrl+K Ctrl+T → Generate tests
6. Save all
```

### Terminal + IDE

```
1. Terminal: claude "Explain the auth flow"
2. IDE: Read explanation, navigate to files
3. Terminal: claude "Refactor to use JWT"
4. IDE: Review changes, test
5. Terminal: claude "Generate migration guide"
```

---

## Cvičenia

### Cvičenie 1: VS Code Setup
```
Nakonfigurujte VS Code s Claude Code extension
a custom keyboard shortcuts.
```

### Cvičenie 2: Tasks
```
Vytvorte VS Code tasks pre code review
a test generation.
```

### Cvičenie 3: Neovim
```
Nakonfigurujte Neovim s claude pluginom
a custom mappings.
```

---

## Ďalej

[Working Trees](20-working-trees.md)
