# Deploy

Update the Agente project from GitHub. Works with the auto-deploy cron (every 5 min) or can be triggered manually.

## When to use
- The user says: "deploy", "actualizar", "update", "git pull", "push"
- After the user pushes changes to GitHub and wants them applied now
- To check the deployment status

## Steps

### 1. Manual deploy
User says: "deploy", "actualizar", "update"

1. Run: `cd ~/hermes && git pull && echo "OK"`
2. If successful, sync skills to Hermes runtime:
   ```
   for skill_dir in ~/hermes/skills/*/; do
     skill_name=$(basename "$skill_dir")
     [ -f "$skill_dir/SKILL.md" ] && cp "$skill_dir/SKILL.md" ~/.hermes/skills/"$skill_name"/SKILL.md
   done
   ```
3. Reload skills in Hermes (the next conversation turn will pick them up)
4. Report: commit hash, what changed (summary of git log --oneline -1), skills synced

### 2. Check deploy status
User says: "deploy status", "ultimo deploy", "cuando se actualizo"

1. Check: `cat /tmp/agente-deploy-status.json 2>/dev/null || echo '{"status":"never"}'`
2. Also check: `tail -5 ~/hermes/logs/auto-deploy.log`
3. Report last deploy time, commit hash, and cron status

### 3. Auto-deploy (passive)
The system has a cron job (`crontab`) that runs every 5 minutes:
- `*/5 * * * * ~/hermes/scripts/auto-deploy.sh`
- It does git fetch + pull, syncs skills, and logs to ~/hermes/logs/auto-deploy.log
- No manual action needed — just push to GitHub and wait up to 5 min

### Notes
- After a deploy that changes config/, the gateway may need a restart:
  `systemctl --user restart hermes-gateway.service`
- After a deploy that changes skills/, the new skills are available on the next conversation turn
- The auto-deploy script has a lock to prevent concurrent runs
