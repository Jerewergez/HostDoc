# Referencia de Comandos

## Discord

### Comandos integrados (Hermes)
| Comando | Descripción |
|---------|-------------|
| `/new` | Nueva conversación |
| `/model` | Cambiar modelo |
| `/status` | Estado del sistema |
| `/help` | Ayuda general |

### Skills personalizados
| Skill | Cómo invocar | Descripción |
|-------|-------------|-------------|
| `vps` | `vps status` o `/skill vps status` | Monitoreo del servidor |
| `deploy` | `deploy` o `actualizar` | Deploy desde GitHub |
| `tasks` | `tasks add <text>` o `tasks list` | Kanban de tareas |
| `daily` | `daily` o `resumen` | Resumen/organización diaria |
| `gym` | `gym log <ejercicio>` | Registro de gimnasio |
| `de` | `de run <modelo>` | Data engineering |
| `pi` | `pi <consulta>` | Coding assistant |
| `gentle` | `gentle doctor` | Gentle-AI management |
| `audit` | `audit` | Auditoría de seguridad |

## Local (CachyOS)

### Engram
```bash
engram sync --cloud --project hermes        # Sincronizar memorias
engram stats --project hermes                # Estadísticas de memoria
engram search <query>                        # Buscar en memoria
engram doctor                                # Diagnóstico
```

### Git
```bash
git add -A && git commit -m "mensaje" && git push   # Deploy rápido
```

### SSH tunnel
```bash
ssh -L 7437:localhost:8080 -N -f botuser@13.140.134.254
```

## VPS (SSH)

### Servicios
```bash
systemctl status engram-cloud.service        # Estado cloud serve
docker ps                                     # Contenedores activos
```

### Scripts
```bash
~/hermes/scripts/start-cloud-serve.sh        # Arrancar cloud serve
~/hermes/scripts/sync-memories.sh            # Sincronizar memorias
```
