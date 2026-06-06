# Gentle (gentle)

Manage the Gentle-AI ecosystem: health checks, skill registry, agent configuration, and multi-agent orchestration.

## When to use
- The user says: "gentle", "gentle-ai", "gestor de agentes", "multi-agente", "health check", "doctor"
- The user wants to check the ecosystem health or update agent configurations
- The user asks about multi-agent workflows or orchestration

## Steps

### 1. Run gentle-ai doctor
User says: "gentle doctor", "gentle health", "gentle status"

1. Run: `export PATH=$HOME/.local/bin:$PATH && gentle-ai doctor 2>&1`
2. Report the results, focusing on:
   - Any failed checks
   - Available tools
   - Engram reachability

### 2. Check gentle-ai version
User says: "gentle version", "que version de gentle"

1. Run: `gentle-ai --version 2>&1`
2. Report version

### 3. Skill registry
User says: "gentle skills", "gentle skill-registry"

1. Run: `gentle-ai skill-registry refresh 2>&1 && echo "---" && cat ~/hermes/.atl/skill-registry.md 2>/dev/null | head -30 || echo "No registry yet"`
2. Report available skills

### 4. Agent status
User says: "gentle agents", "gentle agentes"

1. Check available agents on the system:
   ```
   echo "Installed:"
   which pi 2>/dev/null && pi --version 2>/dev/null || echo "  pi: no"
   which gentle-ai 2>/dev/null && gentle-ai --version 2>/dev/null || echo "  gentle-ai: no"
   which engram 2>/dev/null && engram --version 2>/dev/null || echo "  engram: no"
   ```
2. Report which agents are available and their versions

### Notes
- gentle-ai is in ~/.local/bin/gentle-ai
- The PATH needs $HOME/.local/bin for it to work
- gentle-ai doctor does NOT need admin/sudo
