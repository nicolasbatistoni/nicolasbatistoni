<!-- otara:canonical:begin v=1 -->
# AGENTS.md — Constitución de ingeniería

> Reglas ejecutables para todo agente y contribuidor de la flota: **override** del comportamiento por defecto.
> Agnósticas al stack ("test", "build", "linter", "runtime" = el equivalente del proyecto). Una línea por regla,
> con ID estable; el **porqué, los ejemplos y las recetas** viven en `docs/engineering/{evidence,testing,architecture,delivery}.md`
> y en `docs/*.md` del repo canónico de la flota (lo nombra el bloque local). **C** = rige siempre. **K** = rige si se da su condición.
> Este bloque es idéntico en toda la flota; lo propio del repo va en el bloque local, al final.

## 0. Gobernanza de este archivo (GOV) — `architecture.md`

- **GOV-1** · C: El bloque canónico (marcadores `otara:canonical`) se copia byte a byte del repo canónico; se cambia allá, por PR, nunca en el destino.
- **GOV-2** · C: El bloque canónico contiene políticas invariantes, nunca hechos mutables del repo (paths, runners, adaptadores, nombres de repos): esos van al bloque local o se generan.
- **GOV-3** · C: El bloque local (marcadores `otara:local`) concreta las reglas al stack y al dominio: puede reforzar; sólo exceptúa con `Waiver <ID>: <razón> (revisar: AAAA-MM-DD)`; nunca debilita en silencio una regla C.
- **GOV-4** · C: AGENTS es jerárquico: cada nivel (raíz, app, motor) agrega sólo reglas de su alcance sin repetir al padre; al lado de cada AGENTS.md, un CLAUDE.md que es exactamente `@AGENTS.md`.
- **GOV-5** · C: El nivel más específico manda sólo en lo específico; nunca para relajar una disciplina de verificación.
- **GOV-6** · C: Tamaño: objetivo ≤ 30 KB, techo duro 40 KB (lo verifica el CI); se controla MOVIENDO detalle a `docs/`, nunca borrando reglas; si encoge sin migración documentada, se restaura lo perdido; toda PR que lo toca lo re-mide.
- **GOV-7** · C: Cada repo declara su perfil en `.otara/profile` (`MANAGED` | `MINIMAL` | `FORK` | `ARCHIVED`); no se infiere de si hay CI; sin perfil declarado el repo no es conforme (única excepción: un repo archivado en la plataforma, de sólo lectura, cuenta como `ARCHIVED`).
- **GOV-8** · K MANAGED: lleva AGENTS.md, ARCHITECTURE.md, PROGRESS.md, `specs/`, `backlog.d/`, `ideas.d/` y `changelog.d/`.
- **GOV-9** · K MINIMAL: lleva AGENTS.md y ARCHITECTURE.md; lo demás aparece cuando exista esa información.
- **GOV-10** · K FORK: no se toca la estructura upstream; si pasa a producto mantenido, se re-declara MANAGED. · K ARCHIVED: congelado; sólo se audita, salvo un fix de seguridad puntual y documentado (REL-6).
- **GOV-11** · C: Sin `.gitkeep` ni estructura vacía para fingir convención: cada carpeta (`backlog.d/`, `ideas.d/`, `changelog.d/`, `specs/`, `docs/kb/`) aparece con su primer archivo real.

## 1. Idioma (LANG) — `delivery.md`

- **LANG-1** · C: Todo lo que lee el usuario va en español: respuestas, progreso, títulos y cuerpos de PR, commits, notas.
- **LANG-2** · C: Quedan en inglés: identificadores y símbolos, comentarios y logs del código, archivos ya en inglés, nombres de rama (kebab-case), comandos/paths/hex/ranges/exit-codes.
- **LANG-3** · C: Código, specs, tests y UI usan consistentemente la nomenclatura del dominio (lenguaje ubicuo).

## 2. Norte y forma de trabajo (WORK) — `evidence.md`, `architecture.md`

