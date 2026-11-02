# Tareas programadas — guía de creación

Cinco tareas programadas por proyecto: tres controles de tablero y, si la fábrica usa la opción A, dos runners. Los horarios no se eligen a mano: salen de las variables de sprint en `factory/config.md`.

| Job | Prompt | Modelo | Para qué |
|---|---|---|---|
| Control diario | `daily-check.md` | Haiku | Detectar reglas de tablero rotas antes de la daily. |
| Review de mitad de sprint | `weekly-review.md` | Sonnet | Consejo de scrum master mientras todavía hay tiempo de corregir. |
| Evaluación de cierre | `sprint-review.md` | Opus | Avance contra plan y requisitos, pronóstico, recorte. |
| Fábrica — implementar | `factory-implement.md` | Sonnet | Toma un ticket `factory:go` (o corrige un PR con `factory:changes`) y abre o actualiza el PR. |
| Fábrica — revisar | `factory-review.md` | Opus | Revisa un PR con `factory:review` en una sesión nueva y deja el veredicto. |

Los runners no dependen del largo del sprint: corren cada 2 h en horario laboral, desfasados 1 h para que el review encuentre el PR ya abierto. Una ronda completa (implementar → revisar → corregir → revisar) tarda unas 4 h. Necesitan el repo del proyecto conectado con permiso de push y el repo dark-factory con lectura.

## 1. Variables de sprint (en `config.md`)

| Variable | Qué es | Ejemplo |
|---|---|---|
| `sprint.duracion_semanas` | **N**, largo del sprint en semanas | 2 |
| `sprint.dia_inicio` | Día de la semana en que arranca cada sprint | lunes |
| `sprint.fecha_referencia` | Fecha de inicio de **cualquier** sprint real (ancla para contar ciclos) | 2026-10-26 |
| `sprint.dias_habiles` | Días hábiles por sprint = N × 5 | 10 |
| `equipo.zona_horaria` | Zona IANA | Europe/Amsterdam |
| `equipo.hora_daily` | Hora de la daily | 09:30 |
| `reglas.dias_estancado` | Días hábiles sin cambio para considerar un ticket estancado. Regla: `max(2, N + 1)` | 3 |

## 2. Calendario derivado

Notación: `D` = `sprint.dia_inicio` (día 1 del sprint). `D+k` = k días hábiles después.

| Job | Cuándo corre | Regla |
|---|---|---|
| Control diario | Todos los días hábiles, 20 min antes de `equipo.hora_daily` | Independiente de N. |
| Review de mitad de sprint | Una vez por semana, en el día hábil `D+2` (miércoles si D = lunes) | Con N = 1 **no se crea**: el sprint es tan corto que la evaluación de cierre alcanza. Con N ≥ 2 corre todas las semanas y el reporte dice en qué semana del sprint está ("semana 1 de 2"). |
| Evaluación de cierre | Una vez por semana, día `D` por la mañana | El cron no sabe contar "cada N semanas", así que corre semanal y el prompt decide: sale sin reporte si no cerró un sprint en los últimos 7 días. Ver paso 0 de `sprint-review.md`. |

### Tabla rápida por largo de sprint (D = lunes, daily 09:30)

| N | Control diario | Review de mitad | Evaluación de cierre | Corridas reales de Opus por mes |
|---|---|---|---|---|
| 1 semana | L–V 09:10 | — (no se crea) | lunes 08:10, todas las semanas | ~4 |
| 2 semanas | L–V 09:10 | miércoles 10:40, semanal | lunes 08:10, reporta 1 de cada 2 | ~2 (+2 salidas vacías) |
| 3 semanas | L–V 09:10 | miércoles 10:40, semanal | lunes 08:10, reporta 1 de cada 3 | ~1,3 |
| 4 semanas | L–V 09:10 | miércoles 10:40, semanal | lunes 08:10, reporta 1 de cada 4 | 1 |

Las salidas vacías cuestan poco: el prompt corta en el paso 0.

### Cron (zona horaria del equipo)

| Job | Cron |
|---|---|
| Control diario | `CRON_TZ={{equipo.zona_horaria}} {mm} {hh} * * 1-5`, con `{hh}:{mm}` = `equipo.hora_daily` − 20 min |
| Review de mitad | `CRON_TZ={{equipo.zona_horaria}} 40 10 * * {D+2}` (lunes=1 … viernes=5) |
| Evaluación de cierre | `CRON_TZ={{equipo.zona_horaria}} 10 8 * * {D}` |
| Fábrica — implementar | `CRON_TZ={{equipo.zona_horaria}} 12 8-18/2 * * 1-5` |
| Fábrica — revisar | `CRON_TZ={{equipo.zona_horaria}} 47 9-19/2 * * 1-5` |

Los minutos no caen en :00 ni en :30 a propósito: a esas horas se acumulan las tareas programadas y pueden demorarse.

## 3. Crear las tareas

### Opción rápida: skill de setup

En Claude Code, en el repo del proyecto y con el plugin instalado: `/dark-factory:factory-setup`. Calcula el calendario, genera los prompts y crea las tareas después de mostrarte la tabla.

### Opción rápida sin plugin: pedírselo a Claude

En una sesión con acceso al repo y a Jira, pegá:

> Creá las tareas programadas de la fábrica para este proyecto. Leé `factory/config.md` de este repo y, del repo https://github.com/EnmanuelVarelaPividori/dark-factory, `plugins/dark-factory/jobs/README.md`. Para cada job de `plugins/dark-factory/jobs/` (salvo el de mitad de sprint si `sprint.duracion_semanas` = 1, y los dos runners si `fabrica.disparo` no es A): reemplazá todos los `{{...}}` del prompt con los valores del config, calculá el cron con la tabla de la sección 2, y creá la tarea con nombre `[{{jira.proyecto}}] <job>`, conector Atlassian (y el repo de GitHub para los runners) y notificaciones push + email. Antes de crear, mostrame una tabla con nombre, cron, hora local y modelo sugerido, y el prompt final de cada una. Después de crearlas, ejecutá cada una una vez para probarla.

### A mano

1. Abrí **Tareas programadas** en Claude y creá una nueva.
2. **Nombre:** `[CLAVE] Control diario`, `[CLAVE] Review de mitad de sprint`, `[CLAVE] Evaluación de cierre`.
3. **Prompt:** copiá la sección "Prompt" del archivo del job, con todos los `{{...}}` reemplazados. Verificá que no quede ninguno.
4. **Horario:** según la sección 2.
5. **Conectores:** Atlassian. La evaluación de cierre también necesita leer los docs: o el repo conectado, o los docs del Project.
6. **Modelo:** el de la tabla de arriba, en la configuración de la tarea.
7. **Notificaciones:** push + email.
8. **Aprobación:** automática si la organización lo permite. Los tres jobs solo leen, así que no hay acciones que aprobar; sin aprobación automática, una corrida puede quedar esperando sin nadie que la apruebe.
9. **Prueba:** ejecutá cada tarea una vez a mano y revisá que el reporte llegue al teléfono y al email con números correctos.

## 4. Mantenimiento

| Cuándo | Qué actualizar |
|---|---|
| Cambia el largo del sprint o el día de inicio | `sprint.*` en config y el cron de las tres tareas. Crear o borrar la de mitad de sprint según N. |
| Después de cada evaluación de cierre | `sprint.velocidad_por_persona` con la velocidad real medida (la usa el control diario para la capacidad). |
| Cambia el equipo | `equipo.personas`. |
| Se termina el proyecto | Pausar las tres tareas. |
