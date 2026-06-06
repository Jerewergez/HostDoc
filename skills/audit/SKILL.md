# Audit (audit)

Run CHIHUAUDIT system security audits. Checks firewall, SSH, services, resources, Docker, network, and more. Uses the chihuaudit binary.

## When to use
- The user says: "audit", "auditar", "seguridad", "security", "chihuaudit", "analizar seguridad", "escaneo"
- The user wants a security health check of the VPS
- The user asks about vulnerabilities or security configuration

## Prerequisites
- chihuaudit binary at ~/hermes/scripts/bin/chihuaudit
- For full audit: sudo NOPASSWD configured (otherwise runs without elevated checks)

## Steps

### 1. Full security audit
User says: "audit", "audit full", "chihuaudit", "analisis completo"

1. Run the audit:
   ```
   cd ~/hermes/scripts/bin
   
   # Try with sudo first, fall back to without
   if sudo -n true 2>/dev/null; then
     ./chihuaudit audit 2>&1
   else
     echo "⚠️  Running without sudo (limited checks). Configure sudo NOPASSWD for full audit."
     ./chihuaudit audit 2>&1
   fi
   ```

2. Structure the report in Discord-friendly format:

   **🔒 SECURITY**
   - Firewall: active / inactive / skipped
   - SSH Port: X, Password Auth: yes/no, Root Login: yes/no
   - Fail2ban: active/inactive
   - External Ports: [list]
   - ⚠️ Highlight any: Root SSH enabled, open unusual ports, no firewall

   **🚀 SERVICES**
   - Total: X running, Y failed
   - List failed services by name

   **💻 RESOURCES**
   - CPU Load: X (1/5/15min)
   - RAM: X used / Y total (Z%)
   - Disk: X used / Y total (Z%)

   **🌐 NETWORK**
   - External ports: [list]
   - Localhost ports: [list]

   **📊 SUMMARY**
   - Total Checks: 87
   - Critical: X 🔴
   - Warnings: Y 🟡
   - Passed: Z ✅

### 2. Quick security check
User says: "audit quick", "check rapido", "esta todo bien?"

1. Run only security and resource checks:
   ```bash
   echo "=== FIREWALL ==="
   sudo ufw status 2>/dev/null || echo "ufw not available"
   
   echo "=== SSH ==="
   grep -E "^(Port|PasswordAuthentication|PermitRootLogin)" /etc/ssh/sshd_config 2>/dev/null
   
   echo "=== FAIL2BAN ==="
   systemctl is-active fail2ban 2>/dev/null || echo "not active"
   
   echo "=== RESOURCES ==="
   echo "CPU: $(uptime | awk -F'load average:' '{print $2}' | xargs)"
   echo "RAM: $(free -h | awk '/^Mem:/ {print $3 "/" $2}')"
   echo "DISK: $(df -h / | awk 'NR==2 {print $3 "/" $2 " (" $5 ")"}')"
   ```

### 3. Check specific security items
User says: "audit ports", "audit ssh", "audit firewall", "audit services"

1. Ports: `ss -tuln | grep LISTEN`
2. SSH: `grep -v "^#" /etc/ssh/sshd_config | grep -v "^$"`
3. Firewall: `sudo ufw status verbose 2>/dev/null || sudo iptables -L -n 2>/dev/null || echo "no firewall tool"`
4. Services: `systemctl list-units --type=service --state=running --no-pager`

### Notes
- chihuaudit is a Go binary, 9.3MB, at ~/hermes/scripts/bin/chihuaudit
- Without sudo, some checks are skipped (firewall, SSH config details)
- For best results, configure: `sudo visudo -f /etc/sudoers.d/chihuaudit`
  with: `botuser ALL=(ALL) NOPASSWD: /home/botuser/hermes/scripts/bin/chihuaudit`
- The binary is read-only, makes no system modifications

### 6. Check vulnerability alerts
User says: "audit alerts", "audit vulnerabilidades", "que alertas hay", "vuln", "alertas"

1. Read the current alert state:
   ```
   cat ~/hermes/logs/audit/alert-state.json 2>/dev/null || echo "No alerts yet"
   ```

2. Report:
   - **🔴 CRITICAL**: [count] — list each with description
   - **🟡 WARNINGS**: [count] — list each with description
   - **✅ OK**: if no alerts

3. Recommendations for each critical issue:
   - SSH_ROOT_LOGIN → "Run: sudo sed -i \"s/PermitRootLogin yes/PermitRootLogin no/\" /etc/ssh/sshd_config && sudo systemctl restart sshd"
   - FIREWALL_INACTIVE → "Enable: sudo ufw enable"
   - FAIL2BAN_INACTIVE → "Check: sudo systemctl start fail2ban"
   - SERVICES_FAILED → "Check: systemctl status <service>"

### 7. Run vulnerability scan now
User says: "audit scan", "audit escanear", "analizar ahora"

1. Run: `bash ~/hermes/scripts/vuln-alert.sh 2>&1`
2. Show the full alert report
