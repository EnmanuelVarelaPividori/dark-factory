# Arquitectura

La plantilla vive en **un solo repo** (`dark-factory`). Cada proyecto lleva solo lo propio: su config, su `CLAUDE.md`, sus docs y una línea que activa el plugin. Mejorar una skill o un prompt acá mejora todos los proyectos sin copiar nada.

## Repo central: `dark-factory`

```
dark-factory/
├── README.md                     Playbook: flujo, reglas, Definition of Ready / Done
├── ARQUITECTURA.md               Este documento
├── CHECKLIST.md                  Puesta en marcha por proyecto
├── config.example.md             Variables a completar por proyecto
├── .claude-plugin/
│   └── marketplace.json          Convierte el repo en un marketplace de plugins
├── plugins/
│   └── dark-factory/             El plugin (se instala, no se copia)
│       ├── .claude-plugin/plugin.json
│       └── skills/
│           ├── ticket-quality/SKILL.md
│           ├── factory-implement/SKILL.md
│           └── factory-review/SKILL.md
├── templates/
│   ├── CLAUDE.md                 Base del CLAUDE.md de cada repo
│   ├── settings.json             .claude/settings.json que activa el plugin
│   └── ticket.md                 Formato de ticket
├── jobs/
│   ├── README.md                 Cómo crear las tareas programadas, calendario y cron
│   ├── daily-check.md            Control diario (Haiku)
│   ├── weekly-review.md          Review de mitad de sprint (Sonnet)
│   ├── sprint-review.md          Evaluación de cierre (Opus)
│   ├── factory-implement.md      Runner implementar (Sonnet)
│   └── factory-review.md         Runner revisar (Opus)
└── examples/
    └── hiqual/                   Instalación completa de referencia
```

## Repo de cada proyecto

Solo esto se agrega al repo del proyecto:

```
<repo>/
├── CLAUDE.md                     generado desde templates/CLAUDE.md; importa factory/config.md
├── .claude/
│   └── settings.json             copia de templates/settings.json: activa el plugin dark-factory
├── factory/
│   └── config.md                 la config del proyecto (único archivo propio de la fábrica)
└── docs/<proyecto>/              docs de planificación en Markdown
    ├── 00-analisis-problema.md
    ├── 01-analisis-requerimientos.md
    ├── 02-product-backlog.md
    └── 03-planificacion-sprints.md
```

Los prompts de las tareas programadas **no** viven en el repo del proyecto: se generan desde `jobs/` del repo central al crear las tareas, con los valores del config.

## Project de claude.ai

```
<Project>
├── Instrucciones del Project     rol, producto, cliente, reglas de tickets, Jira
├── docs/…                        los mismos docs de planificación del repo
├── factory/config.md             copia del config del repo
└── (skill de la cuenta)          ticket-quality
```

## Quién carga qué

| Dónde corre Claude | Config | Skills | Prompt |
|---|---|---|---|
| Project de claude.ai (planificar, crear tickets) | Doc `factory/config.md` del Project | `ticket-quality` como skill de la cuenta | La conversación |
| Claude Code de un developer en el repo | `CLAUDE.md` → `@factory/config.md` | Plugin `dark-factory` (activado por `.claude/settings.json`; al abrir el repo Claude Code ofrece instalarlo) | El developer |
| Runner implementar / revisar (tarea programada) | Clona el repo del proyecto: `CLAUDE.md` → config | Plugin `dark-factory`; si la sesión no lo tiene instalado, el prompt lee la skill directo del repo central | `jobs/factory-*.md` con variables reemplazadas |
| Controles de tablero (tarea programada) | Valores pegados en el prompt al crear la tarea | No usan skills | `jobs/daily-check.md`, `weekly-review.md`, `sprint-review.md` |

Las skills del plugin se invocan con el prefijo del plugin: `dark-factory:ticket-quality`, `dark-factory:factory-implement`, `dark-factory:factory-review`.

## Fuente única de cada cosa

| Pieza | Original | Copias | Cómo se actualizan |
|---|---|---|---|
| Playbook, checklist, plantillas, prompts | Repo central | Ninguna | Commit en el repo central. |
| Skills | `plugins/dark-factory/skills/` | Caché del plugin en cada máquina | Subir `version` en `plugin.json`; Claude Code actualiza el plugin. |
| `ticket-quality` en claude.ai | `plugins/dark-factory/skills/ticket-quality/` | Skill de la cuenta | Volver a guardar la skill desde el archivo del repo. |
| Config del proyecto | `factory/config.md` del repo del proyecto | Doc del Project + valores en los controles de tablero | Copiar al Project; volver a correr el prompt de creación de `jobs/README.md`. |

## El ciclo implementar ↔ revisar

Dos tareas programadas distintas: cada corrida es una sesión nueva, así que el reviewer nunca ve el razonamiento del implementador.

```
Persona pone factory:go
        │
        ▼
[Runner implementar] ── pre-flight falla ──► factory:blocked + comentario
        │ OK
        ▼
PR abierto + factory:review
        │
        ▼
[Runner revisar] ── CAMBIOS ──► factory:changes ──► [Runner implementar] corrige ──► factory:review ──┐
        │                                                                                            │
        │◄───────────────────────────────────────────────────────────────────────────────────────────┘
        │ APROBADO                         ronda máxima sin aprobación
        ▼                                          │
factory:approved ──► developer revisa y mergea     ▼
                                          factory:needs-human
```

| Etiqueta | Quién la pone | Significa |
|---|---|---|
| `factory:ready` | `ticket-quality` | Cumple el Definition of Ready. |
| `factory:go` | Una persona | Autoriza a la fábrica a tomarlo. |
| `factory:wip` | Runner implementar | Lo está trabajando una corrida; evita que dos corridas tomen el mismo ticket. |
| `factory:review` | Runner implementar | PR listo para revisar. |
| `factory:changes` | Runner revisar | Hallazgos pendientes de corregir. |
| `factory:approved` | Runner revisar | Listo para el developer. |
| `factory:needs-human` | Cualquiera de los dos runners | Sin acuerdo tras la ronda máxima, o algo que no puede resolver solo. |
| `factory:blocked` | Runner implementar | No pasó el pre-flight. |

Las etiquetas de estado del PR (`review`, `changes`, `approved`, `needs-human`) van en el PR y se reflejan en el ticket.

## Modelos sugeridos

| Rol | Modelo | Por qué |
|---|---|---|
| Runner implementar | Sonnet | Tickets ≤5 SP con criterios claros. Si los reviews rebotan seguido, subir a Opus. |
| Runner revisar | Opus | Es la compuerta antes del humano: conviene que sea el más exigente. Usar un modelo distinto al del implementador también ayuda a no repetir los mismos puntos ciegos. |
| Control diario | Haiku | Reglas mecánicas. |
| Review de mitad de sprint | Sonnet | Lectura del tablero y recomendaciones. |
| Evaluación de cierre | Opus | Cruza requisitos, plan y estado real; de ahí salen decisiones de recorte. |
