<!-- Ejemplo de ticket con la plantilla plugins/dark-factory/templates/ticket.md, a partir de US-064 del backlog.
     Título en Jira: [BE] US-064 — Dashboard inicial -->

**Épica:** EP-06 Datos Maestros y Reportería · **Requisitos:** §11 Dashboard · **Tipo:** núcleo
**Módulo de producto:** Análisis e indicadores · **Prioridad:** MUST · **SP:** 5
**Dependencias:** US-030 Registro de NC · US-040 Modelo de evento de calendario · US-020 Alta y ficha de documento (las tres Done)

## 1. Historia
COMO Dirección / Responsable de Calidad
QUIERO un dashboard inicial con los indicadores clave
PARA ver de un vistazo cómo está la empresa frente a la auditoría de certificación

## 2. Descripción y ejemplo
Página de inicio para los roles de gestión que reúne lo que hoy se consolida a mano antes de cada auditoría: NC abiertas, acciones por vencer, documentos e instrumentos por revisar, ensayos pendientes y auditorías planificadas, más el avance general hacia la certificación.

Ejemplo: la Responsable de Calidad entra el lunes y ve 7 NC abiertas (3 en "En análisis de causa", agrupadas por código de causa: TE12 ×3, TE18 ×2, TE29 ×2), 2 AC que vencen en los próximos 7 días, 1 instrumento con calibración vencida en 12 días y un avance de certificación del 64 %. Con un clic en "AC por vencer (7 días)" llega al listado filtrado.

## 3. Criterios de aceptación
- [ ] CA1 — Muestra NC abiertas: total, por estado y agrupadas por código de causa. Los números coinciden con el listado de NC filtrado por los mismos criterios.
- [ ] CA2 — Muestra AC que vencen en los próximos 7 y 30 días (dos contadores separados), calculados sobre la fecha límite de la AC.
- [ ] CA3 — Muestra documentos vencidos o con revisión en los próximos 30 días, e instrumentos con calibración vencida o por vencer en 30 días.
- [ ] CA4 — Muestra ensayos pendientes y auditorías internas y externas planificadas.
- [ ] CA5 — Muestra el grado de avance general de la certificación IEC 62052-31 como porcentaje, con la fórmula documentada en el código.
- [ ] CA6 — Cada indicador enlaza al listado filtrado correspondiente.
- [ ] CA7 — Solo muestra datos de la empresa del usuario: un usuario de la empresa B no ve números de la empresa A (test kernel).
- [ ] CA8 — Visible para Dirección, Responsable de Calidad y Administrador; un Operador recibe 403.

## 4. Stakeholders
Valida: Responsable de Calidad de HiQual. Usan: Dirección y Responsable de Calidad.

## Notas para la fábrica
- Lógica en `hiqual_core` / `hiqual_app`; presentación en el theme `hiqual_portal`.
- Ticket hermano de frontend: HIQ-99 ([FE] US-064). Este ticket entrega los datos y el endpoint o render; el diseño visual final es del [FE].
- Fuera de alcance: indicadores avanzados (US-067, COULD) y exportación (US-066).
