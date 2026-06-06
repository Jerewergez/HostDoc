# Cómo mantener esta wiki

La idea es que la wiki crezca orgánicamente. No hace falta documentar todo — solo lo que vale la pena recordar.

## Cuándo agregar algo

Agregá una entrada cuando:
- Resolviste un problema que no querés olvidar
- Aprendiste un comando o configuración nueva
- Estableciste un workflow que querés repetir
- Cambiaste algo en la infraestructura

## Formato

Cada entrada es un archivo markdown en la carpeta correspondiente:

```markdown
# Título descriptivo

## Contexto
Por qué es necesario este conocimiento

## Desarrollo
Contenido principal

## Comandos
\`\`\`bash
$ comando
\`\`\`

## Referencias
- Enlaces útiles
```

## Desde Discord

Lo más práctico: `/pi creá una entrada en la wiki sobre <tema> con formato markdown`

## Commit y push

```bash
cd ~/hermes
git add wiki/
git commit -m "docs: wiki - <tema>"
git push
```

Después `/deploy` en Discord y la wiki se actualiza en el VPS también.
