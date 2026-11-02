# Dark Factory — Playbook

Sistema reutilizable para pasar de la planificación al código con Claude, sin tableros inflados ni tickets usados como bitácora. La plantilla vive en este repo y no se copia: cada proyecto agrega solo su `CLAUDE.md`, `.claude/settings.json` (que activa el plugin), `factory/config.md` y sus docs. Ver `ARQUITECTURA.md` y `CHECKLIST.md`.

## Instalación rápida

- **Plugin en Claude Code** (skills `ticket-quality`, `factory-implement`, `factory-review`):

  ```
  /plugin install dark-factory --marketplace EnmanuelVarelaPividori/dark-factory
  ```

  En un repo de proyecto no hace falta instalarlo a mano: `.claude/settings.json` (copiado de `plugins/dark-factory/templates/settings.json`) lo declara y Claude Code ofrece instalarlo al abrir el repo.
- **Proyecto nuevo:** con el plugin instalado, en el repo del proyecto:

  ```
  /dark-factory:factory-setup
  ```

  Detecta lo que puede del repo y de Jira, pregunta el resto, escribe `factory/config.md`, `CLAUDE.md` y `.claude/settings.json`, arma las tareas programadas y corre la checklist. Lo que queda para una persona está en `CHECKLIST.md`. Referencia completa: `examples/hiqual/`.

## El flujo

```
1. PLANIFICAR      Project de claude.ai: investigación, diagramas, docs (problema → requerimientos → backlog → sprints)
        │
2. TICKETEAR       Skill ticket-quality: duplicados, plantilla, preguntas, Definition of Ready → backlog
        │
3. FABRICAR        Skill factory-implement: Claude Code toma un ticket Ready, lo implementa en una rama y abre un PR
        │
4. REVISAR         Skill factory-review: otra sesión revisa el PR contra ticket + docs. Ciclo revisar ↔ corregir (máx. 3 rondas)
        │
5. MERGEAR         Un developer revisa la versión final y mergea. Nada se mergea sin humano.
```

Por encima corren tres controles recurrentes (ver `plugins/dark-factory/jobs/`):

| Job | Frecuencia (derivada de `sprint.duracion_semanas`) | Modelo sugerido | Qué responde |
|---|---|---|---|
| `plugins/dark-factory/jobs/daily-check.md` | Días hábiles, antes de la daily | Haiku | ¿Se rompió alguna regla de tablero hoy? (WIP, capacidad, tickets no Ready en sprint) |
| `plugins/dark-factory/jobs/weekly-review.md` | Semanal, a mitad de semana. No se crea con sprints de 1 semana. | Sonnet | ¿Cómo va el sprint y qué haría un scrum master esta semana? |
| `plugins/dark-factory/jobs/sprint-review.md` | Al cierre de cada sprint (corre semanal y solo reporta cuando cerró uno) | Opus | ¿Avanzamos contra el plan y los requisitos? ¿Llegamos a la fecha? |

Cómo crearlas, horarios por largo de sprint y prompt para que Claude las cree: `plugins/dark-factory/jobs/README.md`.

**Por qué esos modelos:** el diario son reglas mecánicas (contar, comparar contra umbrales), así que Haiku alcanza y es barato. El semanal lee el tablero y da recomendaciones: Sonnet es suficiente, y Opus no cambia mucho el resultado. El de fin de sprint cruza requisitos, backlog, plan y estado real, y de sus conclusiones salen decisiones de alcance; corre cada 2 semanas, así que Opus vale lo que cuesta. El modelo se elige en la configuración de cada tarea programada.

## Qué es plantilla y qué es configuración

| Va en la plantilla (igual en todos los proyectos) | Va en `config.md` (por proyecto) |
|---|---|
| Flujo, reglas, Definition of Ready / Done | Jira: sitio, cloudId, clave de proyecto, board, IDs de campos |
| Formato de ticket | Equipo: personas, capacidad, velocidad, duración de sprint |
| Skills y prompts de los jobs | Ruta de los docs, IDs de requisitos, épicas |
| Ciclo de review y su límite | Convenciones del repo (ramas, commits, tests, stack) |
| Checklist de puesta en marcha | Destino de los reportes (push, email) |

Si algo de un proyecto empuja a cambiar la plantilla, se discute y se cambia acá, no en una copia.

## Reglas del sistema

### Definition of Ready (un ticket puede entrar a un sprint)

- [ ] Historia en formato COMO / QUIERO / PARA.
- [ ] Descripción con un ejemplo concreto.
- [ ] Criterios de aceptación verificables (cada uno se puede marcar cumplido / no cumplido mirando el sistema o un test).
- [ ] Trazado a un requisito (`R-x.y`) o, si no tiene, justificación explícita.
- [ ] Épica asignada y dependencias listadas; las dependencias bloqueantes están Done o en el mismo sprint antes que él.
- [ ] Estimación ≤5 SP. Un 8 se divide antes de entrar.
- [ ] Tipo: núcleo, configuración o específico de cliente.
- [ ] Sin duplicados en el backlog.

