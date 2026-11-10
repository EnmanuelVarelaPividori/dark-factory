---
name: factory-kickoff
description: Única puerta para instalar, configurar, actualizar y verificar Dark Factory en el repo de un proyecto. Entrevista guiada por rondas cortas con propuestas, deja todo chequeado y el avance en factory/proyecto/estado.md para retomar. Usar con "arrancá la dark factory", "instalá la fábrica", "configurá dark factory", "kickoff de la fábrica", "actualizá la fábrica", "retomá el setup de la fábrica", "¿está bien configurada la fábrica?" o "¿qué falta para que la fábrica funcione?".
---

# Factory kickoff

Una sola skill para todo el ciclo de vida de la fábrica en un repo: instalarla con una entrevista, retomarla, actualizarla cuando cambia la plantilla y verificar que funciona. Sirve desde Claude Code o desde un Project de claude.ai.

- **Con el plugin instalado:** la plantilla está en `${CLAUDE_PLUGIN_ROOT}/template/`.
- **Sin el plugin** (por ejemplo, desde un Project de claude.ai): leé la plantilla `plugins/dark-factory/template/` del repo https://github.com/EnmanuelVarelaPividori/dark-factory y seguí esta skill igual. Donde dice `${CLAUDE_PLUGIN_ROOT}/template/`, usá esa carpeta.

La plantilla tiene la misma forma que la raíz de un repo:

```
template/
├── CLAUDE.md, AGENTS.md          bloques genéricos (se agregan, no se pisan)
├── .claude/settings.json         activa el plugin (se combina, no se pisa)
└── factory/                      genérico, salvo factory/proyecto/
    └── proyecto/                 plantilla del proyecto: se crea una vez y nunca se sobrescribe
```

La estructura esperada y las reglas para agentes están en `template/factory/ESTRUCTURA.md`.

## 0. Elegir el modo

Mirá el repo antes de decir nada:

| Estado del repo | Modo |
|---|---|
| No existe `factory/` | **Entrevista** desde la ronda 1. |
| `factory/VERSION` distinta de `${CLAUDE_PLUGIN_ROOT}/template/factory/VERSION` | **Actualizar** (sección *Actualizar*). Después seguí con la fila que corresponda. |
| `factory/proyecto/estado.md` tiene rondas sin ✅ | **Retomar:** mostrá en 3 líneas qué está hecho y qué falta, y seguí desde la primera ronda incompleta. |
| Todo al día y todas las rondas en ✅ o — | **Verificar** (sección *Verificar*) y ofrecé completar lo que dé ❌. |

Un proyecto instalado antes de esta versión puede tener el config completo y las rondas en ⏳: no repitas preguntas. Por cada ronda cuyos datos ya están en el config, mostralos como confirmados, marcá la ronda ✅ y pasá a la siguiente.

## Cómo preguntar

- **Detectá antes de preguntar** (ver *Detectar*). Cada pregunta lleva tu propuesta como primera opción, marcada "(recomendado)", para que la respuesta pueda ser "sí". Lo que ya sabés con certeza no se pregunta: se muestra como confirmado.
- **Rondas cortas:** máximo 4 preguntas por ronda. Si la sesión tiene una herramienta de preguntas con opciones, usala; si no, numeradas en un solo mensaje.
- **Una ronda por tema**, en el orden de abajo. Saltá las que no aplican.
- **"Preguntame todo junto":** si la persona lo pide, juntá las preguntas pendientes de las rondas 2 a 7 en un solo mensaje, con tu propuesta en cada una, y seguí con la ronda 8.
- **"No sé" es una respuesta válida:** queda `COMPLETAR` en el config y como pendiente en el estado. No inventes.
- **Después de cada ronda**, actualizá `factory/proyecto/estado.md` (ver *Formato del estado*). Si `factory/` todavía no existe, guardá las respuestas en la conversación y escribilas en la ronda 8. Si la conversación se corta, se retoma desde el estado.

## Detectar (sin preguntar)

Hacelo al empezar; alimenta las propuestas de todas las rondas.

| Clave del config | De dónde |
|---|---|
| `repo.url` | `git remote get-url origin` |
| `repo.rama_base` | Destino de los últimos PRs mergeados, u `origin/HEAD` |
| `repo.nombre_rama`, `repo.commit` | Patrones en `git branch -r` y `git log --oneline -30` |
| `repo.tests` | Workflows de CI, `README`, `package.json`, `composer.json`, `Makefile` |
| `repo.stack` | Manifiestos, entorno local (`.ddev/`, `docker-compose`), carpetas de código propio |
| `jira.*` | Si hay conector de Atlassian: sitio, cloudId, proyectos, IDs de tipos, campos de story points y sprint (`getJiraIssueTypeMetaWithFields`), boards |
| Docs existentes | Docs de planificación en el Project o en otra carpeta del repo (ej. `docs/`) |

## Rondas

### Ronda 1 — Acceso

Detectá y mostrá: ¿hay acceso a la carpeta o al repo del proyecto? ¿Conector de Atlassian? ¿Acceso a GitHub con escritura? ¿Estás en un Project de claude.ai (podés leer sus docs)?

