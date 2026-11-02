# Checklist de puesta en marcha

`/dark-factory:factory-setup` hace y verifica todo lo que puede, y al final lista lo que necesita a una persona. Esta lista es la referencia completa. Cada bloque termina con una verificación.

## 1. Planificación (Project de claude.ai)

- [ ] Project creado con instrucciones: rol, producto, cliente, reglas de tickets, Jira.
- [ ] Docs en Markdown en el Project con las rutas de `factory/proyecto/docs/` (ver `factory/proyecto/docs/README.md`).
- [ ] Requerimientos con IDs `R-x.y`; cada story del backlog con su campo `Requisitos`.
- [ ] Contradicciones entre docs resueltas y anotadas.
- [ ] **Verificación:** en el Project, "¿qué requisitos cubre US-xxx?" responde lo mismo que el backlog.

## 2. Repo

- [ ] Plugin instalado y `/dark-factory:factory-setup` corrido.
- [ ] `factory/` con la plantilla; `factory/VERSION` igual a la del plugin.
- [ ] `factory/proyecto/config.md` completo (sin filas `COMPLETAR`).
- [ ] `factory/proyecto/docs/` con los mismos docs que el Project.
- [ ] `CLAUDE.md` importa `@factory/ESTRUCTURA.md`, `@factory/reglas.md` y `@factory/proyecto/config.md`; `AGENTS.md` apunta a `factory/ESTRUCTURA.md`.
- [ ] `.claude/settings.json` activa `dark-factory@dark-factory`.
- [ ] `factory/proyecto/config.md` y los docs también en el Project, con las mismas rutas.
- [ ] **Verificación:** una sesión nueva de Claude Code en el repo responde bien "¿dónde está el backlog y cómo se nombra una rama?".

## 3. Jira y GitHub

- [ ] Épicas creadas y con clave anotada en el backlog.
- [ ] Estados del board incluyen "In Review".
- [ ] Etiquetas de PR creadas en GitHub: `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`.
- [ ] Rama base protegida: merge solo con review humano.
- [ ] **Verificación:** una JQL simple sobre el proyecto responde desde el Project y desde Claude Code.

## 4. Skills

- [ ] `ticket-quality` guardada como skill de la cuenta en claude.ai (para crear tickets desde el Project).
- [ ] En Claude Code, `/plugin` muestra `dark-factory` habilitado.
- [ ] Si el repo dark-factory es privado: todos los que usan el plugin y las tareas programadas tienen acceso de lectura.
- [ ] **Verificación:** un ticket a propósito incompleto hace que `ticket-quality` pregunte en vez de crear; uno duplicado se detecta.

## 5. Tareas programadas

- [ ] Horarios calculados con `factory/jobs/README.md` a partir del config.
- [ ] Control diario (Haiku), review de mitad de sprint (Sonnet; solo con sprints de 2+ semanas), evaluación de cierre (Opus).
- [ ] Si `fabrica.disparo` = A: runner implementar (Sonnet) y runner revisar (Opus), con acceso de push al repo.
- [ ] Todas con el prompt corto de `factory/jobs/README.md`, conector de Atlassian, acceso al repo y notificaciones según `reportes.canales`.
- [ ] Aprobación automática en los controles de tablero (solo leen).
- [ ] **Verificación:** cada tarea ejecutada una vez a mano; el reporte llega con datos correctos.

## 6. Smoke test de la fábrica

- [ ] Un ticket chico (1–2 SP), Ready y con `factory:go`, recorre todo: PR con checklist de criterios → review con veredicto → corrección si hace falta → `factory:approved` → merge humano → ticket en Done.
- [ ] Un ticket sin `factory:ready` pero con `factory:go` termina en `factory:blocked` con un comentario de qué falta, sin código.

## 7. Equipo

- [ ] El equipo conoce el flujo y las reglas de `factory/reglas.md`.
- [ ] Limpieza inicial del backlog si viene de antes: duplicados, tickets bitácora y tickets sin requisito se cierran o se reescriben con `ticket-quality`.
- [ ] Velocidad real medida en los primeros 2 sprints y actualizada en el config.
- [ ] Mejoras a la plantilla detectadas en este proyecto llevadas al repo dark-factory, no editadas en la copia local.
