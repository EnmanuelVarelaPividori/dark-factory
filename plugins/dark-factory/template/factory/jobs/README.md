# Tareas programadas

Cinco tareas por proyecto. Los archivos de esta carpeta son genéricos: cada uno es la instrucción completa de un job y lee lo propio del proyecto desde `factory/proyecto/` cuando corre.

| Job | Archivo | Modelo | Va si… |
|---|---|---|---|
| Control diario | `daily-check.md` | Haiku | Siempre |
| Review de mitad de sprint | `weekly-review.md` | Sonnet | `sprint.duracion_semanas` ≥ 2 |
| Evaluación de cierre | `sprint-review.md` | Opus | Siempre |
| Fábrica — implementar | `factory-implement.md` | Sonnet | `fabrica.disparo` = A |
| Fábrica — revisar | `factory-review.md` | Opus | `fabrica.disparo` = A |

## 1. El prompt de cada tarea

Igual para todas; solo cambian el repo, la rama y el job:

```
Repo del proyecto: <repo.url> (rama <repo.rama_base>)
Cloná el repo y ejecutá el job definido en factory/jobs/<job>.md. Seguí ese archivo al pie de la letra.
```

Nombre sugerido de la tarea: `[<jira.proyecto>] <job>`.

## 2. Calendario (sale del config)

`D` = `sprint.dia_inicio` (lunes = 1 … viernes = 5). Hora local de `equipo.zona_horaria`.

| Job | Cuándo | Cron |
|---|---|---|
| Control diario | Días hábiles, 20 min antes de `equipo.hora_daily` | `CRON_TZ=<zona> <mm> <hh> * * 1-5` |
| Review de mitad de sprint | Semanal, día hábil `D+2` | `CRON_TZ=<zona> 40 10 * * <D+2>` |
| Evaluación de cierre | Semanal, día `D` a la mañana. El job decide si cerró un sprint y, si no, termina en una línea. | `CRON_TZ=<zona> 10 8 * * <D>` |
| Fábrica — implementar | Cada 2 h en horario laboral | `CRON_TZ=<zona> 12 8-18/2 * * 1-5` |
| Fábrica — revisar | Cada 2 h, 1 h después del implementador | `CRON_TZ=<zona> 47 9-19/2 * * 1-5` |

Los minutos evitan :00 y :30 a propósito: a esas horas se acumulan las tareas programadas y pueden demorarse. La evaluación de cierre corre semanal porque el cron no sabe contar "cada N semanas"; con N = 2, una de cada dos corridas termina en una línea y cuesta muy poco.

### Por largo de sprint (ejemplo: D = lunes, daily 09:30)

| N | Control diario | Mitad de sprint | Cierre | Corridas reales de Opus por mes |
|---|---|---|---|---|
| 1 semana | L–V 09:10 | — | lunes 08:10 | ~4 |
| 2 semanas | L–V 09:10 | miércoles 10:40 | lunes 08:10 | ~2 |
| 3 semanas | L–V 09:10 | miércoles 10:40 | lunes 08:10 | ~1,3 |
| 4 semanas | L–V 09:10 | miércoles 10:40 | lunes 08:10 | 1 |

## 3. Crear las tareas

Con el plugin: `/dark-factory:factory-setup` calcula el calendario, muestra la tabla y crea las tareas cuando confirmás.

A mano, para cada job que corresponda:

1. Nueva tarea programada con el nombre y el prompt de la sección 1.
2. Horario según la sección 2.
3. Para los dos runners: crear la tarea **dentro del Project de claude.ai** del proyecto, para que lean los docs que los tickets citan en Contexto. Conectores: Atlassian. Repo del proyecto con lectura (controles) o con push (runners). Repo dark-factory con lectura si es privado.
4. Modelo: el de la tabla de arriba, en la configuración de la tarea.
5. Notificaciones según `reportes.canales`.
6. Aprobación automática en los tres controles (solo leen).
7. Ejecutarla una vez a mano y revisar el resultado.

## 4. Mantenimiento

| Cuando cambia… | Hacer |
|---|---|
| Un umbral, el equipo o la velocidad | Editar `factory/proyecto/config.md`. Las tareas lo leen en la próxima corrida. |
| El largo del sprint, el día de inicio o la hora de la daily | Editar el config y actualizar el horario de las tareas (crear o borrar la de mitad de sprint según N). |
| El proyecto termina | Pausar las tareas. |