- **WORK-1** · C: Desempate supremo: ante cualquier duda gana lo más correcto, riguroso y de mayor calidad, verificado contra realidad/datos/referencias, por encima de conveniencia o gusto; entre dos caminos, el que mejor sirve al norte.
- **WORK-2** · C: El norte (GOAL.md o equivalente: pregunta-filtro + loop mínimo) se lee antes de cada iteración; una feature sin causa real del dominio no entra; nada scripteado "para el usuario" si el sistema puede producirlo causalmente.
- **WORK-3** · C: Antes de modificar: leer instrucciones, spec y arquitectura relevantes e inspeccionar el árbol real (no el previsto); una contradicción entre docs fuente se corrige antes de implementar.
- **WORK-4** · C: ARCHITECTURE.md se lee junto al norte y todo cambio la respeta; desviarse exige modificar primero el doc, con la evidencia.
- **WORK-5** · C: Un solo agente escribe el código; un segundo modelo co-diseña lo no trivial (root-cause, arquitectura, approach, elección entre fixes) y co-confirma toda salida perceptible, con prompt neutral.
- **WORK-6** · C: Lo trivial (typo, rename, bump con criterio claro) no pide segundo modelo; si el segundo modelo no responde, no se bloquea: revisión propia, se continúa y el changelog declara la co-confirmación pendiente.
- **WORK-7** · C: Sin ceremonia pesada (brainstorm→spec→plan→N subagentes) para cambios chicos/medianos: diseño directo → rama + spec + tests + impl + PR + merge; spec/plan formal sólo con complejidad arquitectónica genuina y si el usuario lo pide.
- **WORK-8** · C: Bugs primero: un bug o regresión conocido (aunque sea pre-existente) se arregla antes de arrancar o mergear trabajo nuevo; "ya estaba roto" no es excusa; si no se puede ya, va al backlog con prioridad máxima.
- **WORK-9** · C: Stack según STACKS.md: tipo de software → default de su tabla maestra (con versiones de referencia) → recién ahí librería puntual; desviarse exige razón en el PR (por qué, qué, costo permanente).
- **WORK-10** · C: Ante dudas de implementación se consulta primero el proyecto hermano/referente que ya lo resolvió: se portan modelo, fórmulas y orden causal, no el código; las tareas importadas llevan su procedencia.
- **WORK-11** · C: Ante la duda, se verifica contra el código y los datos, no contra intuición o memoria: la memoria y las citas `file:line` envejecen; el repo es la fuente de verdad.

## 3. Trunk y PR (TRUNK) — `delivery.md`

- **TRUNK-1** · C: Todo cambio (hasta una línea) va por rama corta desde la principal actualizada + PR + squash merge; nunca commit/push directo a la principal ni editarla en local.
- **TRUNK-2** · C: "Corta" = minutos a pocas horas; cerca de un día sin mergear, se parte o se mergea un parcial; sin ramas largas paralelas (`develop`, `release/*`, `staging`, `qa`, `hotfix/*`).
- **TRUNK-3** · C: Una PR = una unidad lógica (no mezclar feature+refactor, bugfix+reformateo, migración+cambio visual), revisable en 10–20 min (idealmente < 300–500 líneas).
- **TRUNK-4** · C: Ramas por scope, no por actor ni fecha (`feat-…`, `fix-…`, `chore-…`, `docs-…`); commits en Conventional Commits.
- **TRUNK-5** · C: La principal es la próxima versión publicable (compila, tests verdes, arranca limpio): lo incompleto entra detrás de un flag apagado; los refactors grandes, por branch by abstraction.
- **TRUNK-6** · C: Mergea la máquina, no un humano esperando: automerge del CI exigiendo el sha testeado (opt-out declarado con motivo); donde está apagado, mergea el agente al pasar los gates; nunca queda una PR verde esperando al usuario.
- **TRUNK-7** · C: El usuario gatea un merge sólo si lo pide o si es high-risk (arquitectónico, schema/API-breaking, refactor grande).
- **TRUNK-8** · C: Automatizar el merge no baja el listón: changelog, bloques de diseño y no-regresión de la flota los hace el agente ANTES de abrir la PR.
- **TRUNK-9** · C: Ningún trabajo que deba persistir existe únicamente en una máquina: cada rama se pushea.
- **TRUNK-10** · C: Prohibido saltear hooks (`--no-verify` o equivalente), el force-push sobre la principal y `--no-edit` en un rebase.

## 4. Gates locales, CI y entrega (DEL) — `delivery.md`

