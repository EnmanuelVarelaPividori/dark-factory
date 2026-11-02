# Dark Factory

Plantilla y plugin de Claude Code para pasar de la planificación al código con Claude: docs de planificación → tickets con calidad → implementación → review contra el ticket → merge humano. Con controles de tablero automáticos (diario, mitad de sprint, cierre).

El sistema es **genérico**: no nombra ningún proyecto. Cada proyecto recibe la misma carpeta `factory/` y pone lo propio en `factory/proyecto/`.

## Instalar en un proyecto

En Claude Code, dentro del repo del proyecto:

```
/plugin install dark-factory --marketplace EnmanuelVarelaPividori/dark-factory
/dark-factory:factory-setup
```

El setup copia la plantilla, detecta lo que puede del repo y de Jira, pregunta el resto, conecta `CLAUDE.md`, `AGENTS.md` y `.claude/settings.json`, arma las tareas programadas y verifica la checklist. Volver a correrlo actualiza la plantilla sin tocar `factory/proyecto/`.

## Qué queda en cada proyecto

```
<repo>/
├── CLAUDE.md                  importa factory/ESTRUCTURA.md, factory/reglas.md, factory/proyecto/config.md
├── AGENTS.md                  lo mismo para otros agentes
├── .claude/settings.json      activa el plugin
└── factory/
    ├── README.md  ESTRUCTURA.md  ARQUITECTURA.md  CHECKLIST.md  reglas.md  VERSION    genéricos
    ├── templates/ticket.md                                                         genérico
    ├── jobs/  README.md  daily-check.md  weekly-review.md  sprint-review.md
    │          factory-implement.md  factory-review.md                              genéricos
    └── proyecto/                                                                   propio del proyecto
        ├── config.md
        └── docs/  00-analisis-problema.md  01-analisis-requerimientos.md
                   02-product-backlog.md    03-planificacion-sprints.md
```

Detalle: [`factory/ESTRUCTURA.md`](plugins/dark-factory/template/factory/ESTRUCTURA.md) (para agentes) y [`factory/README.md`](plugins/dark-factory/template/factory/README.md) (flujo).

## Este repo

```
dark-factory/
├── README.md                          Este archivo
├── CLAUDE.md, AGENTS.md               Instrucciones para agentes que mantienen la plantilla
├── .claude-plugin/marketplace.json    Marketplace de plugins de Claude Code
└── plugins/dark-factory/
    ├── .claude-plugin/plugin.json     Manifiesto y versión del plugin
    ├── skills/
    │   ├── factory-setup/             Instalar, actualizar, verificar
    │   ├── ticket-quality/            Tickets que cumplen el Definition of Ready
    │   ├── factory-implement/         Implementar un ticket Ready
    │   └── factory-review/            Revisar un PR contra su ticket
    └── template/                      Lo que se copia a cada repo, con la misma forma que la raíz del repo
        ├── CLAUDE.md, AGENTS.md
        ├── .claude/settings.json
        └── factory/
```

## Skills

| Skill | Dónde se usa | Para qué |
|---|---|---|
| `dark-factory:factory-setup` | Claude Code en el repo | Instalar, actualizar o verificar la fábrica. |
| `dark-factory:ticket-quality` | Project de claude.ai (como skill de la cuenta) o Claude Code | Crear o refinar tickets. |
| `dark-factory:factory-implement` | Tarea programada o Claude Code | Implementar un ticket y corregir hallazgos. |
| `dark-factory:factory-review` | Tarea programada | Revisar un PR en una sesión independiente. |

Para usar `ticket-quality` en claude.ai, guardá `plugins/dark-factory/skills/ticket-quality/SKILL.md` como skill de tu cuenta.

## Acceso

Si este repo es privado, cada persona y cada tarea programada que use el plugin necesita acceso de lectura.
