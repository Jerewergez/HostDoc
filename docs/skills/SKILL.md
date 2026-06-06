# VPS

Monitor and manage the VPS server. Check system status, disk usage, memory, and running processes.

## When to use
- The user asks about server status, health, or performance
- The user wants to check disk space, memory, CPU, or uptime
- The user asks to deploy or update the Hermes configuration

## Steps

### Status check
1. User says: "status", "health", "server ok", "qué tal el vps"
2. Run these commands and report the results:
   - `echo "=== UPTIME ===" && uptime`
   - `echo "=== DISK ===" && df -h /`
   - `echo "=== RAM ===" && free -h`
   - `echo "=== CPU ===" && top -bn1 | grep "Cpu(s)"`
   - `echo "=== DOCKER ===" && docker ps --format "table {{.Names}}\t{{.Status}}" 2>/dev/null || echo "no docker"`
3. Summarize in a friendly way

### Process list
1. User says: "processes", "procesos", "what is running"
2. Run: `ps aux --sort=-%mem | head -15`
3. Report the top processes

### Docker status
1. User says: "docker", "containers"
2. Run: `docker ps -a --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"`
3. Report container statuses

### Disk alert
- If disk usage is above 80%, warn the user
- If disk usage is above 90%, recommend action
