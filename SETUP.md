# Guía de Setup

## Requisitos

- VPS con Ubuntu 24.04 (4 cores, 8GB RAM, 150GB SSD recomendado)
- Docker y Docker Compose
- Cuenta de Discord (para el bot)
- Cuenta en OpenCode Go (para API key del modelo)

## 1. Provisionar el VPS

### 1.1 Docker
```bash
# Verificar Docker
docker --version
docker compose version

# Si no está instalado:
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
newgrp docker
```

### 1.2 Clonar el Repo
```bash
# Configurar SSH key para GitHub (si no existe)
ssh-keygen -t ed25519 -C "tu-email@ejemplo.com"
cat ~/.ssh/id_ed25519.pub
# Agregar la clave en https://github.com/settings/keys

# Clonar
git clone git@github.com:Jerewergez/Agente.git ~/hermes
cd ~/hermes
```

### 1.3 Configurar .env
```bash
cp docker/env.template .env
nano .env
```

Variables requeridas:
```
DISCORD_BOT_TOKEN=<token-de-tu-bot>
DISCORD_ALLOWED_USERS=<tu-discord-user-id>
OPENCODE_GO_API_KEY=<tu-api-key>
```

### 1.4 Instalar Hermes Agent
```bash
# El instalador oficial de Hermes
git clone https://github.com/NousResearch/hermes-agent.git ~/.hermes/hermes-agent
cd ~/.hermes/hermes-agent
./install.sh
# Siga las instrucciones del instalador
```

## 2. Configurar Bot de Discord

### 2.1 Crear el Bot
1. Ir a https://discord.com/developers/applications
2. New Application → nombre: "Agente"
3. Bot → Add Bot
4. Copiar el token

### 2.2 Privilegios del Bot
En la página del bot, habilitar:
- **Privileged Gateway Intents**:
  - ✅ MESSAGE CONTENT INTENT
  - ✅ SERVER MEMBERS INTENT (opcional)
- **Bot Permissions**:
  - Send Messages
  - Read Message History
  - Attach Files
  - Embed Links
  - Use Slash Commands

### 2.3 Invitar el Bot
Usar el OAuth2 URL Generator con scope `bot` y `applications.commands`.

## 3. Configurar Hermes

### 3.1 Config YAML
Editar `~/.hermes/config.yaml`:

```yaml
model: opencode-go/deepseek-v4-flash

discord:
  token: ${DISCORD_BOT_TOKEN}
  allowed_users:
    - ${DISCORD_ALLOWED_USERS}

platforms:
  discord:
    enabled: true
```

### 3.2 Iniciar Hermes
```bash
cd ~/.hermes/hermes-agent
source venv/bin/activate
python -m hermes_cli.main gateway run --replace
```

Para que corra como servicio systemd:
```bash
# Hermes ya incluye integración systemd --user
systemctl --user enable hermes-gateway
systemctl --user start hermes-gateway
```

## 4. Iniciar Servicios Docker de Data Engineering

```bash
cd ~/hermes/docker/data-eng
docker compose -f docker-compose.data-eng.yml up -d

# Verificar
docker compose -f docker-compose.data-eng.yml ps
```

## 5. Verificar Instalación

### En Discord
Enviar mensaje directo al bot:
```
/status
```

Respuesta esperada:
```
✅ Gateway conectado como RichardW#8567
📊 71 skills cargados
🐳 Docker disponible (29.5.3)
💾 Engram activo (1.16.1)
```

## Troubleshooting

### El bot no responde
```bash
# Verificar que el gateway está corriendo
ps aux | grep hermes

# Revisar logs
tail -f ~/.hermes/logs/gateway.log
tail -f ~/.hermes/logs/errors.log
```

### Discord: PrivilegedIntentsRequired
Ir a Discord Developer Portal → Bot → habilitar MESSAGE CONTENT INTENT.

### Docker permission denied
```bash
sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar
```

### Error de conexión a GitHub
```bash
ssh -T git@github.com
# Si falla, verificar clave SSH en ~/.ssh/
