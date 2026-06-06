# Gentle-AI — Gestor de Agentes (por defecto)

Gentle-AI es el ecosistema configurador oficial del proyecto **Agente**. Gestiona la instalación, configuración y orquestación multi-agente.

## Estado Actual

| Componente | Agente | Gestionado por |
|------------|--------|---------------|
| 🧠 Hermes Agent | Native (Discord bot) | Manual + gentle-ai CLI |
| 💾 Engram Memory | MCP server | gentle-ai (provisionado) |
| 🐳 Docker Toolchain | Docker Compose | Hermes skills |
| 💬 Pi Agent | Local (dev) | gentle-ai + gentle-pi |

## Instalación

### VPS
```bash
# Ya instalado en ~/.local/bin/gentle-ai
export PATH="$HOME/.local/bin:$PATH"
gentle-ai --version
```

### Local (macOS/Linux)
```bash
curl -fsSL https://raw.githubusercontent.com/Gentleman-Programming/gentle-ai/main/scripts/install.sh | bash
```

## Comandos Útiles

| Comando | Descripción |
|---------|-------------|
| `gentle-ai doctor` | Health check del ecosistema |
| `gentle-ai install --agent pi` | Instalar/configurar Pi agent |
| `gentle-ai skill-registry refresh` | Refrescar registro de skills |
| `gentle-ai install --dry-run` | Simular instalación |

## Multi-Agente

Gentle-AI soporta 15+ agentes con distintos modelos de delegación:

| Modelo | Agentes |
|--------|---------|
| **Full (sub-agentes)** | Claude Code, OpenCode, Pi, Cursor, VS Code Copilot |
| **Solo-agent** | Codex, Windsurf, Antigravity |
| **Multi-mode SDD** | OpenCode, Kilo Code, Kiro IDE, Pi |

Para el proyecto Agente, el flujo multi-agente es:

```
Pi (local) ── SSH ──▶ VPS ──▶ Hermes (Discord)
  │                             │
  └── gentle-ai                 └── gentle-ai CLI
       │                             │
       └── Engram (memoria compartida entre agentes)
```

## Integración con Hermes

Desde Hermes en Discord, se puede invocar gentle-ai a través del skill `system`:

```
/skill system gentle-ai doctor
/skill system gentle-ai skill-registry refresh
```

## Referencias

- [Gentle-AI GitHub](https://github.com/Gentleman-Programming/gentle-ai)
- [Gentle-Pi (Pi harness)](https://www.npmjs.com/package/gentle-pi)
- [Documentación de agentes](https://github.com/Gentleman-Programming/gentle-ai/blob/main/docs/agents.md)