Preguntá solo lo que falle. Ej.: "No tengo acceso a la carpeta del repo. ¿Me la das, o trabajo sobre la URL de GitHub?". Sin acceso al repo no se puede instalar: decilo y frená ahí.

### Ronda 2 — Proyecto

- Nombre del proyecto.
- Objetivo en una frase (contra qué se prioriza todo).
- Fecha objetivo.
- Nombre del Project de claude.ai con la investigación (`claude.project`).

### Ronda 3 — Docs de planificación

Mostrá lo que encontraste y a qué doc fijo corresponde cada uno: `00-analisis-problema.md`, `01-analisis-requerimientos.md`, `02-product-backlog.md`, `03-planificacion-sprints.md`.

- Si están en otra carpeta del repo: ¿los muevo a `factory/proyecto/docs/` con esos nombres? No muevas sin confirmación.
- ¿Los convierto al formato de la fábrica? (IDs `R-x.y` en requisitos; stories con metadatos, criterios en checklist, `Dependencias` y `Requisitos`.)
- Si falta alguno: ¿lo armo desde lo que hay en el Project, o queda pendiente?
- Si encontrás contradicciones entre docs: listalas y preguntá cómo resolver cada una (máx. 4 por ronda; si hay más, rondas extra).

### Ronda 4 — Jira

- ¿Cuál es el proyecto de Jira? (opciones: los detectados)
- ¿Usan sprints en Jira? ¿En qué board?
- Mapeo de prioridad MUST / SHOULD / COULD (proponé High / Medium / Low).
- ¿Usan prefijos de capa en los títulos (ej. `[BE]`, `[FE]`)?

### Ronda 5 — Equipo y sprint

- ¿Cuántas personas desarrollan y cuáles son sus cuentas de Jira?
- Duración del sprint (1 / 2 / 3 / 4 semanas) y día de inicio.
- Fecha de inicio de algún sprint real, hora de la daily y zona horaria.
- Velocidad por persona y sprint: ¿medida o estimada? (si es estimada, marcala así).

Derivados, sin preguntar: `sprint.dias_habiles` = N × 5; `reglas.dias_estancado` = max(2, N + 1).

### Ronda 6 — Repo

Mostrá lo detectado y pedí confirmación o corrección:

- Rama base y patrón de nombres de rama.
- Convención de commits.
- Comandos de tests (los del CI).
- Stack y carpetas de código propio; ¿algo queda fuera del alcance de la fábrica?

### Ronda 7 — Reglas de la fábrica

Proponé los valores por defecto y preguntá si cambian:

- Disparo: A (etiqueta + tarea programada, recomendado) o B (automatización).
- WIP por persona (2), SP máximos por ticket (5), tickets por carga que exigen confirmación (5), rondas de review (3).
- Reportes: canales (push, email) e idioma.

### Ronda 8 — Instalar

Antes de escribir, mostrá la lista de archivos que se crean o cambian y pedí confirmación. Si la rama actual es la rama base, proponé una rama nueva.

1. Copiá todo `${CLAUDE_PLUGIN_ROOT}/template/factory/` a `factory/`, **excepto `factory/proyecto/`** si ya existe.
2. Si `factory/proyecto/` no existe: copiá `template/factory/proyecto/` (config vacío, `estado.md` y `docs/README.md`).
3. Escribí `factory/proyecto/config.md` con lo detectado y lo respondido. Lo que no se sepa queda `COMPLETAR`.
4. Mové o convertí los docs según la ronda 3.
5. `CLAUDE.md` y `AGENTS.md`: si no existen, copiá los de la plantilla. Si existen y no tienen el bloque de Dark Factory, agregalo al final. Nunca borres contenido.
6. `.claude/settings.json`: si no existe, copiá la plantilla. Si existe, combiná `extraKnownMarketplaces` y `enabledPlugins` sin tocar el resto.
7. Si tenés acceso al Project, copiá ahí `factory/proyecto/config.md` y los docs con las mismas rutas.

No hagas commit: preguntá si querés que lo prepare en una rama.

### Ronda 9 — Tareas programadas

Con `factory/jobs/README.md`:

1. Calculá el calendario desde el config. Con N = 1 no va la de mitad de sprint; si `fabrica.disparo` ≠ A, no van los runners.
2. Cada tarea usa el prompt corto del README (repo + rama + job). No pegues valores del config en el prompt: el job los lee al correr.
3. Mostrá una tabla: nombre, cron, hora local, modelo sugerido, conectores.
4. Preguntá:
   - ¿Creo los controles de tablero (diario, mitad de sprint, cierre)?
   - Los runners van **dentro del Project de claude.ai** (`claude.project`) para que lean los docs que citan los tickets. ¿Los creo yo para que los muevas al Project, o los creás vos desde el Project?
5. Si la sesión puede crear tareas programadas: creá las confirmadas, con notificaciones según `reportes.canales`, y ejecutá cada una una vez para probarla. Si no puede: dejá la tabla y los prompts listos para pegar.

