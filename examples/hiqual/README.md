# Ejemplo completo: instalación en HiQual

Así queda el sistema instalado en un proyecto real. Todos los archivos están listos para copiar y pegar: las variables `{{...}}` ya están reemplazadas con los valores de `factory/config.md`. Los valores marcados **COMPLETAR** no se pudieron sacar del repo ni de los docs.

## Qué va en el repo de HiQual

Solo esto. Todo lo demás queda en el repo dark-factory.

```
hiqual/                                ← raíz del repo del proyecto
├── CLAUDE.md                          ← examples/hiqual/CLAUDE.md
├── .claude/
│   └── settings.json                  ← examples/hiqual/.claude/settings.json (activa el plugin dark-factory)
├── factory/
│   └── config.md                      ← examples/hiqual/factory/config.md
└── docs/
    └── p1-iec-62052-31/               ← docs de planificación (ya están en el repo)
        ├── 00-analisis-problema.md
        ├── 01-analisis-requerimientos.md
        ├── 02-product-backlog.md
        └── 03-planificacion-sprints.md
```

## Qué va en las tareas programadas

Los prompts **no** se guardan en el repo del proyecto: se pegan en cada tarea programada. Están acá como referencia, ya llenos.

| Archivo | Tarea programada | Cron | Modelo |
|---|---|---|---|
| `tareas-programadas/daily-check.md` | [HIQ] Control diario | `CRON_TZ=Europe/Amsterdam 10 9 * * 1-5` | Haiku |
| `tareas-programadas/weekly-review.md` | [HIQ] Review de mitad de sprint | `CRON_TZ=Europe/Amsterdam 40 10 * * 3` | Sonnet |
| `tareas-programadas/sprint-review.md` | [HIQ] Evaluación de cierre de sprint | `CRON_TZ=Europe/Amsterdam 10 8 * * 1` | Opus |
| `tareas-programadas/factory-implement.md` | [HIQ] Fábrica — implementar | `CRON_TZ=Europe/Amsterdam 12 8-18/2 * * 1-5` | Sonnet |
| `tareas-programadas/factory-review.md` | [HIQ] Fábrica — revisar | `CRON_TZ=Europe/Amsterdam 47 9-19/2 * * 1-5` | Opus |

De cada archivo se copia la sección **Prompt**; el encabezado dice nombre, cron, modelo, conectores y notificaciones.

## Qué va en el Project de claude.ai

```
Proyecto ISO (Project)
├── Instrucciones del Project          ← rol, producto, cliente, reglas de tickets, Jira
├── docs/00…03-*.md                    ← mismos docs que docs/p1-iec-62052-31/ del repo
├── factory/config.md                  ← copia de examples/hiqual/factory/config.md
└── Skill de la cuenta: ticket-quality ← desde plugins/dark-factory/skills/ticket-quality/
```

## Otros archivos

| Archivo | Para qué |
|---|---|
| `ejemplo-ticket-HIQ-89.md` | Cómo se ve un ticket que pasa el Definition of Ready (US-064, Dashboard inicial). |

## Para otro proyecto

1. Copiar `config.example.md` al repo del proyecto como `factory/config.md` y completarlo. Este ejemplo sirve de referencia.
2. Copiar `templates/settings.json` a `.claude/settings.json`.
3. Generar `CLAUDE.md` desde `templates/CLAUDE.md` y los prompts de las tareas desde `jobs/`, reemplazando las variables, o pedírselo a Claude con el prompt de `jobs/README.md`.
4. Seguir `CHECKLIST.md`.
