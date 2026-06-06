# Data Engineering (de)

Run data engineering tasks: SQL queries, dbt models, pipeline management, and data analysis. Uses the Docker toolchain (PostgreSQL + dbt).

## When to use
- The user asks to run SQL queries, dbt commands, or data pipelines
- The user uploads a file and asks to analyze or process it
- The user asks about database status or schema
- The user wants to run or test data transformations
- The user says: "de", "data", "pipeline", "sql", "dbt", "analizar", "analiza", "query", "consulta"

## Tools available
- PostgreSQL 16 (Docker container: agente-postgres)
  - Host: localhost / Container: postgres
  - User: hermes / DB: analytics
  - Schemas: bronze (raw), silver (cleaned), gold (business), seeds (reference)
- dbt Core (Docker image: ghcr.io/dbt-labs/dbt-postgres)
  - Project: ~/hermes/dbt/
  - Profiles: ~/hermes/docker/data-eng/configs/profiles.yml

## Steps

### 0. Check toolchain status
User says: "de status", "toolchain status", "servicios data"

1. Run: cd ~/hermes/docker/data-eng && docker compose -f docker-compose.data-eng.yml ps --format "table {{.Name}}\t{{.Status}}"
2. Check if any tables exist: docker compose exec -T postgres psql -U hermes -d analytics -c "\dt bronze.*" 2>/dev/null
3. Report:
   - PostgreSQL: healthy / not running
   - dbt: image available / not pulled
   - Disk usage of postgres volume

### 1. Run SQL query
User says: "de query <sql>", "de sql <query>", "corre esta query", "consulta"

1. Sanitize: ensure the query is read-only or explicitly allowed
2. Run: cd ~/hermes/docker/data-eng && docker compose exec -T postgres psql -U hermes -d analytics -c "<query>"
3. Format the output as a Discord-friendly table
4. If query takes >10s, warn the user

### 2. Run dbt
User says: "de dbt run", "de dbt run --models X", "de dbt test", "corre dbt"

Sub-commands:
- "de dbt run" -> docker compose -f docker-compose.data-eng.yml run --rm dbt run
- "de dbt run --models <selector>" -> docker compose -f docker-compose.data-eng.yml run --rm dbt run --models <selector>
- "de dbt test" -> docker compose -f docker-compose.data-eng.yml run --rm dbt test
- "de dbt test --models <selector>" -> docker compose -f docker-compose.data-eng.yml run --rm dbt test --models <selector>
- "de dbt debug" -> docker compose -f docker-compose.data-eng.yml run --rm dbt debug
- "de dbt docs" -> docker compose -f docker-compose.data-eng.yml run --rm dbt docs generate

1. Confirm with user before running (dbt run can take time)
2. Execute the command
3. Report: success with summary or error with relevant log lines

### 3. Profile a table
User says: "de profile <tabla>", "de analizar <tabla>", "perfil"

1. Run: docker compose exec -T postgres psql -U hermes -d analytics -c "SELECT COUNT(*) as total_rows FROM <tabla>;"
2. Run: docker compose exec -T postgres psql -U hermes -d analytics -c "SELECT column_name, data_type FROM information_schema.columns WHERE table_name = '<tabla>' ORDER BY ordinal_position;"
3. Report schema, row count, and sample data (5 rows)

### 4. List tables
User says: "de tables", "de tablas", "que tablas hay"

1. Run: docker compose exec -T postgres psql -U hermes -d analytics -c "\dt *."
2. Report schema-qualified table list

### 5. Pipeline management
User says: "de pipeline run <nombre>", "de run <pipeline>"

1. Check if pipeline file exists: ls ~/hermes/pipelines/<nombre>.yaml
2. If yes, read the pipeline steps and execute them sequentially
3. If no, list available pipelines

### General notes
- Always use cd ~/hermes/docker/data-eng && prefix for docker-compose commands
- For docker compose exec, use -T (no TTY) for non-interactive commands
- If a command fails, show the last 5 lines of output
- Be careful with long-running queries — set a mental timeout of 30s max
