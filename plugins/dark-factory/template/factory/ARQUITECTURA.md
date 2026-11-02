# Arquitectura

## Tres capas

| Capa | Dónde vive | Qué tiene | Cómo cambia |
|---|---|---|---|
| **Sistema** | Repo [dark-factory](https://github.com/EnmanuelVarelaPividori/dark-factory), instalado como plugin | Skills y la plantilla de `factory/` | Commit en dark-factory + subir versión |
| **Plantilla instalada** | `factory/` de cada repo (todo menos `proyecto/`) | Playbook, reglas, checklist, prompts de jobs, formato de ticket | `/dark-factory:factory-setup` la actualiza; no se edita a mano |
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

## Project de claude.ai

El Project del proyecto replica las mismas rutas, para que las skills funcionen igual en los dos lados:

```
<Project>
├── Instrucciones        rol, producto, cliente, reglas de tickets
├── factory/proyecto/config.md
└── factory/proyecto/docs/00…03-*.md
```

Cuando un doc cambia en el Project, se copia al repo (y al revés). El repo es lo que leen los runners.

## Quién carga qué

| Dónde corre Claude | Config y reglas | Skills | Instrucción |
|---|---|---|---|
| Project de claude.ai | Docs `factory/proyecto/…` del Project | `ticket-quality` como skill de la cuenta | La conversación |
| Claude Code de un developer | `CLAUDE.md` importa config y reglas | Plugin (Claude Code ofrece instalarlo al abrir el repo) | El developer |
| Tarea programada (cualquier job) | Clona el repo: `CLAUDE.md` + `factory/` | Plugin; si falta, el job lee la skill del repo dark-factory | `factory/jobs/<job>.md` |

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
2. En cada proyecto: `/dark-factory:factory-setup`. Compara `factory/VERSION` con la del plugin y reemplaza los archivos genéricos. **Nunca toca `factory/proyecto/`.**
