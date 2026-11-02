# Dark Factory

Sistema para pasar de la planificación al código con Claude, sin tableros inflados ni tickets usados como bitácora.

Esta carpeta es **igual en todos los proyectos**, salvo `proyecto/`, que tiene lo propio de cada uno. No edites los archivos genéricos en un proyecto: se mejoran en el repo [dark-factory](https://github.com/EnmanuelVarelaPividori/dark-factory) y se actualizan con `/dark-factory:factory-setup`.

## Estructura

```
factory/
├── README.md               Este archivo: flujo y mapa del sistema
├── ESTRUCTURA.md           Estructura esperada e instrucciones para agentes de IA
├── ARQUITECTURA.md         Quién carga qué, ciclo de etiquetas, cómo se actualiza
├── CHECKLIST.md            Puesta en marcha y verificación
├── reglas.md               Definition of Ready / Done, reglas de tablero, etiquetas, ciclo de review
├── VERSION                 Versión de la plantilla instalada
├── templates/
│   └── ticket.md           Formato de ticket
├── jobs/
│   ├── README.md           Calendario, cron y cómo crear las tareas programadas
│   ├── daily-check.md      Control diario del tablero
│   ├── weekly-review.md    Review de mitad de sprint
│   ├── sprint-review.md    Evaluación de cierre de sprint
│   ├── factory-implement.md  Runner: implementar un ticket
│   └── factory-review.md     Runner: revisar un PR
└── proyecto/               ← LO ÚNICO PROPIO DE ESTE PROYECTO
    ├── config.md           Jira, equipo, sprint, repo, reglas, reportes
    └── docs/
        ├── README.md           Qué va en cada doc y con qué formato (genérico)
        ├── 00-analisis-problema.md
        ├── 01-analisis-requerimientos.md   requisitos con IDs R-x.y
        ├── 02-product-backlog.md           stories con campo Requisitos
        └── 03-planificacion-sprints.md
```

Todos los archivos genéricos se refieren a `proyecto/` por estas rutas fijas. Por eso funcionan igual en cualquier proyecto.

En la raíz del repo, además: `CLAUDE.md` y `AGENTS.md` (instrucciones para agentes de IA, importan o apuntan a `ESTRUCTURA.md`) y `.claude/settings.json` (activa el plugin).

## El flujo

```
1. PLANIFICAR    Project de claude.ai: investigación, diagramas, docs → factory/proyecto/docs/
       │
2. TICKETEAR     Skill ticket-quality: duplicados, plantilla, preguntas, Definition of Ready → backlog
       │
3. FABRICAR      Runner implementar: toma un ticket Ready con factory:go y abre un PR
       │
4. REVISAR       Runner revisar: otra sesión revisa el PR contra ticket + docs. Ciclo revisar ↔ corregir (máx. N rondas)
       │
5. MERGEAR       Un developer revisa la versión final y mergea. Nada se mergea sin humano.
```

Las reglas de cada paso están en `reglas.md`.

## Tareas programadas

| Job | Frecuencia (derivada del largo del sprint) | Modelo sugerido | Qué responde |
|---|---|---|---|
| `jobs/daily-check.md` | Días hábiles, antes de la daily | Haiku | ¿Se rompió alguna regla de tablero hoy? |
| `jobs/weekly-review.md` | Semanal, a mitad de semana. No va con sprints de 1 semana. | Sonnet | ¿Cómo va el sprint y qué haría un scrum master esta semana? |
| `jobs/sprint-review.md` | Al cierre de cada sprint (corre semanal; reporta solo si cerró uno) | Opus | ¿Avanzamos contra el plan y los requisitos? ¿Llegamos a la fecha? |
| `jobs/factory-implement.md` | Cada 2 h en horario laboral | Sonnet | Implementa un ticket o corrige un PR. |
| `jobs/factory-review.md` | Cada 2 h, desfasado 1 h | Opus | Revisa un PR en una sesión nueva. |

Cada tarea programada lleva el mismo prompt corto: el repo del proyecto y qué job ejecutar. Todo lo demás lo lee del repo al correr. Detalle en `jobs/README.md`.

**Por qué esos modelos:** el diario son reglas mecánicas; el semanal lee el tablero y recomienda; el de cierre cruza requisitos, plan y estado real y de ahí salen decisiones de recorte. El implementador trabaja tickets chicos y claros; el reviewer es la compuerta antes del humano y conviene que sea el más exigente.

## Skills (plugin `dark-factory`)

| Skill | Para qué |
|---|---|
| `dark-factory:factory-setup` | Instalar, actualizar o verificar la fábrica en un repo. |
| `dark-factory:ticket-quality` | Crear o refinar tickets que cumplan el Definition of Ready. |
| `dark-factory:factory-implement` | Implementar un ticket Ready y corregir hallazgos del review. |
| `dark-factory:factory-review` | Revisar un PR contra su ticket y los docs. |

## Disparo de la fábrica

Se elige por proyecto en `proyecto/config.md` (`fabrica.disparo`):

| Opción | Cómo | Pros | Contras |
|---|---|---|---|
| **A. Etiqueta + tarea programada** (recomendada para empezar) | Los runners buscan cada 2 h tickets con `factory:go` y PRs con `factory:review` / `factory:changes`. | Sin infraestructura extra. Fácil de pausar. | Latencia de horas. |
| **B. Automatización** | Jira Automation dispara un webhook (por ejemplo a CI con Claude Code) al agregar `factory:go`. | Inmediato. | Requiere configurar Jira Automation, CI y secretos. |

En ambos casos `factory:go` lo pone una persona: la fábrica nunca se auto-asigna trabajo.