- **DEL-1** · C: Los gates locales son la autoridad; el CI es una mejora, nunca el bloqueo: si el CI remoto no está o se traba, se corren los mismos gates en local (checkout limpio, arquitectura del destino, revisando antes los pipelines y pins pendientes para que no pisen el deploy) y se entrega.
- **DEL-2** · C: Tras clonar se instalan los hooks versionados (`core.hooksPath`): `pre-commit` (commit directo a la principal, archivos enormes, secrets obvios), `commit-msg` (Conventional Commits), `pre-push` (push directo y force-push que pisa lo no visto).
- **DEL-3** · K código: gate de calidad local fail-closed en los hooks: ningún cambio de código sin `Spec: ID` preexistente, sin test RED contra el código anterior y GREEN con el nuevo, sin el e2e de la unidad en verde ni con líneas que ningún test ejecute; el agente no cierra un turno con código sin su spec/test.
- **DEL-4** · C: El CI/CD vive una sola vez en el motor portable de la flota: cada repo declara sólo sus parámetros, el adaptador de CI se genera (un gate rompe si se edita a mano) y un único comando corre igual local o en CI.
- **DEL-5** · C: El motor y sus imágenes base van siempre en la última versión publicada, vía la PR rodante de bump; un pin viejo es deuda.
- **DEL-6** · C: Build-once: cada commit produce un artefacto inmutable identificado por su SHA; el mismo artefacto testeado se promueve por los entornos, nunca se rebuildea por entorno ni a mano.
- **DEL-7** · K stack que lo permita: preview/entorno efímero por PR, para revisar algo corriendo y no sólo el diff.
- **DEL-8** · C: CI con permiso mínimo por job; producción protegida (reviewers/OIDC/secrets por entorno); tags y releases los crea el pipeline, nunca la máquina del dev.
- **DEL-9** · C: Objetivo de tiempos: feedback de PR ~5–10 min, pipeline de la principal ~10–20 min; si se pasa, paralelizar o partir.
- **DEL-10** · K servicios: deploy por GitOps: el pipeline abre una PR al repo de entorno bumpeando el pin de versión y el cluster reconcilia (pull, sin credenciales en CI, sin pollers ni `:latest`); release ≠ deploy; rollback = revert del bump; editar el pin a mano es drift.
- **DEL-11** · K monorepo: path-filter por paquete; SemVer, tag prefijado `<pkg>-v*` y changelog por paquete; deps entre paquetes en una sola dirección; lock, build context y scanner propios por unidad desplegable.
- **DEL-12** · K servicio con usuarios: progressive delivery (internos → 1 % → … → 100 %) con métricas en cada paso; rollback: flag off > build anterior > revert; seguir métricas DORA + salud.
- **DEL-13** · K artefacto distribuido: el agente corre el build (script del proyecto) y confirma que el artefacto existe antes de declarar entregado; nunca lo delega ni pregunta "¿buildeo?".
- **DEL-14** · K producto distribuido: la instrumentación de test no viaja al artefacto productivo; el artefacto final lleva su propio smoke.

## 5. Versionado y políticas (REL) — `delivery.md`, `docs/versionado.md`

- **REL-1** · C: SemVer 2.0.0 por Conventional Commits (`fix`→PATCH, `feat`→MINOR, `!`/`BREAKING CHANGE`→MAJOR; el resto no corta); en `0.y.z` sube MINOR; versión publicada inmutable (release mala → nueva PATCH); hotfix = rama `fix-…` → PR → PATCH.
- **REL-2** · C: API pública = contrato observable (endpoints, URLs, datos públicos, env documentadas, schema persistido); deprecaciones vivas ≥ 1 MINOR antes de removerse en una MAJOR.
- **REL-3** · C: Toda release se ancla en un tag: servicio continuo → tag siempre, release si hay algo que comunicar; librería/CLI/SDK/artefacto consumible → tag + release + changelog siempre, con artefacto + checksum.
- **REL-4** · C: Datos persistidos backward-compatible (expand/contract): nunca romper un dato, save o schema existente sin migración.
- **REL-5** · C: Una policy se cambia modificando primero su doc con la evidencia, nunca violándola en silencio.
- **REL-6** · C: Estado del proyecto declarado en el README: activo (todos los gates) · mantenimiento (sólo fixes, con gates) · archivado (congelado, exento de gates activos, sólo fix de seguridad documentado) · experimento (corto: se promueve o se archiva); archivado/experimento no es referencia de stack; reactivar = cambiar el estado y cumplir ENTREGADO antes del próximo merge de feature.