Solo un ticket que cumple todo recibe la etiqueta `factory:ready`. Sin esa etiqueta, la fábrica no lo toca.

### Definition of Done

- [ ] Todos los criterios de aceptación verificados en el PR (uno por uno, con evidencia: test, captura o paso de verificación).
- [ ] Review automático aprobado (factory-review) y review humano aprobado.
- [ ] Tests pasan en CI.
- [ ] Docs actualizados si el ticket cambió algo que los docs describen.
- [ ] Ticket movido a Done con link al PR mergeado.

### Reglas de tablero

- **WIP:** máximo 2 tickets In Progress por persona (configurable).
- **Capacidad:** la suma de SP del sprint no supera `personas × velocidad medida`. Lo que no entra va al backlog, no al sprint.
- **Nada de tickets bitácora.** Si el trabajo ya está hecho, va como comentario o subtarea del ticket existente. Si no hay ticket, se crea uno que cumpla el DoR antes de seguir trabajando, no después.
- **Lotes:** más de 5 tickets en una sola carga requiere mostrar el borrador completo y confirmar antes de crear.
- **Agregar al sprint en curso** cuenta como cambio de alcance: el job diario lo reporta.

### Ciclo de review (pasos 3–4)

1. `factory-implement` abre el PR con la checklist de criterios de aceptación y comenta en el ticket.
2. `factory-review` corre en una sesión **nueva**, sin el razonamiento del implementador. Lee ticket, docs y diff, y responde `APROBADO` o `CAMBIOS` con hallazgos numerados.
3. Si responde `CAMBIOS`, `factory-implement` corrige solo esos hallazgos y vuelve a pedir review.
4. **Máximo 3 rondas.** Si en la tercera ronda sigue sin aprobarse, el PR queda con la etiqueta `factory:needs-human` y un resumen del desacuerdo. Sin límite, dos agentes pueden discutir sin fin y quemar tokens.
5. Con `APROBADO`, el PR queda listo para el developer, que revisa y mergea.

### Disparo de la fábrica

Hay dos formas de que Claude tome un ticket. Elegir una por proyecto en `config.md`:

| Opción | Cómo | Pros | Contras |
|---|---|---|---|
| **A. Etiqueta + tarea programada** (recomendada para empezar) | Una tarea programada busca cada N horas tickets con `factory:ready` + `factory:go` y los procesa de a uno. | Sin infraestructura extra. Fácil de pausar. | Latencia de horas. |
| **B. Mención / automatización** | Jira Automation dispara un webhook (por ejemplo a GitHub Actions con Claude Code) cuando se agrega `factory:go` o se menciona a Claude. | Inmediato. | Requiere configurar Jira Automation, CI y secretos. |

En ambos casos la etiqueta `factory:go` la pone una persona: la fábrica nunca se auto-asigna trabajo.

## Contenido de este repo

| Archivo | Para qué |
|---|---|
| `README.md` | Este playbook. |
| `ARQUITECTURA.md` | Qué vive acá y qué en cada proyecto, quién carga config y skills, ciclo de etiquetas. |
| `CHECKLIST.md` | Puesta en marcha en un proyecto nuevo y verificación de que funciona. |
| `plugins/dark-factory/templates/config.example.md` | Variables por proyecto. Se copia al proyecto como `factory/config.md`. |
| `.claude-plugin/marketplace.json` | Hace de este repo un marketplace de plugins de Claude Code. |
| `plugins/dark-factory/` | El plugin: skills `factory-setup`, `ticket-quality`, `factory-implement`, `factory-review`, más las plantillas y los prompts que usan. |
| `plugins/dark-factory/templates/CLAUDE.md` | Base del `CLAUDE.md` de cada repo. |
| `plugins/dark-factory/templates/settings.json` | `.claude/settings.json` que activa el plugin en cada repo. |
| `plugins/dark-factory/templates/ticket.md` | Formato de ticket. |
| `plugins/dark-factory/jobs/README.md` | Guía de creación de las tareas programadas: variables, calendario, cron, pasos. |
| `plugins/dark-factory/jobs/daily-check.md`, `weekly-review.md`, `sprint-review.md` | Prompts de los controles de tablero. |
| `plugins/dark-factory/jobs/factory-implement.md`, `plugins/dark-factory/jobs/factory-review.md` | Prompts de los runners de la fábrica (opción A de disparo). |
| `examples/hiqual/` | Instalación completa en HiQual, lista para copiar: config, `CLAUDE.md`, `settings.json`, prompts con variables reemplazadas y un ticket de ejemplo. |
