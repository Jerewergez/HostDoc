# Arquitectura del Sistema

## Visión General

El sistema Agente está compuesto por tres capas principales que trabajan juntas para proveer un asistente de data engineering inteligente.

## Capa 1: Hermes Agent (Nativo)

### Proceso Principal
Hermes Agent corre como un proceso nativo en el VPS, gestionado por systemd (user service). El gateway se conecta a Discord y escucha comandos en tiempo real.

```
PID: 119243
Runtime: Python 3.11 (venv)
Path: ~/.hermes/hermes-agent/
Config: ~/.hermes/config.yaml
Estado: ✅ Running (Discord conectado como RichardW#8567)
```

### Componentes Internos
- **Gateway**: Maneja conexiones de plataforma (Discord), sesiones, y enrutamiento de mensajes
- **Agent Loop**: Procesa cada mensaje entrante (turno de conversación)
- **Skill Engine**: Carga y ejecuta skills desde `~/.hermes/skills/`
- **Kanban Board**: Sistema de tareas interno con persistencia SQLite
- **Cron Scheduler**: Ejecuta tareas programadas cada 60s

### Persistencia
- `~/.hermes/sessions/` — Sesiones de conversación
- `~/.hermes/state.db` — Estado del agente (SQLite)
- `~/.hermes/kanban.db` — Tablero de tareas (SQLite)
- `~/.hermes/memories/` — Memoria persistente
- `~/.hermes/logs/` — Logs de gateway, agente y errores

## Capa 2: Docker Toolchain

### Propósito
Contenedores especializados para tareas de data engineering que Hermes gestiona bajo demanda.

### Servicios
| Servicio | Puerto | Persistencia |
|----------|--------|-------------|
| PostgreSQL | 5432 | `docker/data-eng/volumes/postgres/` |
| dbt Core | CLI | Imagen con dbt + drivers |
| Airflow (próximo) | 8080 | `docker/data-eng/volumes/airflow/` |

### Gestión desde Hermes
Hermes puede ejecutar comandos Docker directamente:
```bash
# Ejemplo: dbt run
docker compose -f ~/hermes/docker/data-eng/docker-compose.data-eng.yml run --rm dbt run

# Ejemplo: consultar PostgreSQL
docker compose -f ~/hermes/docker/data-eng/docker-compose.data-eng.yml exec -T postgres psql -U hermes -d analytics -c "SELECT * FROM my_table"
```

## Capa 3: GitHub Integration

### Flujo de Deploy
```
Local: git push → GitHub
  │
  ▼
VPS: Hermes detecta cambios (polling cada 60s o webhook)
  │
  ▼
Hermes ejecuta: cd ~/hermes && git pull
  │
  ▼
Hermes evalúa cambios en skills/ y configuración
  │
  ▼
Hermes recarga skills si es necesario
```

## Flujo de Datos en Análisis Dinámico

```
Usuario sube archivo (CSV/JSON/Excel) a Discord
  │
  ▼
Hermes recibe → guarda en ~/hermes/data/
  │
  ▼
Hermes analiza: esquema, tipos, tamaño, calidad
  │
  ▼
Hermes decide estrategia:
  ├── Genera modelo bronze (raw) en PostgreSQL
  ├── Genera modelo silver (limpiado) en dbt
  ├── Ejecuta transformaciones
  └── Devuelve resumen + estadísticas
  │
  ▼
Usuario recibe resultados en Discord
  │
  ▼
(Opcional) Hermes persiste pipeline como skill reutilizable
```

## Seguridad

### Principios
1. **No privilegios**: Hermes corre como `botuser`, sin sudo
2. **Tokens seguros**: En `.env` (gitignorado), redactados en logs por Hermes
3. **Aislamiento**: Cada servicio Docker tiene sus propios límites de recursos
4. **Backup**: Volúmenes Docker persistentes, configuración en Git
