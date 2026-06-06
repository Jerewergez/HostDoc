# Daily

Manage your daily life, notes, reminders, and general organization.

## When to use
- The user says "daily", "daily note", "recordatorio", "nota", "remind"
- The user wants to save a thought, note, or idea
- The user asks for help organizing their day

## Steps

### Save a note
1. User says: "note", "nota", "apunte", "recordá"
2. Save: `echo "[$(date +%Y-%m-%d %H:%M)] $NOTE" >> ~/hermes/daily-notes.txt`
3. Confirm: "📝 Note saved"

### Read notes
1. User says: "notes", "notas", "mostrar notas"
2. Run: `tail -20 ~/hermes/daily-notes.txt 2>/dev/null || echo "no notes yet"`
3. Show the recent notes

### Morning routine
1. User asks: "morning", "buenos días", "hoy"
2. Suggest checking tasks, checking the server, planning the day
3. Offer to show: pending tasks, VPS status, today's date

### Set a reminder
1. User says: "remind", "recordatorio", "alarma"
2. Note that Hermes cron can be used for scheduled reminders
3. For persistent reminders, suggest the user configure a cron job
