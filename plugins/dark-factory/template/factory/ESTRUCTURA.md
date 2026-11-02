# Estructura esperada — instrucciones para agentes de IA

Este repo usa **Dark Factory**. Este archivo le dice a cualquier agente de IA (Claude Code, Codex u otro) qué estructura esperar, qué puede tocar y dónde va cada cosa. Es genérico: es igual en todos los proyectos.

## Árbol esperado

```
<raíz del repo>/
├── CLAUDE.md                  Contexto para Claude Code. Importa factory/ESTRUCTURA.md, factory/reglas.md y factory/proyecto/config.md
├── AGENTS.md                  Mismo propósito para otros agentes: apunta a este archivo
├── .claude/
│   └── settings.json          Activa el plugin dark-factory (extraKnownMarketplaces + enabledPlugins)
└── factory/
    ├── README.md              GENÉRICO · Flujo y mapa del sistema
    ├── ESTRUCTURA.md          GENÉRICO · Este archivo
    ├── ARQUITECTURA.md        GENÉRICO · Quién carga qué, ciclo de etiquetas, actualización
    ├── CHECKLIST.md           GENÉRICO · Puesta en marcha y verificación
    ├── reglas.md              GENÉRICO · Definition of Ready / Done, tablero, etiquetas, review
    ├── VERSION                GENÉRICO · Versión de la plantilla instalada
    ├── templates/
    │   └── ticket.md          GENÉRICO · Formato de ticket
    ├── jobs/
    │   ├── README.md          GENÉRICO · Calendario y prompt de las tareas programadas
    │   ├── daily-check.md     GENÉRICO · Job: control diario
    │   ├── weekly-review.md   GENÉRICO · Job: review de mitad de sprint
    │   ├── sprint-review.md   GENÉRICO · Job: evaluación de cierre
    │   ├── factory-implement.md  GENÉRICO · Job: runner implementar
    │   └── factory-review.md     GENÉRICO · Job: runner revisar
    └── proyecto/              PROPIO DEL PROYECTO
        ├── config.md          Valores del proyecto: Jira, equipo, sprint, reglas, repo, reportes
        └── docs/
            ├── README.md                       GENÉRICO · Qué va en cada doc y con qué formato
            ├── 00-analisis-problema.md
            ├── 01-analisis-requerimientos.md   Requisitos con IDs R-x.y
            ├── 02-product-backlog.md           Stories con campo Requisitos
            └── 03-planificacion-sprints.md
```

## Reglas para agentes

1. **Archivos GENÉRICOS: no se editan en el proyecto.** Vienen del plugin dark-factory y se reemplazan al actualizar. Si encontrás algo para mejorar, decilo (o proponé el cambio en el repo dark-factory); no lo cambies acá.
2. **Lo propio del proyecto va solo en `factory/proyecto/`.** Valores en `config.md`; alcance en `docs/`. No crees archivos de configuración de la fábrica en otro lugar.
3. **Rutas y nombres fijos.** Los archivos genéricos referencian `factory/proyecto/config.md` y `factory/proyecto/docs/0X-*.md` por nombre. No los renombres ni los muevas.
4. **Leé el config antes de actuar.** Cualquier valor (clave de Jira, rama base, comandos de tests, umbrales) sale de `factory/proyecto/config.md`. No asumas valores ni los copies a otros archivos.
5. **Docs = fuente de verdad del alcance.** Antes de implementar, leé la story en `02-product-backlog.md` y sus requisitos en `01-analisis-requerimientos.md`. Si el ticket contradice los docs, comentalo en el ticket y en el PR; no elijas en silencio.
6. **Reglas de trabajo en `factory/reglas.md`.** En particular: solo tickets con `factory:ready` y `factory:go`, un ticket = una rama = un PR, sin cambios de alcance, nunca mergear.
7. **Archivos fuera de `factory/`.** `CLAUDE.md`, `AGENTS.md` y `.claude/settings.json` pueden tener contenido propio del repo además de lo de la fábrica: agregá, no reemplaces.

## Si falta algo

| Situación | Qué hacer |
|---|---|
| No existe `factory/` o falta un archivo genérico | Correr `/dark-factory:factory-setup` (o pedirle a una persona que lo haga). |
| `factory/VERSION` distinta a la del plugin | Correr `/dark-factory:factory-setup` para actualizar. |
| `config.md` tiene filas `COMPLETAR` que necesitás | Pedirle el valor a una persona; no inventarlo. |
| Faltan docs en `factory/proyecto/docs/` | Se escriben en el Project de claude.ai y se copian acá. |
