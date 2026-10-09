# update-readme.yml corre código de terceros sin fijar, con GH_TOKEN
> Contexto: auditoría de la migración a la constitución v1 (2026-10-09). El workflow usa `actions/checkout@v4` (tag
> movible) y `anmol098/waka-readme-stats@master`, cuyo `action.yml` corre la imagen
> `docker://wakareadmestats/waka-readme-stats:master`: cada corrida diaria ejecuta lo que haya en esa rama/tag, con
> `permissions: contents: write` y el secreto `GH_TOKEN`. Fijar sólo el SHA de la action no alcanza: la imagen sigue
> en `:master`. Viola SEC-5 (dependencias desplegables fijadas por digest); waiver con fecha en `AGENTS.md`.
> **Scope:** fijar `actions/checkout` por SHA de commit y la imagen de waka-readme-stats por digest (`docker://…@sha256:…`,
> o un fork/copia fijada), sin cambiar la salida; con `docker://` no se aplican los defaults del `action.yml` (p. ej.
> `PUSH_TOKEN` = `github.token`, `SHOW_AI_CODE_TIME`/`SHOW_AI_CODING` = `"True"`): hay que declararlos explícitos.
> Documentar cómo se bumpea.
> **Done:** `actionlint` limpio; ninguna referencia `@master`/`:master`/tag movible en el workflow; un
> `workflow_dispatch` tras mergear termina verde y el README generado conserva las mismas secciones; se quita el
> `Waiver SEC-5` del bloque local.
