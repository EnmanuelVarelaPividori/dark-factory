# Plantilla de ticket

Título: `[CAPA] US-xxx — Verbo + objeto` (la capa solo si el proyecto la usa)

```markdown
**Épica:** EP-xx · **Requisitos:** R-x.y, R-x.z · **Tipo:** núcleo | configuración | específico de cliente
**Módulo de producto:** <módulo> · **Prioridad:** MUST | SHOULD | COULD · **SP:** 1 | 2 | 3 | 5
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

## Notas para la fábrica (opcional)
<Archivos o módulos tocados, restricciones técnicas, fuera de alcance de este ticket.>
```

## Cómo se ve un criterio verificable

| Malo | Bueno |
|---|---|
| "El sistema es rápido." | "La búsqueda de documentos responde en <2 s con 5.000 documentos cargados." |
| "Se puede firmar el NDA." | "Al primer ingreso, un usuario sin firma no accede a ninguna ruta salvo `/nda`; tras firmar, se registra usuario, documento, versión y fecha/hora, y el registro no es editable." |
| "Funciona bien con permisos." | "Un Operador recibe 403 al intentar aprobar; un Responsable de Calidad puede aprobar." |
