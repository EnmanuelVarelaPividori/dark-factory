# Plantilla de ticket

Título: `[CAPA] US-xxx — Verbo + objeto` (la capa solo si el proyecto usa prefijos; ver `jira.prefijos_titulo` en el config).

```markdown
**Épica:** EP-xx · **Requisitos:** R-x.y, R-x.z · **Tipo:** núcleo | configuración | específico de cliente
**Módulo:** <módulo> · **Prioridad:** MUST | SHOULD | COULD · **SP:** 1 | 2 | 3 | 5
**Dependencias:** US-xxx (Done) · US-yyy (en este sprint)

## 1. Historia
COMO <rol>
QUIERO <acción>
PARA <beneficio>

## 2. Descripción y ejemplo
<Qué cambia y por qué, en 2–4 frases.>

Ejemplo: <caso concreto con datos reales: quién, qué carga, qué ve después.>

## 3. Criterios de aceptación
- [ ] CA1 — <condición verificable; cómo se comprueba>
- [ ] CA2 — …
- [ ] CA3 — …

## 4. Stakeholders
<Quién valida y quién usa el resultado.>

## 5. Contexto
- Requisitos: R-x.y — <título> (`factory/proyecto/docs/01-analisis-requerimientos.md`)
- Project «<claude.project>» › `<ruta/del/doc.md>` — <qué mirar ahí y por qué> · obligatorio | referencia
- Project «<claude.project>» › `<ruta/del/doc.md>` — …

## Notas para la fábrica (opcional)
<Módulos o archivos tocados, restricciones técnicas, fuera de alcance de este ticket.>
```

## Sección Contexto

- La investigación detallada (research, decisiones, diagramas, actas) vive en el Project de claude.ai, no en el repo. El ticket la nombra por **ruta dentro del Project**.
- **Lo imprescindible va en el ticket.** Si una decisión del Project define cómo se implementa, resumila en la descripción o en un criterio de aceptación; el doc es para profundizar.
- Marcá cada doc como **obligatorio** (sin leerlo no se puede implementar bien) o **referencia** (ayuda, pero el ticket alcanza).
- Sin docs relevantes en el Project: `- Project: no aplica`.

## Criterios verificables

| Malo | Bueno |
|---|---|
| "El sistema es rápido." | "La búsqueda responde en menos de 2 s con 5.000 registros cargados." |
| "Se puede aprobar." | "Un usuario con rol Aprobador cambia el estado de Borrador a Aprobado; queda registrado quién, cuándo y el estado anterior, y el registro no es editable." |
| "Funciona bien con permisos." | "Un usuario sin permiso de aprobación recibe 403 al intentar aprobar." |
| "Muestra los datos de la empresa." | "Un usuario de la empresa B no ve ningún registro de la empresa A (test automatizado)." |
