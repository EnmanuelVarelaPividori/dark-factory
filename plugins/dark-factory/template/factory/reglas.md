# Reglas de la fábrica

Los valores numéricos (WIP, SP máximos, rondas…) están en `factory/proyecto/config.md`. Acá están las reglas.

## Definition of Ready

Un ticket puede entrar a un sprint y a la fábrica solo si cumple todo:

- [ ] Historia en formato COMO / QUIERO / PARA.
- [ ] Descripción con un ejemplo concreto.
- [ ] Criterios de aceptación verificables: cada uno se marca cumplido / no cumplido mirando el sistema o un test.
- [ ] Trazado a un requisito `R-x.y` de `factory/proyecto/docs/01-analisis-requerimientos.md` o, si no tiene, justificación explícita.
- [ ] Épica asignada y dependencias listadas; las bloqueantes están Done o en el mismo sprint antes que él.
- [ ] Estimación ≤ `reglas.sp_max_ticket`. Si es mayor, se divide antes de entrar.
- [ ] Tipo: núcleo, configuración o específico de cliente.
- [ ] Sin duplicados en el backlog.

Solo un ticket que cumple todo recibe `factory:ready`. Sin esa etiqueta, la fábrica no lo toca.

## Definition of Done

- [ ] Todos los criterios de aceptación verificados en el PR, uno por uno, con evidencia (test, captura o paso de verificación).
- [ ] Review automático aprobado y review humano aprobado.
- [ ] Tests (`repo.tests`) pasan en CI.
- [ ] Docs de `factory/proyecto/docs/` actualizados si el ticket cambió algo que describen.
- [ ] Ticket en Done con link al PR mergeado.

## Reglas de tablero

- **WIP:** máximo `reglas.wip_por_persona` tickets In Progress por persona.
- **Capacidad:** la suma de SP del sprint no supera `equipo.cantidad × sprint.velocidad_por_persona`. Lo que no entra va al backlog.
- **Nada de tickets bitácora.** Si el trabajo ya está hecho, va como comentario o subtarea del ticket existente. Si no hay ticket, se crea uno que cumpla el DoR antes de seguir trabajando, no después.
- **Lotes:** más de `reglas.lote_confirmacion` tickets en una carga exige mostrar el borrador y confirmar antes de crear.
- **Agregar al sprint en curso** es cambio de alcance: el control diario lo reporta.

## Etiquetas

| Etiqueta | Dónde | Quién la pone | Significa |
|---|---|---|---|
| `factory:ready` | Ticket | `ticket-quality` | Cumple el Definition of Ready. |
| `factory:go` | Ticket | Una persona | Autoriza a la fábrica a tomarlo. |
| `factory:wip` | Ticket | Runner implementar | Lo está trabajando una corrida; evita que dos corridas tomen el mismo. |
| `factory:blocked` | Ticket | Runner implementar | No pasó el pre-flight; el comentario dice qué falta. |
| `factory:review` | PR | Runner implementar | PR listo para revisar. |
| `factory:changes` | PR | Runner revisar | Hallazgos pendientes de corregir. |
| `factory:approved` | PR | Runner revisar | Listo para el developer. |
| `factory:needs-human` | PR y ticket | Cualquier runner | Sin acuerdo tras la ronda máxima, o algo que no puede resolver solo. |

## Ciclo de review

1. El runner implementar abre el PR con la checklist de criterios de aceptación y pone `factory:review`.
2. El runner revisar corre en una sesión **nueva**, sin el razonamiento del implementador. Responde `APROBADO` o `CAMBIOS` con hallazgos numerados.
3. Con `CAMBIOS` pone `factory:changes`; el implementador corrige solo esos hallazgos y vuelve a `factory:review`.
4. Máximo `fabrica.rondas_review` rondas. Sin aprobación en la última: `factory:needs-human` y resumen del desacuerdo. Sin límite, dos agentes pueden discutir sin fin.
5. Con `APROBADO`: `factory:approved`, y el developer revisa y mergea.

## Nunca

- Mergear sin una persona.
- Trabajar un ticket sin `factory:ready` y `factory:go`.
- Cambiar el alcance de un ticket: lo extra se propone como ticket nuevo.
- Elegir en silencio cuando un ticket contradice los docs: se comenta en el ticket y en el PR.
