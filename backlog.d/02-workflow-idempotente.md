# update-readme.yml no es idempotente: dos commits por corrida aunque no cambie nada
> Contexto: auditoría de la migración a la constitución v1 (2026-10-09). Cada corrida (1) deja un commit de
> waka-readme-stats que reescribe la sección generada, incluida la hora en «Last Updated» (`SHOW_UPDATED_DATE`), y
> `assets/bar_graph.png` — la action commitea siempre, haya cambios o no; y (2) un segundo commit que borra la línea
> «Almacenamiento de GitHub utilizado» que el primero vuelve a insertar. El historial lo muestra: 94 commits de
> métricas y 92 de storage de `readme-bot` (92 pares; los 2 sueltos son del 2026-07-06, antes del paso de storage).
> La regla local exige que dos ejecuciones con el mismo input no generen diff (TEST-14); waiver con fecha en
> `AGENTS.md`. Apagar flags no alcanza: hace falta cambiar cómo se commitea (generar sin commitear y commitear una
> sola vez si hay diff) o fijar un fork de la action.
> **Scope:** que la sección generada dependa sólo de los datos (sin la hora de la corrida) y que la línea de storage
> no llegue a commitearse (un solo commit por corrida, y ninguno si los datos no cambiaron).
> **Done:** dos `workflow_dispatch` seguidos: el primero deja a lo sumo un commit y el segundo ninguno; el README
> conserva las secciones actuales; se quita el `Waiver TEST-14` del bloque local.
