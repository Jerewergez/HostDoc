# 🏠 HostDoc

> 📖 Documentación de mis proyectos — accesible desde cualquier PC.

## Cómo usar

```bash
git clone git@github.com:Jerewergez/HostDoc.git
cd HostDoc
```

Agregá documentación en `docs/` con formato markdown. Usá MkDocs para generar el sitio.

## Requisitos

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install mkdocs mkdocs-material
```

## Preview local

```bash
mkdocs serve
# http://localhost:8000
```

## Publicar

```bash
mkdocs gh-deploy
```

## Estructura sugerida

```
docs/
├── index.md        ← Página principal
├── wiki/           ← Conocimiento acumulativo
│   ├── guides/     ← Guías
│   ├── solutions/  ← Problemas resueltos
│   └── reference/  ← Referencia técnica
└── proyectos/      ← Documentación por proyecto
    └── agente/
```
