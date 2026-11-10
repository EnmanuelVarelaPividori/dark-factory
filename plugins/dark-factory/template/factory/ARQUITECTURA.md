# Arquitectura

## Tres capas

| Capa | Dónde vive | Qué tiene | Cómo cambia |
|---|---|---|---|
| **Sistema** | Repo [dark-factory](https://github.com/EnmanuelVarelaPividori/dark-factory), instalado como plugin | Skills y la plantilla de `factory/` | Commit en dark-factory + subir versión |
| **Plantilla instalada** | `factory/` de cada repo (todo menos `proyecto/`) | Playbook, reglas, checklist, prompts de jobs, formato de ticket | `/dark-factory:factory-kickoff` la actualiza; no se edita a mano |
| **Proyecto** | `factory/proyecto/` de cada repo | `config.md` y los docs de planificación | Se edita en el proyecto (y en su Project de claude.ai) |

Los archivos genéricos nunca nombran un proyecto: leen `factory/proyecto/config.md` y `factory/proyecto/docs/` por rutas fijas.

## Qué se agrega a cada repo

```
<repo>/
├── CLAUDE.md                importa factory/ESTRUCTURA.md, factory/reglas.md y factory/proyecto/config.md
├── AGENTS.md                lo mismo para agentes que no son Claude (apunta a factory/ESTRUCTURA.md)
├── .claude/settings.json    activa el plugin dark-factory
└── factory/                 plantilla genérica + proyecto/
```

Si el repo ya tenía `CLAUDE.md`, `AGENTS.md` o `.claude/settings.json`, el setup les agrega lo necesario sin pisar nada. La estructura completa y las reglas para agentes están en `factory/ESTRUCTURA.md`.

## Repo y Project: qué va en cada uno

| | Repo (`factory/proyecto/`) | Project de claude.ai (`claude.project`) |
|---|---|---|
| Para qué | Lo que necesita el código: general y estable | Todo el trabajo de planificación, con el detalle |
| Contenido | `config.md` y los 4 docs generales (problema, requisitos `R-x.y`, backlog, plan) | Los mismos 4 docs + investigación detallada, decisiones, diagramas, actas, borradores |
| Quién lo lee | Todos los agentes y runners | Quien planifica; los runners cuando un ticket cita un doc en **Contexto** |

El **ticket** es el puente: trae lo imprescindible para implementar y, en la sección **Contexto**, nombra los docs del Project que hay que mirar (ruta + para qué + obligatorio / referencia).

Convención de carpetas sugerida en el Project, para que las rutas citadas sean estables:

```
<Project>
├── Descripción e instrucciones         qué es el proyecto y contexto del dominio (no reglas de tickets ni Jira)
├── factory/proyecto/config.md          copia del repo
├── factory/proyecto/docs/00…03-*.md    copia del repo (o sincronizado desde GitHub)
├── investigacion/<tema>.md             research detallado
├── decisiones/<AAAA-MM-DD>-<tema>.md   decisiones con su porqué
└── diagramas/<tema>.md                 diagramas y flujos
```

Si una ruta del Project cambia, los tickets abiertos que la citan quedan desactualizados: conviene no mover docs citados.

## Quién carga qué

| Dónde corre Claude | Config y reglas | Skills | Instrucción |
|---|---|---|---|
| Project de claude.ai | Docs `factory/proyecto/…` del Project | `ticket-quality` como skill de la cuenta | La conversación |
| Claude Code de un developer | `CLAUDE.md` importa config y reglas | Plugin (Claude Code ofrece instalarlo al abrir el repo) | El developer |
| Tarea programada (cualquier job) | Clona el repo: `CLAUDE.md` + `factory/` | Plugin; si falta, el job lee la skill del repo dark-factory | `factory/jobs/<job>.md` |
| Runners creados dentro del Project | Lo anterior + los docs del Project que cite el ticket (`project_read`) | Ídem | Ídem |

## Prompt de cada tarea programada

Todas las tareas usan el mismo prompt; solo cambian el repo y el job:

```
Repo del proyecto: <url>  (rama <rama_base>)
Cloná el repo y ejecutá el job definido en factory/jobs/<job>.md. Seguí ese archivo al pie de la letra.
```

Cambiar un umbral, el equipo o la velocidad es editar `factory/proyecto/config.md`: las tareas lo leen en la próxima corrida. Solo hay que tocar la tarea si cambia el horario (largo del sprint, día de inicio, hora de la daily).

## Ciclo de etiquetas

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

## Actualizar la plantilla en un proyecto

1. Mejorar el archivo en dark-factory, subir `version` en `plugins/dark-factory/.claude-plugin/plugin.json` y la del archivo `template/factory/VERSION`.
2. En cada proyecto: `/dark-factory:factory-kickoff`. Compara `factory/VERSION` con la del plugin y reemplaza los archivos genéricos. **Nunca toca `factory/proyecto/`.**
