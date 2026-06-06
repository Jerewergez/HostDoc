# Pi — Coding Agent

Pi is a coding AI assistant that can read, edit, and write files, run commands, and interact with the filesystem. Two modes: direct pi invocation for coding tasks, or Hermes-assisted mode for quick questions.

## When to use
- The user says: `/pi`, "pi", "coding assistant", "code", "codigo", "programa"
- The user needs to read, edit, or create files on the server
- The user wants to run diagnostic commands, check logs, or inspect code
- The user asks about project structure, dependencies, or configuration

## Steps

### 0. Setup: model provider
Pi uses **OpenCode Go** (OpenAI-compatible) via `~/.pi/agent/models.json`. The same API key Hermes uses (`OPENCODE_GO_API_KEY`) is configured. Provider: `opencode-go`, model: `deepseek-v4-flash`.

Verify pi works:
```
export PATH=$PATH:$HOME/.local/bin:$HOME/.npm-global/bin
pi --print "test"  # should respond
```

### 1. Run pi as coding assistant
User says: `/pi <query>` or "pi <query>"

Run pi directly with the user's prompt:
```
export PATH=$PATH:$HOME/.local/bin:$HOME/.npm-global/bin
timeout 120 pi --provider opencode-go --model opencode-go/deepseek-v4-flash --print "<query>"
```

Pi has access to:
- Full filesystem under `/home/botuser/`
- Git, git commands
- Docker (via CLI, not inside container)
- npm, node, python3
- All project files at `/home/botuser/hermes/`

### 2. Context: read and explain
If the user wants quick info without waiting for pi's full response, read files directly:
```
cat <file> | head -<lines>
```
Show relevant sections and explain.

### 3. Grouped commands
For multi-step work, use pi (it handles tool orchestration better):
```
export PATH=$PATH:$HOME/.local/bin:$HOME/.npm-global/bin
timeout 120 pi --provider opencode-go --model opencode-go/deepseek-v4-flash --print "<multi-step request>"
```

### 4. Summary
After any operation, report:
- What was done
- What files were affected
- Any warnings or notes
- Next steps if applicable

## Notes
- Long-running pi tasks timeout at 120 seconds; for complex tasks, break into smaller prompts.
- Pi shares the same model + API key as Hermes (OpenCode Go / DeepSeek V4 Flash).
- Engram memory is available to pi via gentle-engram package.
- gentle-ai is installed: `gentle-ai doctor` to check health.
