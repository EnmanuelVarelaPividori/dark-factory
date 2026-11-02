# Job: control diario

Modelo sugerido: Haiku. Solo lectura: no modifiques tickets.

## Contexto

1. Leé `factory/proyecto/config.md`. De ahí salen todos los valores: Jira (`jira.*`), equipo, sprint y reglas.
2. Capacidad del sprint = `equipo.cantidad` × `sprint.velocidad_por_persona` SP.

## Datos

Consultá en Jira (`jira.cloud_id`, proyecto `jira.proyecto`) el sprint activo con JQL acotada: key, summary, status, assignee, labels, `jira.campo_sp`, `jira.campo_sprint`, created, updated; máximo 100 por consulta.

Calculá el día hábil del sprint `d` de `sprint.dias_habiles`, desde la fecha de inicio del sprint activo. Si Jira no tiene sprints, usá `sprint.fecha_referencia` y `sprint.duracion_semanas`.

## Reglas a revisar

1. **WIP:** personas con más de `reglas.wip_por_persona` tickets In Progress.
2. **Capacidad:** SP del sprint vs. capacidad; exceso en SP y %.
3. **Alcance agregado:** tickets que entraron al sprint en las últimas 24 h; marcá si son más de `reglas.lote_confirmacion` de una misma persona.
4. **No Ready:** tickets del sprint sin `factory:ready` o con más de `reglas.sp_max_ticket` SP.
5. **Sin dueño:** In Progress sin asignar.
6. **Estancados:** In Progress sin cambios hace más de `reglas.dias_estancado` días hábiles.
7. **Fábrica:** tickets con `factory:blocked` o `factory:needs-human`.
8. **Ritmo:** si `d` > `sprint.dias_habiles` / 2 y menos del 30 % de los SP está Done.

## Salida

En `reportes.idioma`, máximo 15 líneas:

```
Control diario <jira.proyecto> — <fecha> · día <d>/<sprint.dias_habiles>
Estado: OK | ATENCIÓN (n reglas rotas)
- <regla>: <dato concreto> (<claves>)
```

Si no hay nada roto, una línea: "Control diario <jira.proyecto> — día <d>/<sprint.dias_habiles>, sin novedades". Sin consejos: eso es del review de mitad de sprint.
