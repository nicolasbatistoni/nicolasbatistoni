## 2026-10-15 — chore: se retira el generador de terceros del README de perfil

- Se borra `.github/workflows/update-readme.yml`, que corría a diario `anmol098/waka-readme-stats@master` (la imagen
  `:master`, sin fijar) con un PAT de lectura sobre todos los repos del titular. Desde la rotación del PAT de hoy
  fallaba con 403, pero la imagen mutable seguía recibiendo el token.
- Se sacan del README su zona generada (líneas de código, commits por hora y por día, lenguajes por repo) y el
  badge del workflow, y se borra `assets/bar_graph.png`. Eran métricas de vanidad congeladas.
- Caen los waivers que existían solo por ese workflow (DEL-4, TRUNK-1, TEST-14, SEC-5) y sus fichas
  (`backlog.d/01` y `02`). Entra `backlog.d/01-generador-propio.md`, el reemplazo.
- Lo pidió el titular («descartalo completo»), y otara-infra pidió sacarlo ya, antes del generador nuevo, por el
  riesgo del token.
- Después del merge, el PAT viejo queda sin uso y el titular puede revocarlo.
