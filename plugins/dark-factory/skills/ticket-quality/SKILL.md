---
name: ticket-quality
description: Crear o refinar tickets de Jira con calidad Dark Factory: busca duplicados, usa la plantilla de 4 bloques, pregunta lo que falta, aplica el Definition of Ready y muestra el borrador antes de crear. Usar con "creá un ticket", "cargá esto en el backlog", "pasá esta story a Jira", "refiná este ticket", o al cargar varios tickets juntos.
---

# Ticket quality

Convierte una necesidad en un ticket que la fábrica puede ejecutar sin preguntar. El objetivo no es crear tickets: es que cada ticket del backlog represente trabajo real, trazable y listo.

## 0. Cargar el contexto

1. Buscá la configuración del proyecto: `factory/config.md` en el repo, o el doc de configuración del Project de claude.ai. Si no existe, pedí los datos mínimos (clave Jira, cloudId, ruta de docs) y seguí.
2. Leé la story del backlog y los requisitos (`R-x.y`) relacionados con el pedido. Si el pedido no aparece en los docs, decilo: puede ser alcance nuevo.

## 1. Clasificar el pedido

| Si el pedido es… | Hacé esto |
|---|---|
| Trabajo nuevo, no hecho todavía | Seguí con el paso 2. |
| Registro de algo ya hecho ("arreglé X", "hice Y") | No crees una story. Buscá el ticket al que pertenece y proponé un comentario o subtarea ahí. Si no existe ninguno, avisá que el trabajo se hizo sin ticket y proponé uno que cumpla el DoR, marcado como hecho. |
| Bug | Plantilla de bug: pasos para reproducir, esperado vs. real, entorno, ticket o PR de origen si se conoce. |
| Varias cosas mezcladas | Separalas en tickets distintos antes de seguir. |

## 2. Buscar duplicados

Antes de redactar, buscá en Jira con JQL acotado (pocos campos, máximo 25 resultados):

- `project = {clave} AND text ~ "{2–3 palabras clave}" AND statusCategory != Done`
- La misma búsqueda por el ID de story (`US-xxx`) y por el requisito (`R-x.y`).

Si hay coincidencias, mostralas y preguntá si es duplicado, extensión del existente o algo distinto. No crees hasta tener respuesta.

## 3. Redactar con la plantilla

Usá la plantilla de ticket (`templates/ticket.md` del repo dark-factory): metadatos, Historia, Descripción con ejemplo, Criterios de aceptación y Stakeholders. En español salvo que el proyecto diga otra cosa.

- Cada criterio de aceptación tiene que poder verificarse mirando el sistema o un test. Reescribí los vagos ("funciona bien", "es rápido").
- Cada criterio define una sola condición.
- Trazá a requisitos `R-x.y`. Si no hay requisito, escribí por qué existe el ticket.
- Clasificá: núcleo, configuración o específico de cliente. Si un pedido de cliente empuja lógica específica al núcleo, señalalo y proponé resolverlo por configuración.

## 4. Preguntar lo que falta

Si falta algo que no podés inferir de los docs, preguntá, en un solo mensaje y con un máximo de 3 preguntas. Ofrecé tu mejor propuesta para cada una, así la respuesta puede ser "sí". Ejemplos de lo que vale preguntar: rol afectado, dato de ejemplo real, criterio de éxito medible, dependencia dudosa.

No preguntes lo que ya está en los docs.

## 5. Gate: Definition of Ready

Revisá el borrador contra esta lista y mostrá el resultado:

- [ ] Historia COMO / QUIERO / PARA
- [ ] Ejemplo concreto
- [ ] Criterios verificables
- [ ] Requisito `R-x.y` o justificación
- [ ] Épica y dependencias
- [ ] ≤5 SP. Si es 8 o más, proponé la división antes de seguir.
- [ ] Tipo (núcleo / configuración / cliente)
- [ ] Sin duplicados

Si todo está OK, el ticket lleva la etiqueta `factory:ready`. Si falta algo, se crea sin la etiqueta y con un comentario que diga qué falta.

## 6. Capacidad y destino

- Por defecto, el ticket va al **backlog**, no al sprint activo.
- Si piden meterlo en el sprint activo: sumá los SP actuales del sprint y compará con la capacidad de `config.md`. Si la supera, decilo y proponé qué sacar. Agregar al sprint en curso es un cambio de alcance: dejalo dicho en el comentario.

## 7. Mostrar y crear

1. Mostrá el borrador completo (o una tabla resumen si son varios: título, SP, prioridad, requisitos, estado del DoR).
2. Si son más de `reglas.lote_confirmacion` tickets, o si hay cualquier duda, esperá confirmación explícita.
3. Antes de crear, consultá los campos del tipo de issue (`getJiraIssueTypeMetaWithFields`).
4. Creá con: tipo, `parent` = épica, prioridad mapeada (MUST → High, SHOULD → Medium, COULD → Low), SP, etiquetas y descripción en markdown.
5. Respondé con la clave creada y el estado del DoR. No resumas de nuevo el contenido.

## No hacer

- Crear tickets para registrar trabajo ya hecho.
- Crear en lote sin mostrar el borrador.
- Poner `factory:go`: eso lo decide una persona.
- Inventar criterios que los docs no respaldan sin marcarlos como propuesta.
