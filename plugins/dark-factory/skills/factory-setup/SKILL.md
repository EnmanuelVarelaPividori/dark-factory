---
name: factory-setup
description: Instalar o verificar Dark Factory en el repo de un proyecto. Detecta datos del repo, pregunta lo que falta, escribe factory/config.md, CLAUDE.md y .claude/settings.json, genera los prompts de las tareas programadas y corre la checklist. Usar con "instalá la fábrica", "configurá dark factory en este repo", "setup de la fábrica", o "¿está bien configurada la fábrica?".
---

# Factory setup

Deja un repo listo para la fábrica en una sesión. Las plantillas están en el plugin:

- Config: `${CLAUDE_PLUGIN_ROOT}/templates/config.example.md`
- `CLAUDE.md`: `${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md`
- Settings: `${CLAUDE_PLUGIN_ROOT}/templates/settings.json`
- Prompts y calendario de las tareas: `${CLAUDE_PLUGIN_ROOT}/jobs/` (empezá por `jobs/README.md`)

Si ya existe `factory/config.md`, saltá a **Verificar** (paso 6) y después ofrecé completar lo que falte.

## 1. Detectar (sin preguntar)

Sacá del repo todo lo que se pueda:

| Dato | De dónde |
|---|---|
| `repo.url` | `git remote get-url origin` |
| `repo.rama_base` | Rama de destino de los últimos PRs mergeados, o `origin/HEAD` |
| `repo.nombre_rama`, `repo.commit` | Patrones de `git branch -r` y `git log --oneline -30` |
| `repo.tests` | Workflows de CI (`.github/workflows/`), `README`, `package.json`, `composer.json`, `Makefile` |
| `repo.stack` | Manifiestos (`composer.json`, `package.json`, `pyproject.toml`, `go.mod`…), entorno local (`.ddev/`, `docker-compose`), carpetas de código propio |
| `docs.*` | Carpeta con docs de planificación (`docs/**/` con requerimientos y backlog) |
| `jira.*` | Si hay conector de Atlassian: sitio, cloudId, proyectos visibles, IDs de tipos y campos (story points, sprint) con `getJiraIssueTypeMetaWithFields` |
| `CLAUDE.md`, `.claude/settings.json` | ¿Existen? Si existen, se combinan; no se pisan |

## 2. Preguntar lo que falta

Un solo mensaje, con tu mejor propuesta en cada punto, para que la respuesta pueda ser "sí":

- Proyecto: nombre, objetivo en una frase (contra qué se prioriza) y fecha objetivo.
- Jira: clave del proyecto, si no quedó clara.
- Equipo: cuántas personas desarrollan y sus cuentas de Jira.
- Sprint: duración en semanas, día de inicio, fecha de inicio de algún sprint real, hora de la daily, zona horaria.
- Velocidad: SP por persona y por sprint (medida si la hay; si no, una estimación marcada como tal).
- Disparo de la fábrica: A (etiqueta + tarea programada, recomendado) o B (automatización).
- Reportes: canales (push, email) e idioma.

Lo que el usuario no sepa queda como `COMPLETAR` en el config. No inventes valores.

## 3. Escribir los archivos del proyecto

1. `factory/config.md`: mismo formato que `config.example.md`, con valores en vez de ejemplos. Derivados: `sprint.dias_habiles` = N × 5, `reglas.dias_estancado` = max(2, N + 1).
2. `CLAUDE.md`: desde la plantilla, con las variables reemplazadas. Si ya existe un `CLAUDE.md`, agregá las secciones de la fábrica (import `@factory/config.md`, docs, convenciones, reglas) sin borrar lo que había, y mostrá el diff.
3. `.claude/settings.json`: si no existe, copiá la plantilla. Si existe, combiná `extraKnownMarketplaces` y `enabledPlugins` sin tocar el resto.
4. Si no hay docs de planificación: decilo y ofrecé crear el esqueleto (`00-analisis-problema.md`, `01-analisis-requerimientos.md`, `02-product-backlog.md`, `03-planificacion-sprints.md`) en `docs/<proyecto>/`. Los docs se completan en el Project de claude.ai, no acá.

No hagas commit: mostrá el resumen de cambios y preguntá. Si la rama actual es la rama base, proponé una rama nueva.

## 4. Etiquetas

- GitHub: si `gh` está disponible y autenticado, ofrecé crear `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human` (con `gh label create`, una por línea, mostrando los comandos antes).
- Jira: las etiquetas se crean al usarlas por primera vez. Listá las que usa el sistema: `factory:ready`, `factory:go`, `factory:wip`, `factory:blocked` (y las del PR, reflejadas en el ticket).

## 5. Tareas programadas

Con `${CLAUDE_PLUGIN_ROOT}/jobs/README.md`:

1. Calculá el calendario desde `sprint.duracion_semanas`, `sprint.dia_inicio`, `equipo.hora_daily` y `equipo.zona_horaria`. Con N = 1 no va la de mitad de sprint; si `fabrica.disparo` no es A, no van los dos runners.
2. Generá cada prompt reemplazando todas las variables `{{...}}` y los marcadores de cron. Verificá que no quede ninguno.
3. Mostrá una tabla: nombre, cron, hora local, modelo sugerido, conectores.
4. Si la sesión tiene herramientas para crear tareas programadas: creá las tareas después de que el usuario confirme la tabla, con notificaciones según `reportes.canales`, y ejecutá cada una una vez para probarla. Si no las tiene: guardá los prompts listos en un archivo temporal fuera del repo (no en el repo del proyecto) y decí dónde pegarlos.
5. El modelo de cada tarea se elige en su configuración: recordalo en la tabla.

## 6. Verificar

Recorré la checklist del sistema y reportá ✅ / ❌ / ⏳ (requiere a una persona):

| Ítem | Cómo se verifica |
|---|---|
| Config completo | `factory/config.md` existe; listá las filas `COMPLETAR` |
| `CLAUDE.md` importa el config | Contiene `@factory/config.md` |
| Plugin activado | `.claude/settings.json` tiene `dark-factory@dark-factory: true` |
| Docs de planificación | Existen los archivos de `docs.*`; el backlog tiene campo `Requisitos` |
| Jira accesible | Una JQL simple sobre `jira.proyecto` responde |
| Etiquetas GitHub | `gh label list` las muestra |
| Tareas programadas | Existen con esos nombres (si se pueden listar) |
| Config en el Project | ⏳ Copiar `factory/config.md` como doc del Project de claude.ai |
| `ticket-quality` en claude.ai | ⏳ Guardarla como skill de la cuenta |
| Rama base protegida | ⏳ Merge solo con review humano |
| Smoke test | ⏳ Un ticket de 1–2 SP recorre todo el flujo (ver `CHECKLIST.md` del repo dark-factory) |

Terminá con la lista de pendientes para una persona, nada más.

## No hacer

- Sobrescribir un `CLAUDE.md` o un `settings.json` existente.
- Hacer commit o push sin confirmación.
- Crear tareas programadas sin mostrar antes la tabla.
- Inventar valores de Jira, equipo o velocidad.
