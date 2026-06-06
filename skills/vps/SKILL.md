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

### 6. Token consumption + Engram memory
User says: "tokens", "memoria", "uso", "consumo", "usage", "memory", "stats completos"

1. Parse agent log for token usage:
   ```
   echo "=== TOKEN CONSUMPTION ==="
   grep "API call" ~/.hermes/logs/agent.log | tail -100 | awk -F' ' '{
     for(i=1;i<=NF;i++) {
       if($i ~ /^in=/) { in_t+=$i; sub(/^in=/,"",in_t); }
       if($i ~ /^out=/) { out_t+=$i; sub(/^out=/,"",out_t); }
       if($i ~ /^total=/) { total_t+=$i; sub(/^total=/,"",total_t); }
     }
   } END {
     printf "Total API calls: %d\n", NR;
     printf "Input tokens:    %d\n", in_t;
     printf "Output tokens:   %d\n", out_t;
     printf "Total tokens:    %d\n", total_t;
   }'
   ```
   (Use real awk parsing. The log format is: `API call #N: model=... in=X out=Y total=Z latency=...`)

2. Run Engram stats:
   ```
   echo "=== ENGRAM MEMORY ==="
   export PATH=$HOME/.local/bin:$PATH
   engram stats --project hermes 2>&1
   ls -lh ~/.engram/engram.db | awk "{print "DB size: " \$5}"
   ```

3. Run system info:
   ```
   echo "=== SYSTEM ==="
   echo "Uptime: $(uptime -p)"
   echo "Disk: $(df -h / | awk 'NR==2{print $3 " / " $2 " (" $5 ")"}')"
   echo "RAM: $(free -h | awk 'NR==2{print $3 " / " $2}')"
   echo "Docker: $(docker ps --format '{{.Names}}' | wc -l) containers running"
   ```

4. Report a clean summary with sections:
   - **💰 Token Usage**: total calls, input/output/total tokens
   - **💾 Engram Memory**: sessions, observations, DB size
   - **🖥️ System**: uptime, disk, RAM, Docker containers
