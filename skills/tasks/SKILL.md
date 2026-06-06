# Tasks

Manage your data engineering tasks, to-dos, and work items. Keep track of what you need to do.

## When to use
- The user wants to add, list, complete, or manage tasks
- The user says "task", "tarea", "to-do", "pendiente", "recordame"
- The user is planning data engineering work

## Steps

### Add a task
1. User says: "add task", "agregar tarea", "recordame", "nueva tarea"
2. Ask: what is the task? (if not provided)
3. Determine the category: data-eng, gym, daily, or general
4. Append to the task file: `echo "[$(date +%Y-%m-%d)] $TASK ($CATEGORY)" >> ~/hermes/tasks.txt`
5. Confirm: "✅ Task added: [category] $TASK"

### List tasks
1. User says: "list tasks", "tareas", "qué tengo", "show tasks"
2. If category specified: `grep "(CATEGORY)" ~/hermes/tasks.txt 2>/dev/null || echo "no tasks"`
3. If no category: `cat ~/hermes/tasks.txt 2>/dev/null || echo "no tasks yet"`
4. Format the output nicely

### Complete a task
1. User says: "done", "completada", "terminé", "complete"
2. Find the task in the file
3. Mark it: use sed to add "[DONE]" prefix

### Category filter
- "data-eng" → Data engineering tasks
- "gym" → Gym and exercise tasks
- "daily" → Daily life tasks
- "general" → Uncategorized tasks
