---
name: factory-setup
description: Instalar, actualizar o verificar Dark Factory en el repo de un proyecto. Copia la plantilla genérica a factory/, crea factory/proyecto/ con el config, conecta CLAUDE.md, AGENTS.md y .claude/settings.json, arma las tareas programadas y corre la checklist. Usar con "instalá la fábrica", "configurá dark factory", "actualizá la fábrica" o "¿está bien configurada la fábrica?".
---

# Factory setup

La plantilla está en el plugin: `${CLAUDE_PLUGIN_ROOT}/template/`. Tiene la misma forma que la raíz de un repo:

```
template/
├── CLAUDE.md, AGENTS.md          bloques genéricos (se agregan, no se pisan)
├── .claude/settings.json         activa el plugin (se combina, no se pisa)
└── factory/                      genérico, salvo factory/proyecto/
    └── proyecto/                 plantilla del proyecto: se crea una vez y nunca se sobrescribe
```

La estructura esperada y las reglas para agentes están en `template/factory/ESTRUCTURA.md`.

## 0. Elegir el modo

| Estado del repo | Modo |
|---|---|
| No existe `factory/` | **Instalar** (pasos 1–6) |
| `factory/VERSION` distinta de `${CLAUDE_PLUGIN_ROOT}/template/factory/VERSION` | **Actualizar** (paso 2 y después 6) |
| Misma versión | **Verificar** (paso 6) y ofrecer completar lo que falte |

## 1. Detectar (sin preguntar)

| Clave del config | De dónde |
|---|---|
| `repo.url` | `git remote get-url origin` |
| `repo.rama_base` | Destino de los últimos PRs mergeados, u `origin/HEAD` |
| `repo.nombre_rama`, `repo.commit` | Patrones en `git branch -r` y `git log --oneline -30` |
| `repo.tests` | Workflows de CI, `README`, `package.json`, `composer.json`, `Makefile` |
| `repo.stack` | Manifiestos, entorno local (`.ddev/`, `docker-compose`), carpetas de código propio |
| `jira.*` | Si hay conector de Atlassian: sitio, cloudId, proyectos, IDs de tipos, campos de story points y sprint (`getJiraIssueTypeMetaWithFields`) |
| Docs existentes | Docs de planificación en otra carpeta del repo (ej. `docs/`) que haya que mover a `factory/proyecto/docs/` |

## 2. Copiar la plantilla

1. Copiá todo `${CLAUDE_PLUGIN_ROOT}/template/factory/` a `factory/`, **excepto `factory/proyecto/`** si ya existe. En modo actualizar, los archivos genéricos se reemplazan; mostrá qué cambió.
2. Si `factory/proyecto/` no existe: copiá `template/factory/proyecto/` (config vacío, `estado.md` y `docs/README.md`). Si existe pero le falta `estado.md` (proyectos instalados antes de 0.5.0), copiá solo ese archivo.
3. Si encontraste docs de planificación en otra carpeta: proponé moverlos a `factory/proyecto/docs/` con los nombres fijos (`00-analisis-problema.md`, `01-analisis-requerimientos.md`, `02-product-backlog.md`, `03-planificacion-sprints.md`). No muevas sin confirmación.
4. `CLAUDE.md` y `AGENTS.md`: si no existen, copiá los de la plantilla. Si existen y no tienen el bloque de Dark Factory, agregalo al final. Nunca borres contenido.
5. `.claude/settings.json`: si no existe, copiá la plantilla. Si existe, combiná `extraKnownMarketplaces` y `enabledPlugins` sin tocar el resto.

## 3. Completar el config

Llená `factory/proyecto/config.md` con lo detectado. Preguntá el resto en **un solo mensaje**, con tu mejor propuesta en cada punto para que la respuesta pueda ser "sí":

