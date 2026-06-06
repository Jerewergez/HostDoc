# Docker Toolchain para Data Engineering

## Filosofía

Docker no es para correr Hermes. Docker es una **herramienta gestionada por Hermes** para ejecutar tareas de data engineering de forma aislada, reproducible y descartable.

Cada servicio es un contenedor especializado que Hermes levanta bajo demanda cuando necesita ejecutar una tarea específica.

## Servicios Disponibles

### PostgreSQL 16

Base de datos analítica para stage intermedio y consultas.

```yaml
postgres:
  image: postgres:16
  environment:
    POSTGRES_DB: analytics
    POSTGRES_USER: hermes
    POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
  volumes:
    - postgres_data:/var/lib/postgresql/data
  ports:
    - "5432:5432"
  healthcheck:
    test: ["CMD-SHELL", "pg_isready -U hermes -d analytics"]
    interval: 10s
```

**Uso desde Hermes:**
```
Cargar datos:      docker compose exec -T postgres psql -U hermes -d analytics < data.sql
Consultar:         docker compose exec -T postgres psql -U hermes -d analytics -c "SELECT * FROM ..."
Importar CSV:      docker compose exec -T postgres psql -U hermes -d analytics -c "\COPY ..."
```

### dbt Core

Transformaciones SQL con dbt, conectado a PostgreSQL.

```yaml
dbt:
  image: ghcr.io/dbt-labs/dbt-postgres:latest
  environment:
    DBT_PROFILES_DIR: /profiles
  volumes:
    - ../../dbt:/dbt
    - ./configs:/profiles
  working_dir: /dbt
  entrypoint: ["dbt"]
```

**Uso desde Hermes:**
```
dbt run:         docker compose run --rm dbt run
dbt test:        docker compose run --rm dbt test
dbt docs:        docker compose run --rm dbt docs generate
dbt debug:       docker compose run --rm dbt debug
```

### Próximos Servicios

#### Airflow (orquestación)
Pipeline programado con DAGs generados dinámicamente.

#### Jupyter (notebooks)
Entorno interactivo para análisis exploratorio.

#### MinIO (object storage)
Almacenamiento estilo S3 para datasets grandes y backups.

---

## Configuración de Red

Todos los servicios comparten una red Docker llamada `data-eng-net`:

```yaml
networks:
  data-eng-net:
    driver: bridge
```

PostgreSQL es accesible desde otros contenedores por nombre de host `postgres`.

---

## Gestión desde Hermes

### Skills Data Engineering

Se agregarán skills específicos a Hermes:

- **`de`** — Comandos generales de data engineering
- **`dbt`** — Gestión de modelos dbt
- **`sql`** — Consultas SQL interactivas

### Ejemplo: Hermes ejecuta dbt

Cuando un usuario pide "corré el pipeline de ventas", Hermes:

1. Verifica que PostgreSQL esté corriendo (`docker compose ps`)
2. Si no está, lo levanta (`docker compose up -d postgres`)
3. Ejecuta dbt: `docker compose run --rm dbt run --models sales_*`
4. Captura stdout/stderr
5. Analiza el resultado (errores, warnings, tablas creadas)
6. Reporta en Discord

---

## Perfiles dbt

El archivo `docker/data-eng/configs/profiles.yml` define los perfiles de conexión:

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
      password: "{{ env_var(POSTGRES_PASSWORD) }}"
      dbname: analytics
      schema: analytics_dev
    prod:
      type: postgres
      threads: 8
      host: postgres
      port: 5432
      user: hermes
      password: "{{ env_var(POSTGRES_PASSWORD) }}"
      dbname: analytics
      schema: analytics_prod
```

---

## Comandos Útiles

```bash
# Estado de todos los servicios
docker compose -f docker/data-eng/docker-compose.data-eng.yml ps

# Logs de un servicio
docker compose -f docker/data-eng/docker-compose.data-eng.yml logs -f postgres

# Ejecutar comando ad-hoc en PostgreSQL
docker compose -f docker/data-eng/docker-compose.data-eng.yml exec -T postgres \
  psql -U hermes -d analytics -c "SELECT table_name FROM information_schema.tables WHERE table_schema = public"

# Backup de PostgreSQL
docker compose -f docker/data-eng/docker-compose.data-eng.yml exec -T postgres \
  pg_dump -U hermes analytics > backup_$(date +%Y%m%d).sql

# Restaurar backup
cat backup.sql | docker compose -f docker/data-eng/docker-compose.data-eng.yml exec -T postgres \
  psql -U hermes analytics

# Reset completo de servicios
docker compose -f docker/data-eng/docker-compose.data-eng.yml down -v
docker compose -f docker/data-eng/docker-compose.data-eng.yml up -d
```

## Límites de Recursos

Cada servicio tiene límites definidos en `docker-compose.data-eng.yml`:

```yaml
deploy:
  resources:
    limits:
      cpus: "1"
      memory: 1G
    reservations:
      cpus: "0.5"
      memory: 512M
```

Esto asegura que los contenedores de data engineering no compitan con Hermes por recursos del VPS.
