# 🧠 Wiki — Base de Conocimiento

> Documentación acumulativa del proyecto **Agente**. Cada solución, guía, configuración o aprendizaje se guarda acá para referencia futura.

## Estructura

| Directorio | Contenido |
|---|---|
| `guides/` | Guías y tutoriales paso a paso |
| `solutions/` | Problemas resueltos (síntoma → causa → solución) |
| `reference/` | Referencia técnica (comandos, configs, arquitectura) |
| `topics/` | Temas generales y documentación de dominio |

## Cómo agregar conocimiento

### Opción 1 — Desde Discord (recomendado)

```
/pi creá docs/wiki/solutions/<nombre>.md con:
- Título del problema
- Síntoma
- Causa
- Solución
- Comandos usados
```

### Opción 2 — Desde el repo

```bash
cp docs/wiki/template.md docs/wiki/solutions/mi-nueva-entrada.md
# Editar con contenido
```

### Opción 3 — Exportar memorias de Engram

```bash
engram obsidian-export --vault docs/wiki/ --project hermes
```

## Índice

### Guías
- [Uso de la wiki](guides/wiki-usage.md) — Cómo mantener esta wiki

### Soluciones
*Vacío — agregá la primera solución con `/pi`*

### Referencia
- [Comandos Discord](reference/commands.md) — Todos los comandos disponibles

---

> 💡 **Tip:** Cuanto más alimentés la wiki, más rápido vas a resolver problemas en el futuro. Hermes + pi pueden leerla y usarla como contexto.
