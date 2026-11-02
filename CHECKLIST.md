# Checklist de puesta en marcha

Copiar al proyecto nuevo y marcar en orden. Cada bloque termina con una verificación: si falla, el bloque no está listo.

**Proyecto:** ______________________ · **Responsable:** ____________ · **Fecha de inicio:** ________

## 1. Planificación (Project de claude.ai)

- [ ] Project creado con instrucciones: rol, producto, cliente, reglas de tickets, Jira.
- [ ] Docs en Markdown cargados en el Project: análisis de problema, requerimientos (con IDs `R-x.y`), backlog (con `Requisitos` por story), plan de sprints.
- [ ] Contradicciones entre docs resueltas y anotadas.
- [ ] **Verificación:** se pregunta en el Project "¿qué requisitos cubre US-xxx?" y la respuesta coincide con el backlog.

## 2. Configuración

> Atajo: instalar el plugin y correr `/dark-factory:factory-setup` en el repo del proyecto. Cubre los bloques 2, 4, 5 y 6 salvo los ítems que requieren a una persona, y al final reporta qué falta.

- [ ] `factory/config.md` creado en el repo del proyecto a partir de `plugins/dark-factory/templates/config.example.md` de dark-factory (Jira, equipo, velocidad, repo, reportes). Es lo único de la fábrica que vive en el repo del proyecto.
- [ ] Los mismos docs de planificación copiados al repo en `docs.ruta`.
- [ ] `CLAUDE.md` del repo creado desde `plugins/dark-factory/templates/CLAUDE.md`, con la línea `@factory/config.md` que importa el config.
- [ ] Copia de `factory/config.md` subida al Project como doc `factory/config.md` (para `ticket-quality`).
- [ ] **Verificación:** una sesión de Claude Code nueva en el repo responde correctamente "¿dónde está el backlog y cómo se nombra una rama?".

## 3. Jira

- [ ] Etiquetas `factory:ready` y `factory:go` creadas (el resto del ciclo en el bloque 5; ver `ARQUITECTURA.md`).
- [ ] Épicas creadas y con clave anotada en el backlog.
- [ ] Estados del board incluyen "In Review".
- [ ] Límite de WIP configurado en el board (si Jira lo permite) y en `config.md`.
- [ ] **Verificación:** los conectores de Atlassian funcionan desde el Project y desde Claude Code (una búsqueda JQL simple devuelve resultados).

## 4. Skills

- [ ] `ticket-quality` guardada como skill de la cuenta en claude.ai (desde `plugins/dark-factory/skills/ticket-quality/SKILL.md`).
- [ ] `.claude/settings.json` del repo creado desde `plugins/dark-factory/templates/settings.json` (activa el plugin `dark-factory`) y commiteado.
- [ ] Al abrir el repo en Claude Code, el plugin se instala; `/plugin` lo muestra habilitado y las skills aparecen como `dark-factory:factory-implement` y `dark-factory:factory-review`.
- [ ] **Verificación:** se pide crear un ticket a propósito incompleto y la skill pregunta lo que falta en vez de crearlo; se pide uno duplicado y lo detecta.

## 5. Fábrica

- [ ] Disparo elegido (A: etiqueta + tarea programada · B: automatización) y anotado en `config.md`.
- [ ] Si es A: tareas `Fábrica — implementar` (Sonnet) y `Fábrica — revisar` (Opus) creadas desde `plugins/dark-factory/jobs/factory-implement.md` y `plugins/dark-factory/jobs/factory-review.md`, con Atlassian, el repo del proyecto (push) y el repo dark-factory (lectura) conectados.
- [ ] Etiquetas del ciclo creadas en Jira y GitHub: `factory:wip`, `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`, `factory:blocked`.
- [ ] Si es B: Jira Automation → webhook → CI con Claude Code; secretos configurados.
- [ ] Límite de rondas de review fijado (`fabrica.rondas_review`).
- [ ] Si el repo dark-factory es privado: quienes usan el plugin y las tareas programadas tienen acceso de lectura.
- [ ] Rama base protegida: merge solo con review humano.
- [ ] **Verificación (smoke test):** un ticket chico (1–2 SP), Ready y con `factory:go`, recorre todo el flujo: PR abierto con checklist de criterios → review con veredicto → corrección si hace falta → `factory:approved` → merge humano → ticket en Done.
- [ ] **Verificación negativa:** un ticket sin `factory:ready` con `factory:go` termina en `factory:blocked` con un comentario de qué falta, sin código.

## 6. Controles recurrentes

- [ ] Variables de sprint completas en `config.md`: `sprint.duracion_semanas`, `sprint.dia_inicio`, `sprint.fecha_referencia`, `equipo.zona_horaria`, `equipo.hora_daily`.
- [ ] Horarios calculados con la tabla de `plugins/dark-factory/jobs/README.md`.
- [ ] Control diario creado (Haiku, push + email).
- [ ] Review de mitad de sprint creado (Sonnet, push + email); solo si el sprint dura 2 semanas o más.
- [ ] Evaluación de cierre creada (Opus, push + email).
- [ ] Placeholders `{{...}}` reemplazados en los prompts: no queda ninguno.
- [ ] Aprobación automática activada en las tres (solo leen).
- [ ] **Verificación:** cada tarea se ejecutó una vez a mano y el reporte llegó al teléfono y al email con datos correctos.

## 7. Equipo

- [ ] El equipo conoce el flujo (5 pasos) y las reglas: Definition of Ready, WIP, nada de tickets bitácora, `factory:go` lo pone una persona.
- [ ] Limpieza inicial del backlog si viene de antes: duplicados, tickets bitácora y tickets sin requisito se cierran o se reescriben con `ticket-quality`.
- [ ] Velocidad real medida en los primeros 2 sprints y actualizada en `config.md`.

## 8. Sistema en marcha

- [ ] Primer sprint completo con el flujo.
- [ ] Primer `sprint-review` revisado en la planning siguiente.
- [ ] Mejoras a la plantilla detectadas en este proyecto devueltas a `factory/` (no solo a la copia local).
