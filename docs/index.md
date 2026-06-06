# 📖 HostDoc

> Biblioteca personal de apuntes técnicos. Arquitecturas, estudios, proyectos, y conocimiento acumulado.

## Categorías

| Sección | Para qué |
|---------|----------|
| 📚 **Apuntes** | Notas de estudio, cursos, tecnologías, lenguajes |
| 🏗️ **Proyectos** | Documentación de proyectos propios (Agente, etc.) |
| 🧠 **Arquitectura** | Decisiones técnicas, diagramas, patrones |
| 📝 **Wiki** | Conocimiento general, soluciones, guías, referencia |

## Cómo usar

```bash
# Clonar
git clone git@github.com:Jerewergez/HostDoc.git
cd HostDoc

# Crear entorno
python3 -m venv .venv && source .venv/bin/activate
pip install mkdocs mkdocs-material

# Escribir en docs/
# ...

# Preview local
mkdocs serve

# Publicar
mkdocs gh-deploy
```

> Cada entrada es un archivo markdown. Simple, portable, sin bases de datos.