## 6. Verificación y evidencia (VER) — `evidence.md`, `docs/metodo-cientifico.md`

- **VER-1** · C: TDD + e2e en TODO cambio de comportamiento observable, sin "cambio demasiado chico" (include, default, config, texto, una palabra), y perf si toca el hot path: filtros independientes, no alternativas.
- **VER-2** · C: "Es trivial", "ya andaba", "lo cubre otro test", "lo agarra el CI" o "lo probé a mano" no son razones; si tocás algo sin test, el test entra en esa PR.
- **VER-3** · C: Spec primero (SDD): causa del dominio, dado/cuando/entonces, criterios medibles y falsables, fuera de alcance; versionada con el cambio; si la implementación se desvía, cambia primero la spec.
- **VER-4** · K SDD: todo cambio conductual tiene criterios verificables previos; una tabla es el mínimo, no el máximo.
- **VER-5** · C: Método científico: hipótesis + predicción con umbral ANTES de medir; datos reales o derivados de la realidad, nunca un número "plausible"; root-cause = cadena causal demostrada (reproducción + aislamiento + control); si los datos refutan, se corrige hipótesis o código, nunca el test; el negativo se registra; reproducible (comando, seed, versión y fuente en el PR).
- **VER-6** · C: La fuente de verdad son los datos (logs, valores, asserts, métricas, validaciones del runtime), no la impresión; si chocan, ganan los datos.
- **VER-7** · C: Una proxy (CI verde, `/health` 200, un conteo) nunca sustituye la propiedad que se quiere verificar: se verifica el efecto, no la declaración.
- **VER-8** · C: La evidencia es acumulativa: GREEN no borra RED (un reintento exitoso no borra el fallo); lo no ejecutado se declara NO VERIFICADO, nunca "aprobado".
- **VER-9** · C: Al cerrar trabajo se declara qué cambió, qué se verificó y qué quedó sin verificar, distinguiendo "validé documentos" de "ejecuté el sistema".
- **VER-10** · C: Un gap declarado no es un permiso: WIP/deferred y verification gaps son para lo que todavía no se puede verificar; se declaran en el PR y entran al backlog; un PR sin verificación ni gap declarado no se mergea.
- **VER-11** · K perceptible: cobertura múltiple: varias muestras en estados y escalas opuestos y extremos, revisando TODAS antes de declarar que está bien.
- **VER-12** · K UI/feature: alcanzabilidad: nada se mergea sin su entrada real (binding/menú/ruta/interacción), salvo WIP con el gap declarado.
- **VER-13** · C: Infra de verificación durable: si falta un modo de verificar, se parametriza el mecanismo existente; un diagnóstico efímero sólo si es imposible parametrizar, y se borra al cumplir.
- **VER-14** · C: Verificación en capas: linter/estático + scanners/validadores del runtime + tests automatizados + revisión (humana o segundo modelo); a11y, seguridad y perf también por las cuatro.
- **VER-15** · K UI: accesibilidad 100 % automatizada y gate (una regresión en rutas principales bloquea); las auditorías de la plataforma (a11y, SEO, best-practices, perf) se sostienen en su puntaje máximo.

## 7. Tests (TEST) — `testing.md`