- Proyecto: nombre, objetivo en una frase, fecha objetivo, y el nombre del Project de claude.ai con la investigación (`claude.project`).
- Jira: clave del proyecto si no quedó clara.
- Equipo: cantidad y cuentas de Jira.
- Sprint: duración en semanas, día de inicio, fecha de inicio de un sprint real, hora de la daily, zona horaria.
- Velocidad por persona (medida o estimada, marcada como tal).
- Disparo (A o B), canales e idioma de reportes.

Derivados: `sprint.dias_habiles` = N × 5; `reglas.dias_estancado` = max(2, N + 1). Lo que no se sepa queda `COMPLETAR`. No inventes valores.

## 4. Etiquetas

- GitHub, si `gh` está autenticado: ofrecé crear `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`, mostrando los comandos antes.
- Jira: las etiquetas se crean al usarlas. Listá las del sistema (`factory/reglas.md`).

## 5. Tareas programadas

Con `factory/jobs/README.md`:

1. Calculá el calendario desde el config. Con N = 1 no va la de mitad de sprint; si `fabrica.disparo` ≠ A, no van los runners.
2. Cada tarea usa el prompt corto del README (repo + rama + job). No pegues valores del config en el prompt: el job los lee al correr.
3. Mostrá una tabla: nombre, cron, hora local, modelo sugerido, conectores.
4. Los dos runners van **dentro del Project de claude.ai** (`claude.project`) para que lean los docs que citan los tickets. Si la sesión no puede crearlas ahí, decí cómo moverlas al Project.
5. Si la sesión puede crear tareas programadas: crealas después de que el usuario confirme, con notificaciones según `reportes.canales`, y ejecutá cada una una vez para probarla. Si no puede: dejá la tabla y el prompt listos para pegar.
6. El modelo se elige en la configuración de cada tarea: recordalo.

## 6. Verificar

Reportá ✅ / ❌ / ⏳ (requiere a una persona):

| Ítem | Cómo |
|---|---|
| Plantilla instalada y al día | Todos los archivos genéricos existen; `factory/VERSION` = versión del plugin |
| Config completo | Filas `COMPLETAR` en `factory/proyecto/config.md` |
| Docs | Existen los 4 docs con los nombres fijos; los requisitos tienen IDs `R-x.y`; las stories tienen `Requisitos` |
| `CLAUDE.md` | Importa `@factory/ESTRUCTURA.md`, `@factory/reglas.md`, `@factory/proyecto/config.md` |
| `AGENTS.md` | Apunta a `factory/ESTRUCTURA.md` |
| Plugin activado | `.claude/settings.json` con `dark-factory@dark-factory: true` |
| Jira accesible | Una JQL simple sobre `jira.proyecto` responde |
| Etiquetas GitHub | `gh label list` |
| Tareas programadas | Existen (si se pueden listar) |
| Project de claude.ai | ⏳ `factory/proyecto/config.md` y los docs subidos con las mismas rutas; investigación en `investigacion/`, `decisiones/`, `diagramas/` |
| Runners dentro del Project | ⏳ Las tareas implementar y revisar pertenecen al Project |
| `ticket-quality` en claude.ai | ⏳ Guardada como skill de la cuenta |
| Rama base protegida | ⏳ Merge solo con review humano |
| Smoke test | ⏳ Ver bloque 6 de `factory/CHECKLIST.md` |

Terminá con la lista de pendientes para una persona y actualizá los checks en `factory/proyecto/estado.md`. Si el usuario prefiere que le preguntes todo paso a paso, usá `dark-factory:factory-kickoff`.

## No hacer

- Sobrescribir `factory/proyecto/`, ni borrar contenido de `CLAUDE.md`, `AGENTS.md` o `settings.json`.
- Editar archivos genéricos con valores del proyecto.
- Hacer commit o push sin confirmación. Si la rama actual es la rama base, proponé una rama nueva.
- Crear tareas programadas sin mostrar antes la tabla.
- Inventar valores de Jira, equipo o velocidad.
