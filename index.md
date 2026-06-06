# 🏠 HostDoc

> 📖 **Documentación viva del proyecto Agente.**
> Este repositorio contiene la wiki, guías, referencia de comandos, arquitectura y documentación técnica.
> Todo en markdown, accesible desde cualquier PC.

## Cómo usar este repo

### Opción 1 — GitHub Pages (recomendada)

```
https://jerewergez.github.io/HostDoc/
```
Navegación completa con buscador, desde cualquier browser.

### Opción 2 — Clonar + MkDocs

```bash
git clone git@github.com:Jerewergez/HostDoc.git
cd HostDoc
python3 -m venv .venv
source .venv/bin/activate
pip install mkdocs mkdocs-material
mkdocs serve
# http://localhost:8000
```

### Opción 3 — Leer en GitHub

Cada archivo markdown se puede leer directamente desde la interfaz de GitHub.

## Navegación rápida

| Sección | Descripción |
|---------|-------------|
| [Comandos Discord](wiki/reference/commands.md) | Todos los comandos disponibles |
| [Wiki](wiki/README.md) | Base de conocimiento acumulativa |
| [Arquitectura](ARCHITECTURE.md) | Arquitectura del sistema |
| [Gentle-AI](GENTLE-AI.md) | Gestor de agentes |

## Stack

- **Agente:** Hermes Agent (Nous Research) · Discord · DeepSeek V4 Flash
- **Memoria:** Engram · PostgreSQL · Cloud Sync
- **Toolchain:** Pi · Gentle-AI · Docker · PostgreSQL 16
- **Docs:** MkDocs + Material Theme
