---
name: factory-kickoff
description: Arrancar Dark Factory en un proyecto con una entrevista guiada. Detecta lo que puede, hace preguntas por rondas cortas con propuestas, instala y configura todo, corre los checks y deja el estado en factory/proyecto/estado.md para retomar. Usar con "arrancá la dark factory", "kickoff de la fábrica", "configurá la fábrica preguntándome", "retomá el setup de la fábrica" o "¿qué falta para que la fábrica funcione?".
---

# Factory kickoff

Entrevista guiada para dejar un proyecto con la fábrica funcionando y todo verificado. Sirve desde un Project de claude.ai o desde Claude Code.

- **Con el plugin instalado:** plantilla en `${CLAUDE_PLUGIN_ROOT}/template/`; los pasos de instalación son los de la skill `dark-factory:factory-setup`.
- **Sin el plugin** (por ejemplo, desde un Project de claude.ai): leé del repo https://github.com/EnmanuelVarelaPividori/dark-factory la plantilla `plugins/dark-factory/template/` y la skill `plugins/dark-factory/skills/factory-setup/SKILL.md`, y seguilas igual.

## Cómo preguntar

- **Detectá antes de preguntar.** Cada pregunta lleva tu propuesta (lo que detectaste o lo más razonable) como primera opción, marcada "(recomendado)". No preguntes lo que ya sabés con certeza: mostralo como confirmado.
- **Rondas cortas:** máximo 4 preguntas por ronda. Si la sesión tiene una herramienta de preguntas con opciones, usala; si no, numeradas en un solo mensaje.
- **Una ronda por tema**, en el orden de abajo. Saltá las que no aplican.
- **"No sé" es una respuesta válida:** queda `COMPLETAR` en el config y como pendiente en el estado. No inventes.
- **Después de cada ronda**, actualizá `factory/proyecto/estado.md` (sección 5). Si la conversación se corta, se retoma desde ahí.

## 0. Retomar o empezar

1. Si existe `factory/proyecto/estado.md`: leelo, mostrá en 3 líneas qué está hecho y qué falta, y seguí desde la primera ronda incompleta.
2. Si no: empezá por la ronda 1.

## Rondas

### Ronda 1 — Acceso

Detectá y mostrá: ¿hay acceso a la carpeta o al repo del proyecto? ¿Conector de Atlassian? ¿Acceso a GitHub (escritura)? ¿Estás en un Project de claude.ai (podés leer sus docs)?

Preguntá solo lo que falle. Ej.: "No tengo acceso a la carpeta del repo. ¿Me la das, o trabajo sobre la URL de GitHub?". Sin acceso al repo no se puede instalar: decilo y frená ahí.

### Ronda 2 — Proyecto

- Nombre del proyecto.
- Objetivo en una frase (contra qué se prioriza todo).
- Fecha objetivo.
- Nombre del Project de claude.ai con la investigación (`claude.project`).

### Ronda 3 — Docs de planificación

Buscá en el Project (o en el repo) análisis de problema, requerimientos, backlog y plan. Mostrá qué encontraste y a qué doc fijo corresponde cada uno (`00-analisis-problema.md`, `01-analisis-requerimientos.md`, `02-product-backlog.md`, `03-planificacion-sprints.md`).

- ¿Los convierto al formato de la fábrica? (IDs `R-x.y` en requisitos; stories con metadatos, criterios en checklist, `Dependencias` y `Requisitos`.)
- Si falta alguno: ¿lo armo desde lo que hay en el Project, o queda pendiente?
- Si encontrás contradicciones entre docs: listalas y preguntá cómo resolver cada una (máx. 4 por ronda; si hay más, rondas extra).

### Ronda 4 — Jira

Con el conector, listá proyectos y detectá tipos de issue, campo de story points y campo de sprint.

- ¿Cuál es el proyecto de Jira? (opciones: los detectados)
- ¿Usan sprints en Jira? ¿En qué board?
- Mapeo de prioridad MUST / SHOULD / COULD (proponé High / Medium / Low).
- ¿Usan prefijos de capa en los títulos (ej. `[BE]`, `[FE]`)?

### Ronda 5 — Equipo y sprint

- ¿Cuántas personas desarrollan y cuáles son sus cuentas de Jira?
- Duración del sprint (1 / 2 / 3 / 4 semanas) y día de inicio.
- Fecha de inicio de algún sprint real, hora de la daily y zona horaria.
- Velocidad por persona y sprint: ¿medida o estimada? (si es estimada, marcala así).

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

Con las respuestas, ejecutá los pasos 2–5 de `factory-setup`: copiar la plantilla, escribir `factory/proyecto/config.md`, convertir o mover los docs, conectar `CLAUDE.md`, `AGENTS.md` y `.claude/settings.json`. Copiá también config y docs al Project con las mismas rutas, si tenés acceso.

Antes de escribir, mostrá la lista de archivos que se crean o cambian y pedí confirmación. No hagas commit: preguntá si querés que lo prepare en una rama nueva.

### Ronda 9 — Tareas programadas

Mostrá la tabla calculada (nombre, horario, modelo sugerido) con `factory/jobs/README.md`.

- ¿Creo los controles de tablero (diario, mitad de sprint, cierre)?
- ¿Los runners los creo yo para que los muevas al Project, o los creás vos desde el Project?

Recordá: el modelo de cada tarea y la aprobación automática se eligen en su configuración.

### Ronda 10 — Checks que hace una persona

Preguntá uno por uno (hecho / no / más tarde), en rondas de 4:

- Etiquetas de PR en GitHub: `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human` (si tenés `gh` o acceso a GitHub, ofrecé crearlas vos).
- Rama base protegida: merge solo con review humano.
- Plugin aceptado en Claude Code al abrir el repo (`/plugin` lo muestra habilitado).
- `ticket-quality` guardada como skill de la cuenta en claude.ai.
- Modelo elegido en cada tarea programada.
- Aprobación automática en los controles.
- Runners movidos al Project.
- Si el repo dark-factory es privado: acceso de lectura para todos y para las tareas.

### Ronda 11 — Smoke test

- ¿Elegimos ahora un ticket chico (1–2 SP) para probar el ciclo completo? Proponé candidatos del backlog.
- Si sí: verificalo o crealo con `ticket-quality` y explicá que alguien tiene que ponerle `factory:go`.

## Cierre

Terminá con:

1. Qué quedó listo (una línea por bloque).
2. Pendientes, cada uno con quién lo hace.
3. Cómo retomar: "Decí *retomá el setup de la fábrica* y sigo desde `factory/proyecto/estado.md`".

## 5. Formato de `factory/proyecto/estado.md`

Partí de `template/factory/proyecto/estado.md`. Actualizá en cada ronda:

- Tabla de rondas: estado (✅ hecha / ⏳ en curso / — no aplica) y fecha.
- Checks: ✅ / ❌ / ⏳ con quién y cuándo.
- Decisiones tomadas en la entrevista (una línea cada una).
- Pendientes con responsable.

No guardes en el estado valores que van en el config: el estado registra qué se hizo y qué falta.
