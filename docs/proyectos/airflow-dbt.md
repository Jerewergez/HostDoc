# Airflow_DBT — Medallion Architecture

> Pipeline de datos con **dbt Core + PostgreSQL 16** siguiendo la arquitectura medallón (Bronce → Plata → Oro). Procesa, limpia y modela datos analíticos de forma incremental y testeada.

## Stack

| Componente | Tecnología |
|------------|-----------|
| **Orquestación** | Apache Airflow |
| **Transformación** | dbt Core (adapter postgres) |
| **Base de datos** | PostgreSQL 16 |
| **Contenedores** | Docker Compose |
| **Lenguaje** | SQL + Python (Jinja macros) |

## Arquitectura Medallón (Bronze → Silver → Gold)

El pipeline organiza los datos en tres capas progresivas:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   BRONZE     │ ──▶ │   SILVER     │ ──▶ │    GOLD      │
│  (raw data)  │     │  (cleaned)   │     │  (business)   │
└──────────────┘     └──────────────┘     └──────────────┘
```

### 🟤 Bronze — Capa Raw

Datos tal cual llegan de las fuentes. Sin transformaciones, sin limpieza. Es el punto de entrada y la fuente de verdad inmutable para reprocesos.

```sql
-- Ejemplo: modelo bronze (carga directa desde seed)
{{ config(materialized='table', schema='bronze') }}

SELECT
    id,
    raw_payload,
    _loaded_at
FROM {{ source('source_system', 'raw_table') }}
```

- `materialized: table` — se regenera completa en cada carga
- `schema: bronze` — esquema dedicado en PostgreSQL

### ⚪ Silver — Capa Limpia

Datos deduplicados, tipados y validados. Se aplican reglas de negocio generales: casting de tipos, manejo de nulos, estandarización de formatos.

```sql
-- Ejemplo: modelo silver (limpieza y tipado)
{{ config(materialized='table', schema='silver') }}

SELECT
    id::BIGINT,
    COALESCE(name, 'unknown') AS name,
    created_at::TIMESTAMP,
    status,
    ROW_NUMBER() OVER (PARTITION BY id ORDER BY _loaded_at DESC) = 1 AS is_latest
FROM {{ ref('bronze_raw_table') }}
WHERE id IS NOT NULL
```

### 🟡 Gold — Capa de Negocio

Modelos listos para consumo: métricas agregadas, KPIs, tablas de hechos y dimensiones. Optimizadas para dashboards, reportes y análisis.

```sql
-- Ejemplo: modelo gold (métrica de negocio)
{{ config(materialized='table', schema='gold') }}

SELECT
    DATE_TRUNC('day', created_at) AS day,
    status,
    COUNT(*) AS total_records,
    COUNT(DISTINCT user_id) AS unique_users
FROM {{ ref('silver_cleaned_table') }}
GROUP BY 1, 2
```

## Infraestructura

### Docker Compose

```yaml
services:
  postgres:
    image: postgres:16
    container_name: agente-postgres
    environment:
      POSTGRES_DB: analytics
      POSTGRES_USER: hermes
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}

  dbt:
    image: ghcr.io/dbt-labs/dbt-postgres:latest
    container_name: agente-dbt
    volumes:
      - ./dbt:/dbt
      - ./configs:/profiles
    working_dir: /dbt
    entrypoint: ["dbt"]
```

### Esquemas en PostgreSQL

| Schema | Capa | Propósito |
|--------|------|-----------|
| `bronze` | 🟤 Bronce | Datos crudos, sin transformar |
| `silver` | ⚪ Plata | Datos limpios y tipados |
| `gold` | 🟡 Oro | Métricas y modelos de negocio |
| `seeds` | 🌱 Semillas | Datos de referencia (CSV cargados) |

## Pipeline de Datos

### dbt Project Structure

```
dbt/
├── dbt_project.yml        # Config del proyecto (schemas, materializaciones)
├── models/
│   ├── bronze/            # Modelos de capa raw
│   ├── silver/            # Modelos de capa limpia
│   └── gold/              # Modelos de capa de negocio
├── macros/                # Macros Jinja reutilizables
├── seeds/                 # Archivos CSV de referencia
├── tests/                 # Tests de calidad (singular + generic)
└── analyses/              # Consultas de análisis exploratorio
```

### Flujo de Ejecución

```
1. Seeds     ──▶ Cargar datos de referencia a schema seeds
2. Bronze    ──▶ Crear/actualizar tablas raw en schema bronze
3. Silver    ──▶ Limpiar y transformar datos → schema silver
4. Gold      ──▶ Calcular métricas de negocio → schema gold
5. Tests     ──▶ Validar calidad de datos en todas las capas
```

### Pipelines Automatizados

| Pipeline | Schedule | Descripción |
|----------|----------|-------------|
| `daily_refresh` | 6:00 AM | Actualiza modelos gold con datos del día |
| `full_refresh` | Manual | Reconstrucción completa de todas las capas |
| `quality_check` | Post-run | Tests de calidad sobre datos transformados |

## Conexión y Configuración

### profiles.yml

```yaml
analytics:
  target: dev
  outputs:
    dev:
      type: postgres
      threads: 4
      host: postgres
      port: 5432
      user: hermes
      password: "{{ env_var("POSTGRES_PASSWORD") }}"
      dbname: analytics
      schema: analytics_dev
    prod:
      type: postgres
      threads: 8
      host: postgres
      port: 5432
      user: hermes
      password: "{{ env_var("POSTGRES_PASSWORD") }}"
      dbname: analytics
      schema: analytics_prod
```

## Decisiones Técnicas

- **dbt en vez de SQL scripts sueltos**: testing integrado, documentación automática, lineage tracking, y reutilización de modelos vía `ref()` y `source()`.
- **PostgreSQL en vez de una warehouse**: simplicidad operativa. El VPS no necesita un cluster de Snowflake ni BigQuery. PG 16 con índices y particionado alcanza para el volumen actual.
- **Docker Compose sobre instalación nativa**: aislamiento de dependencias, mismo entorno en dev y prod, replicable en cualquier máquina.
- **Carga incremental preferida sobre full refresh**: los modelos gold son materializados como `table` pero pueden migrarse a `incremental` cuando el volumen crezca.

## Comandos Útiles

```bash
# Iniciar servicios
cd ~/hermes/docker/data-eng
docker compose -f docker-compose.data-eng.yml up -d postgres

# Ejecutar dbt
docker compose -f docker-compose.data-eng.yml run --rm dbt debug
docker compose -f docker-compose.data-eng.yml run --rm dbt seed
docker compose -f docker-compose.data-eng.yml run --rm dbt run
docker compose -f docker-compose.data-eng.yml run --rm dbt test

# Solo modelos de una capa
docker compose -f docker-compose.data-eng.yml run --rm dbt run --models gold.*

# Generar documentación
docker compose -f docker-compose.data-eng.yml run --rm dbt docs generate
```

## Referencias

- [dbt Documentation](https://docs.getdbt.com/)
- [Medallion Architecture (Databricks)](https://www.databricks.com/glossary/medallion-architecture)
- [Airflow_DBT — GitHub](https://github.com/Jerewergez/Airflow_DBT)
- [Agente — Repo principal](https://github.com/Jerewergez/hermes)