- **TEST-1** · C: Por aspecto: lógica pura → unit (TDD); comportamiento/integración → spec dado/cuando/entonces (e2e); perceptible → verificación de salida (captura/snapshot/golden + revisión); el e2e nunca se omite.
- **TEST-2** · C: Se usa la capa de test más baja que pruebe la conducta, aislando el IO externo; el e2e se reserva para el camino real.
- **TEST-3** · K conductual: un test usado como evidencia demuestra RED por la conducta objetivo antes de GREEN; se admite mutación controlada (mutar, ver fallar, revertir).
- **TEST-4** · K conductual: RED sólo cuenta si falla por la conducta ausente; fallas de discovery/import/infra no prueban nada.
- **TEST-5** · K repos con tests: el runner es fail-closed: cero tests, tests inesperados, runner ausente o manifiesto incompleto = fallo.
- **TEST-6** · K runners agregados/engines: GREEN se deriva del resumen real de tests ejecutados, no sólo del exit code.
- **TEST-7** · K specs con IDs: requisitos y tests se trazan por IDs estables, verificados automáticamente y nunca reciclados; requisito sin test o test sin requisito = defecto.
- **TEST-8** · K e2e: usa la vía real de entrada, incluye un control negativo (con la entrada deshabilitada falla) y falla ante errores no esperados en el log.
- **TEST-9** · C: Tests, fixtures y capturas nunca leen ni modifican estado real del usuario o de producción.
- **TEST-10** · K bug-fix: test de regresión que falla contra el código viejo y pasa con el fix, con before + after en el PR; sin él, el fix no está terminado.
- **TEST-11** · C: No se borra ni se saltea un test para pasar un gate (`skip`/`xfail` sólo temporal, documentado y con follow-up).
- **TEST-12** · K hot path: perf en el caso más estresado y el peor perfil realista, promedio y p99; budget documentado por componente; regresión > 5 % bloquea salvo justificación; vigilar N+1.
- **TEST-13** · K perf gate: compara contra un baseline equivalente en un runner suficientemente estable.
- **TEST-14** · K reproducibilidad: donde se reclama determinismo, RNG, reloj e inputs son controlables y el mismo input da el mismo resultado.
- **TEST-15** · K código compartido: un cambio compartido corre las suites de todos los consumidores afectados.

## 8. Diseño y principios (DES) — `architecture.md`, `docs/flags.md`

- **DES-1** · C: Todo cambio respeta Clean Code, SOLID, KISS, DRY, YAGNI, Arquitectura Hexagonal (dominio al centro, deps hacia adentro, lo externo por puertos/adaptadores) y las mejores prácticas vigentes del stack; vara industrial: seguro, robusto, prolijo, estable y durable.
- **DES-2** · C: DRY vs KISS/YAGNI: ante la duda, lo simple y concreto; un proyecto chico o sin IO puede simplificar la Hexagonal declarándolo; una violación deliberada se declara en el changelog.
- **DES-3** · K duplicación: en la segunda repetición se evalúa extraer; sólo se comparte una abstracción estable que no nombra a sus consumidores.
- **DES-4** · K variantes reales: Strategy: cada variante se registra una vez y el caso de uso recibe la estrategia; nunca decide por nombre ni con un `if` que crece.
- **DES-5** · K fronteras declaradas: toda frontera arquitectónica declarada tiene guard ejecutable; una dependencia nueva actualiza el guard en la misma PR.
- **DES-6** · C: El PR body lleva los bloques SADD, PDD y UXDD contestados, o `no-op — <motivo>`; el silencio = "ni lo pensé".
- **DES-7** · K SADD (fronteras, dep nueva, API pública, sistema nuevo, grafo de imports): módulo dueño, delta de deps en dirección válida, patrón existente, fronteras.
- **DES-8** · K PDD (hot path, allocs por iteración, IO síncrono, threads, cómputo pesado): costo, budget, allocations, async-able, test que detecte una regresión 10×.
- **DES-9** · K UXDD (lo que el usuario ve o toca): principios, anti-patterns evitados, capa de marca (voz del producto ≠ voz del sistema), accesibilidad (escala, contraste, no sólo color, teclado, low-motion), heurísticas del proyecto.
- **DES-10** · K frontend: Atomic Design obligatorio (átomos → moléculas → organismos → templates → páginas), sin duplicar ni saltear niveles.
- **DES-11** · K safety/security/físico/pérdida de datos: antes de la feature, modos de falla, peor caso y márgenes verificables.
- **DES-12** · K lógica crítica: deja logs/métricas/trazas suficientes para operarla y diagnosticarla en producción.
- **DES-13** · C: Flags/parámetros: subir la escalera (reusar → parametrizar una familia existente → branch temporal → flag permanente documentado); toggle = switch con default seguro, nunca comentar/borrar código; cada flag con dueño y condición de borrado.
- **DES-14** · C: Ninguna dependencia nueva sin justificación registrada en el PR.

## 9. ENTREGADO (DONE) — `testing.md`

