# Docs de planificación

Fuente de verdad del alcance del proyecto. Se trabajan en el Project de claude.ai y se copian acá con los mismos nombres: los leen el implementador, el reviewer y la evaluación de cierre.

| Archivo | Qué contiene | Formato que esperan las skills y los jobs |
|---|---|---|
| `00-analisis-problema.md` | El porqué: contexto, problema central, problemas identificados, necesidades, fuera de alcance, restricciones | Libre |
| `01-analisis-requerimientos.md` | Qué se construye y con qué prioridad | Cada requisito en una fila con ID `R-<sección>.<n>` y prioridad `MUST` / `SHOULD` / `COULD` |
| `02-product-backlog.md` | User stories por épica | Cada story: `### US-xxx — Título`, línea de metadatos con prioridad, SP, épica, sprint y clave de Jira, **Historia**, **Criterios de aceptación** como checklist, **Dependencias** y **Requisitos** (IDs `R-x.y`) |
| `03-planificacion-sprints.md` | Sprints, tickets por sprint, velocidad supuesta | Tabla resumen por sprint y detalle por sprint con US, Jira, prioridad y SP |

Reglas:

- Si los docs se contradicen, se anota y se resuelve; no se elige en silencio.
- Un cambio de alcance se refleja primero acá y después en los tickets.
- Los nombres de archivo no cambian: los archivos genéricos de `factory/` los referencian por nombre.
