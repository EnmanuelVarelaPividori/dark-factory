# Mantener Dark Factory

Este repo es el **sistema**, no un proyecto. Contiene el plugin de Claude Code y la plantilla que se copia a cada repo de proyecto.

## Reglas

1. **Nada específico de un proyecto.** Ni nombres de clientes, ni claves de Jira, ni URLs de repos de proyecto, ni stacks concretos. Los ejemplos usan valores neutros (`ABC`, `miempresa.atlassian.net`).
2. **Los archivos genéricos referencian lo propio del proyecto solo por rutas fijas:** `factory/proyecto/config.md` (por clave, ej. `repo.rama_base`) y `factory/proyecto/docs/0X-*.md`. Nunca con valores pegados ni placeholders `{{...}}`.
3. **`template/` tiene la misma forma que la raíz de un repo de proyecto.** Lo que agregues ahí aparece en todos los proyectos al actualizar.
4. **`template/factory/proyecto/` es la plantilla inicial del proyecto:** se copia una sola vez y el setup nunca la sobrescribe. Cambios ahí no llegan a proyectos existentes; si hace falta migrar, documentalo en la tabla *Migraciones* de `factory-kickoff`.
5. **Rutas y nombres fijos.** Renombrar o mover un archivo de `template/` rompe referencias en skills, jobs y en `ESTRUCTURA.md`. Si es necesario, actualizá todas las referencias y la migración en `factory-kickoff`.
6. **Versionado.** Cualquier cambio en `template/` o `skills/` sube la versión en `plugins/dark-factory/.claude-plugin/plugin.json` y en `plugins/dark-factory/template/factory/VERSION` (la misma).
7. **Validar antes de commitear:** `claude plugin validate .` y `claude plugin validate plugins/dark-factory`.

## Dónde está cada cosa

| Quiero cambiar… | Archivo |
|---|---|
| Reglas (DoR, DoD, WIP, etiquetas, review) | `plugins/dark-factory/template/factory/reglas.md` |
| Estructura esperada / instrucciones para agentes | `plugins/dark-factory/template/factory/ESTRUCTURA.md` |
| Prompts de las tareas programadas | `plugins/dark-factory/template/factory/jobs/` |
| Claves del config | `plugins/dark-factory/template/factory/proyecto/config.md` + las skills y jobs que las usan |
| Comportamiento de una skill | `plugins/dark-factory/skills/<skill>/SKILL.md` |
| Cómo se instala, actualiza o verifica; preguntas de la entrevista; migraciones | `plugins/dark-factory/skills/factory-kickoff/SKILL.md` + `template/factory/proyecto/estado.md` |
