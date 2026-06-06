# Workflows de Data Engineering

Este documento describe los workflows automatizados que Hermes ejecuta para tareas de data engineering.

---

## Workflow 1: Análisis Dinámico de Datos

### Disparador
Usuario envía un archivo (CSV, JSON, Excel, Parquet) por Discord DM.

### Flujo Completo

```
1. RECEPCIÓN
   Usuario → Discord → Hermes recibe archivo adjunto
   │
2. ALMACENAMIENTO
   Hermes guarda en ~/hermes/data/{timestamp}_{filename}
   │
3. ANÁLISIS PRELIMINAR
   Hermes inspecciona:
   ├── Formato y codificación
   ├── Esquema (columnas, tipos)
   ├── Estadísticas básicas (filas, nulos, únicos)
   └── Calidad (valores atípicos, duplicados)
   │
4. DECISIÓN (Hermes decide autónomamente)
   ├── "Crear tabla raw en PostgreSQL"
   │   → docker compose exec postgres psql ...
   │
   ├── "Generar modelo dbt bronze"
   │   → Crea dbt/models/bronze/{nombre}.sql
   │   → Ejecuta: docker compose run --rm dbt run --models bronze.{nombre}
   │
   ├── "Generar pipeline bronze → silver → gold"
   │   → Crea modelos en cascada
   │   → dbt run completo
   │
   └── "Solo análisis exploratorio"
       → Python inline para estadísticas y gráficos
   │
5. REPORTE
   Hermes envía resumen a Discord:
   ├── Schema detectado
   ├── Filas procesadas
   ├── Calidad de datos (% nulos, duplicados)
   ├── Query de ejemplo
   └── Enlace a documentación (si aplica)
```

### Ejemplo en Discord

```
Usuario: [sube ventas_mensuales.csv]

Hermes:
📊 Archivo recibido: ventas_mensuales.csv (2.3 MB)
✔ 15 columnas, 48,230 filas detectadas

📋 Schema:
  • fecha (date) — OK
  • producto (varchar) — 230 valores únicos
  • monto (decimal) — 4 nulos (0.008%)
  • región (varchar) — 5 valores únicos
  • vendedor (varchar) — 23 valores únicos

✅ Creé tabla raw_ventas_mensuales en PostgreSQL
✅ Generé modelo bronze_ventas → silver_ventas con limpieza
✅ Ejecutando dbt run...

📈 Resultados:
  • Total ventas: $2,340,500
  • Promedio x mes: $195,041
  • Top región: Buenos Aires (32%)

¿Qué más querés analizar?
```

---

## Workflow 2: Pipeline dbt Automatizado

### Disparador
Usuario ejecuta `/pipeline run <nombre>` en Discord.

### Flujo

```
1. Hermes busca pipeline definido en pipelines/{nombre}.yaml
2. Prepara entorno:
   ├── docker compose -f docker/data-eng/docker-compose.data-eng.yml
   │   run --rm dbt debug (verifica conexión)
3. Ejecuta dbt:
   ├── dbt seed (datos de referencia)
   ├── dbt run (modelos)
   ├── dbt test (calidad)
   └── dbt docs generate (documentación)
4. Reporta resultados en Discord
```

### Pipelines Definidos

| Pipeline | Descripción | Frecuencia |
|----------|-------------|------------|
| `daily_refresh` | Actualiza modelos gold con datos del día | Cada 24h |
| `full_refresh` | Reconstruye todos los modelos | Manual |
| `quality_check` | Ejecuta tests de calidad | Diario |
| `custom_analysis` | Análisis ad-hoc definido por usuario | Manual |

---

## Workflow 3: Monitoreo y Alertas

### Health Check (cada hora)
Hermes verifica:
- CPU, RAM, disco del VPS
- Estado del gateway Discord
- Servicios Docker corriendo
- Último commit en GitHub

### Alertas
Si algo falla, Hermes envía DM al usuario con el diagnóstico.

---

## Workflow 4: Deploy Automático

### Disparador
`git push` al repositorio GitHub.

### Detección
Hermes verifica cada 60s (via cron interno) si hay cambios en el remoto.

### Acción
```bash
cd ~/hermes
git pull
# Hermes evalúa qué cambió:
# - skills/ → recarga skills
# - config/ → actualiza configuración
# - scripts/ → verifica integridad
# - dbt/ → refresca modelos
```

---

## Workflow 5: Data Engineering como Servicio

### Comandos disponibles en Discord

| Comando | Descripción |
|---------|-------------|
| `/de run query <SQL>` | Ejecuta consulta SQL en PostgreSQL |
| `/de run dbt` | Ejecuta dbt run completo |
| `/de run dbt --models <selector>` | Ejecuta modelos específicos |
| `/de test` | Ejecuta dbt test |
| `/de docs` | Genera y muestra documentación dbt |
| `/de profile <tabla>` | Muestra perfil de datos de una tabla |
| `/de status` | Estado de servicios data-eng |
| `/de psql` | Terminal interactiva PostgreSQL |

---

## Próximos Workflows

### Orquestación con Airflow
- DAGs generados dinámicamente por Hermes
- Programación basada en eventos de datos
- Dependencias entre pipelines

### Notebooks Automáticos
- Hermes genera notebooks Jupyter de análisis
- Usuario descarga o visualiza en el VPS

### Alertas Inteligentes
- Hermes monitorea calidad de datos y alerta si detecta anomalías
- Umbrales configurables por columna