- **DONE-1** · C: Entregado = TDD verde + e2e verde + perf verde si hay hot path + merge a la principal por PR + build/arranque limpio revisado tras mergear; si falta uno, no está entregado.
- **DONE-2** · C: Pipeline a punto fijo: si cualquier etapa (test, diseño, perf, revisión) obliga a un cambio, se re-corre todo desde el principio hasta una pasada entera sin cambios.
- **DONE-3** · C: El PR body confirma: build limpio (exit 0, sin warnings nuevos; toda supresión con su motivo al lado), suite 100 % verde, perf (promedio + p99) si aplica, gate del runtime especial con validaciones activadas, smoke, artefacto de salida citado con seed/fixture, observabilidad, bloques de diseño y principios.
- **DONE-4** · C: Una PR docs-only saltea build/test/captura declarándolo, pero lleva los bloques de diseño que apliquen.
- **DONE-5** · C: Limpieza tras cada entrega: borrar efímeros (capturas revisadas, scripts de un uso, artefactos descartados, ramas mergeadas), nunca lo versionado/útil ni los tests.

## 10. Documentación (DOC) — `architecture.md`, `docs/changelog-fragmentos.md`

- **DOC-1** · C: Roles estrictamente separados; los docs de proceso de la raíz van con basename en MAYÚSCULAS (en FS case-insensitive, verificar con `git ls-files`).
- **DOC-2** · C: `GOAL.md` = norte (casi nunca cambia); `README.md` = visión + estático (arquitectura macro, setup, uso), nunca roadmap ni estado actual.
- **DOC-3** · C: `ARCHITECTURE.md` = arquitectura vigente: bounded contexts, fronteras, dirección de dependencias, invariantes.
- **DOC-4** · K PROGRESS: `PROGRESS.md` = snapshot vivo derivado (funciona ahora / en curso / bloqueado / próximo hito), nunca historia ni backlog; si contradice specs/backlog/changelog, ganan ellos; se actualiza en el mismo merge.
- **DOC-5** · C: `specs/` = specs de feature/sistema con IDs estables (`TEL-001`); `<unidad>/SPEC.md` = contrato local (invariantes, interfaces) que referencia esos IDs; cada requisito vive en un solo lugar; el trailer `Spec: ID` indexa ambos.
- **DOC-6** · C: Fichas, una por archivo: ideas en `ideas.d/` (explícitamente no comprometidas, dificultad + valor), tareas en `backlog.d/` (`NN-slug`, NN = prioridad por ROI, scope + done), entradas en `changelog.d/`; BACKLOG.md e IDEAS.md son índices; el release pliega `changelog.d/` en el CHANGELOG (append-only).
- **DOC-7** · C: ideas.d → backlog.d → changelog.d siempre MOVIENDO (`git mv`), nunca copiando; nada se implementa mientras siga sólo en ideas.d; al entregar, la tarea se borra de backlog.d en la misma PR.
- **DOC-8** · C: Cada PR que entrega escribe su fragmento de changelog (título + fecha + cambios + verificación); README sólo si cambió algo estático; showcase y docs narrativos avanzan en el mismo merge que el código.
- **DOC-9** · K material editorial (no de cliente): una nota del blog del lab por proyecto y día, en el mismo merge (qué → changelog; por qué, cómo e impacto → nota); el trabajo de cliente no se publica.
- **DOC-10** · K publicable/vendible: material de venta en el portfolio de la flota, en lenguaje de cliente (problema → solución → resultado, nunca tecnología), en el mismo merge que avanza lo que se ve; sin él no está entregado.
- **DOC-11** · C: Material público: nunca nombrar personas (sí negocios/marcas); las imágenes son capturas reales del sistema, no mockups ni diagramas.
- **DOC-12** · C: No se crean `*.md` nuevos salvo pedido explícito, spec/plan de un cambio arquitectónico genuino o los docs vivos de este contrato.
- **DOC-13** · C: Knowledge y memoria de los agentes viven versionados en el repo (`docs/kb/` autoritativo y citado, `docs/research/` fuentes, `docs/notes/` gotchas, cada uno con índice), nunca sólo en la memoria per-máquina del harness (se migra).
- **DOC-14** · K knowledge: `docs/kb/` aparece con la primera pieza real; research nueva: leer → diff contra lo condensado → fuente a `docs/research/` → actualizar `docs/kb/` + índice → PR.

