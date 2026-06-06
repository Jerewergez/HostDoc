# Gym

Track your gym workouts, log exercises, and keep your fitness routine organized.

## When to use
- The user says "gym", "ejercicio", "workout", "rutina", "entrenamiento"
- The user wants to log a workout or check their routine
- Time for the daily gym reminder

## Steps

### Log a workout
1. User says: "log workout", "registrar", "hice", "entrené"
2. Ask what exercises they did (if not provided)
3. Save to the gym log: `echo "[$(date +%Y-%m-%d %H:%M)] $EXERCISES" >> ~/hermes/gym-log.txt`
4. Confirm with a 💪 emoji

### Show recent workouts
1. User says: "last workouts", "últimos", "historial", "progreso"
2. Run: `tail -10 ~/hermes/gym-log.txt 2>/dev/null || echo "no workouts logged yet"`
3. Format nicely

### Today's routine (if configured)
1. User asks: "routine", "rutina de hoy", "qué toca"
2. Check if there's a routine file: `cat ~/hermes/gym-routine.txt 2>/dev/null || echo "no routine configured"`
3. Suggest the routine

### Weekly summary
1. User asks: "semana", "weekly", "this week"
2. Run: `grep "$(date +%Y-%W)" ~/hermes/gym-log.txt 2>/dev/null || echo "no workouts this week"`
3. Count and report