Recordá: el modelo de cada tarea y la aprobación automática se eligen en su configuración.

### Ronda 10 — Verificar

Corré la sección *Verificar*. Los ítems ⏳ se preguntan uno por uno (hecho / no / más tarde), en rondas de 4. Para las etiquetas de GitHub, si `gh` está autenticado, ofrecé crearlas vos mostrando antes los comandos.

### Ronda 11 — Smoke test

- ¿Elegimos ahora un ticket chico (1–2 SP) para probar el ciclo completo? Proponé candidatos del backlog.
- Si sí: verificalo o crealo con `ticket-quality` y explicá que alguien tiene que ponerle `factory:go`.

## Actualizar

Cuando `factory/VERSION` es distinta de la del plugin:

1. Reemplazá los archivos genéricos de `factory/` con los de `${CLAUDE_PLUGIN_ROOT}/template/factory/`, **sin tocar `factory/proyecto/`**. Mostrá qué archivos cambiaron, se agregaron o se quitaron.
2. Aplicá las migraciones que correspondan a la versión instalada (sección *Migraciones*).
3. Compará las claves de `template/factory/proyecto/config.md` con las del config del proyecto. Las claves nuevas se agregan con su valor por defecto o `COMPLETAR`, y se preguntan como una ronda más.
4. Seguí con *Verificar*. No hagas commit sin confirmación.

## Migraciones

Una entrada por versión que necesita tocar algo fuera de los archivos genéricos. Aplicá todas las posteriores a la versión instalada, en orden.

| Desde una versión anterior a | Qué hacer |
|---|---|
| 0.5.0 | Si falta `factory/proyecto/estado.md`, copiá solo ese archivo de la plantilla. |
| 0.6.0 | La skill `factory-setup` se unificó en esta. En el bloque de Dark Factory de `CLAUDE.md`, reemplazá la mención a `dark-factory:factory-setup` por la línea de skills de `template/CLAUDE.md`. En `estado.md`, si la ronda 10 se llama "Checks de una persona", renombrala a "Verificar". |

## Verificar

Una sola lista de verificación, usada en la ronda 10 y en el modo verificar. Reportá ✅ / ❌ / ⏳ (requiere a una persona):

| Ítem | Cómo |
|---|---|
| Plantilla instalada y al día | Todos los archivos genéricos existen; `factory/VERSION` = versión del plugin |
| Config completo | Filas `COMPLETAR` en `factory/proyecto/config.md` |
| Docs | Existen los 4 docs con los nombres fijos; los requisitos tienen IDs `R-x.y`; las stories tienen `Requisitos` |
| `CLAUDE.md` | Importa `@factory/ESTRUCTURA.md`, `@factory/reglas.md`, `@factory/proyecto/config.md` |
| `AGENTS.md` | Apunta a `factory/ESTRUCTURA.md` |
| Plugin activado | `.claude/settings.json` con `dark-factory@dark-factory: true`; ⏳ aceptado en Claude Code (`/plugin` lo muestra habilitado) |
| Jira accesible | Una JQL simple sobre `jira.proyecto` responde |
| Etiquetas GitHub | `gh label list` incluye `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human` |
| Tareas programadas | Existen (si se pueden listar); ⏳ modelo elegido y aprobación automática en los controles |
| Project de claude.ai | ⏳ `factory/proyecto/config.md` y los docs subidos con las mismas rutas; investigación en `investigacion/`, `decisiones/`, `diagramas/` |
| Runners dentro del Project | ⏳ Las tareas implementar y revisar pertenecen al Project |
| `ticket-quality` en claude.ai | ⏳ Guardada como skill de la cuenta |
| Rama base protegida | ⏳ Merge solo con review humano |
| Repo dark-factory accesible | ⏳ Si es privado: lectura para todos y para las tareas |
| Smoke test | ⏳ Ver bloque 6 de `factory/CHECKLIST.md` |

Actualizá los checks en `factory/proyecto/estado.md`.

## Cierre

Terminá siempre con:

1. Qué quedó listo (una línea por bloque).
2. Pendientes, cada uno con quién lo hace.
3. Cómo retomar: "Decí *retomá el setup de la fábrica* y sigo desde `factory/proyecto/estado.md`".

## Formato del estado

`factory/proyecto/estado.md` parte de `template/factory/proyecto/estado.md`. Actualizá en cada ronda:

- Tabla de rondas: estado (✅ hecha / ⏳ en curso / — no aplica) y fecha.
- Checks: ✅ / ❌ / ⏳ con quién y cuándo.
- Decisiones tomadas en la entrevista (una línea cada una).
- Pendientes con responsable.

No guardes en el estado valores que van en el config: el estado registra qué se hizo y qué falta.

## No hacer

- Sobrescribir `factory/proyecto/`, ni borrar contenido de `CLAUDE.md`, `AGENTS.md` o `settings.json`.
- Editar archivos genéricos con valores del proyecto.
- Escribir archivos sin mostrar antes la lista, ni hacer commit o push sin confirmación.
- Crear tareas programadas sin mostrar antes la tabla.
- Inventar valores de Jira, equipo o velocidad.