## 11. Seguridad, datos y artefactos (SEC) — `delivery.md`

- **SEC-1** · C: Secretos de producción nunca entran en dev/CI; acceder o mutar producción requiere un contexto explícitamente protegido.
- **SEC-2** · C: Scan de seguridad completo (vulns HIGH/CRITICAL, secrets, misconfigs de IaC, licencias) bloqueante pre-merge; lo corregible se arregla; falso positivo o riesgo aceptado va al ignore-file con justificación + `exp:AAAA-MM-DD`, nunca sin ambas.
- **SEC-3** · C: Jamás borrar datos de usuario: la limpieza no toca datos persistidos (DB, stores, WAL, uploads) ni archivos sin commitear; ante la duda, preguntar.
- **SEC-4** · K archivos generados: todo artefacto generado sale de un generador versionado; no se edita a mano.
- **SEC-5** · K toolchains/deploy: las dependencias desplegables se fijan inmutablemente (digest); un cambio de toolchain o de dependencia crítica va en su propia PR.
- **SEC-6** · K binarios grandes: LFS configurado (`.gitattributes`) antes de que el binario entre al historial.

<!-- otara:canonical:end -->

<!-- otara:local:begin -->
## Local — nicolasbatistoni

> Bloque local: hechos mutables de ESTE repo y concreciones a su stack/dominio (GOV-2, GOV-3).

- Perfil: `MINIMAL` (`.otara/profile`). Estado: **activo** (README, comentario de cabecera).
- Rol: **perfil público de GitHub** del titular: el `README.md` de este repo es la portada de su cuenta. Lo actualiza
  a diario `.github/workflows/update-readme.yml` (action `anmol098/waka-readme-stats`, datos de GitHub y WakaTime).
  Arquitectura: `ARCHITECTURE.md`.
- El README es **en parte GENERADO**: todo lo que está entre `<!--START_SECTION:waka-->` y `<!--END_SECTION:waka-->`,
  y `assets/bar_graph.png`, los escribe el workflow; no se editan a mano (el próximo run los pisa). Lo manual es el resto
  del README: badge del workflow, presentación, «Lenguajes y Tecnologías» y «Contacto».
- El workflow debe ser reproducible e idempotente: dos ejecuciones con el mismo input no generan diff. Cuando cambia el
  workflow o lo que genera, se valida en local el YAML (parser + `actionlint`) y el resultado generado, y tras mergear
  se dispara a mano (`workflow_dispatch`) y se revisa el README resultante antes de declararlo entregado.
- Material público (DOC-11): el README es la cara pública del titular; sin datos de clientes ni personas de terceros.
- Verificación de un cambio manual (VER-14, sin CI propio): links e imágenes del README resuelven; SEC-2 en local antes
  de mergear (`trivy fs --scanners secret,misconfig .`, sin hallazgos); `cicd conform` (otaralabs/cicd-toolkit#375,
  todavía sin release) da `✓ Conforme (MINIMAL)`.
  Mergea el agente tras esa verificación (TRUNK-6).
- Waivers:
  - `Waiver DEL-4: update-readme.yml no usa el motor de la flota: es el generador del perfil (cron diario de una
    action de terceros que reescribe el README y commitea), no CI/CD — excepción SÓLO para ese workflow
    (revisar: 2026-12-31)`.
  - `Waiver TRUNK-1: los commits del bot (readme-bot) de update-readme.yml van directo a main, sólo dentro de la zona
    generada; todo cambio manual va por rama + PR (revisar: 2026-12-31)`.
  - `Waiver TEST-14: el workflow todavía no es idempotente: la action commitea siempre, escribe la hora en «Last
    Updated» y el paso de storage deja un segundo commit; backlog.d/02 (revisar: 2026-10-22)`.
  - `Waiver SEC-5: actions/checkout@v4 y anmol098/waka-readme-stats@master (que corre la imagen
    wakareadmestats/waka-readme-stats:master) no están fijados por SHA/digest y reciben GH_TOKEN; backlog.d/01
    (revisar: 2026-10-22)`.
  - `Waiver DEL-2: el repo no versiona hooks (.githooks/); se instalan con cicd install-hooks en su propia PR
    (revisar: 2026-10-22)`.
<!-- otara:local:end -->
