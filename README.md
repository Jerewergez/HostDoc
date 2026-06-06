# 🏠 HostDoc

> 📖 Documentación viva del proyecto **Agente** — accesible desde cualquier PC.

## 🌐 Sitio web

```
https://jerewergez.github.io/HostDoc/
```

Navegación completa con buscador, dark/light mode, desde cualquier navegador.

## 💻 Local (cualquier PC con Python)

```bash
git clone git@github.com:Jerewergez/HostDoc.git
cd HostDoc
python3 -m venv .venv
source .venv/bin/activate
pip install mkdocs mkdocs-material
mkdocs serve
# Abrí http://localhost:8000
```

## 📂 Estructura

```
docs/
├── index.md              ← Página principal
├── ARCHITECTURE.md       ← Arquitectura del sistema
├── GENTLE-AI.md          ← Gestor de agentes
├── DOCKER-TOOLCHAIN.md   ← Toolchain Docker
├── WORKFLOWS.md          ← Workflows
├── SETUP.md              ← Guía de instalación
├── wiki/                 ← Base de conocimiento acumulativa
│   ├── README.md         ← Índice
│   ├── guides/           ← Guías
│   ├── reference/        ← Referencia técnica
│   └── template.md       ← Template para nuevas entradas
└── skills/               ← Skills de Hermes
    ├── vps/SKILL.md
    ├── pi/SKILL.md
    ├── deploy/SKILL.md
    └── ...
```

## 📝 Cómo agregar contenido

```bash
# Desde cualquier PC con el repo clonado:
cp docs/wiki/template.md docs/wiki/solutions/mi-solucion.md
# Editar, commit, push
git add -A && git commit -m "docs: nueva entrada" && git push

# Actualizar el sitio:
mkdocs gh-deploy
```
