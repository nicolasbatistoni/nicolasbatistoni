# Arquitectura — nicolasbatistoni/nicolasbatistoni

Arquitectura vigente (DOC-3). Es el repo especial de perfil de GitHub: su `README.md` es la portada de la cuenta
`nicolasbatistoni`. No hay código de aplicación: hay un README con una zona manual y otra generada, y un workflow
(una action de terceros más un paso de shell propio) que regenera la segunda.

## Flujo: workflow → fuentes → README generado

```
schedule (cron "0 12 * * *") ─┐
workflow_dispatch ────────────┴─> .github/workflows/update-readme.yml  (ubuntu-latest, contents: write)
                                    1. actions/checkout@v4 (token: secrets.GH_TOKEN) — lo usa el paso 3
                                    2. anmol098/waka-readme-stats@master (imagen docker wakareadmestats/
                                       waka-readme-stats:master): clona SU PROPIA copia del repo, consulta
                                       GitHub (GH_TOKEN) y WakaTime (WAKATIME_API_KEY, obligatoria), reescribe
                                       la zona generada + assets/bar_graph.png, commitea «chore: actualizar
                                       métricas del perfil» y pushea a main
                                    3. paso de shell propio: git pull; si aparece «Almacenamiento de GitHub
                                       utilizado», borra esa línea y la siguiente; commit «chore: ocultar línea
                                       de storage del perfil» como readme-bot + push a main
```

- Flags que el workflow fija: prendidos `SHOW_SHORT_INFO`, `SHOW_COMMIT`, `SHOW_DAYS_OF_WEEK`,
  `SHOW_LANGUAGE_PER_REPO`, `SHOW_LINES_OF_CODE`, `SHOW_LOC_CHART`, `SHOW_UPDATED_DATE`; apagados
  `SHOW_TOTAL_CODE_TIME`, `SHOW_LANGUAGE`, `SHOW_EDITORS`, `SHOW_OS`, `SHOW_PROJECTS`, `SHOW_TIMEZONE`,
  `SHOW_PROFILE_VIEWS`; `LOCALE: "es"`. Lo que no fija toma el default de la action en `@master`: así quedan
  prendidos `SHOW_AI_CODE_TIME` y `SHOW_AI_CODING` (la sección «AI Coding This Week», dato de WakaTime).
- WakaTime se consulta en cada corrida aunque la mayoría de sus secciones estén apagadas; los horarios y días de
  commits salen de GitHub pero se calculan con la zona horaria de WakaTime.
- El paso 3 existe porque `SHOW_SHORT_INFO` es todo-o-nada y la línea de storage no se quiere mostrar (el
  comentario del workflow la llama «mal formateada»).
- Los commits del workflow van directo a `main` (`Waiver TRUNK-1` en `AGENTS.md`); no usa el motor de CI/CD de la
  flota (`Waiver DEL-4`).

## Zonas del README

| Zona | Quién la escribe |
|---|---|
| Comentario de cabecera (estado y aviso de zona generada) | Manual |
| Badge del workflow, saludo, «Acerca de mí», link a la web | Manual |
| Entre `<!--START_SECTION:waka-->` y `<!--END_SECTION:waka-->`: líneas de código, datos de GitHub, horarios y días de commits, «AI Coding This Week», lenguajes por repo, «Cronología» y «Last Updated» | **Generada** por el workflow; no se edita a mano |
| `assets/bar_graph.png` (la «Cronología», enlazada por URL absoluta a `raw.githubusercontent.com/.../main/assets/bar_graph.png`) | **Generada** por el workflow |
| «Lenguajes y Tecnologías» (badges de shields.io) y «Contacto» | Manual |

## Invariantes y estado

- Un cambio manual no toca la zona generada ni `assets/bar_graph.png`: el próximo run los pisa.
- Objetivo: dos ejecuciones con el mismo input no generan diff. **Hoy no se cumple** — la action commitea siempre
  (también sin cambios), cada corrida escribe la hora en «Last Updated» y el paso 3 agrega un segundo commit; ver
  `backlog.d/02` (`Waiver TEST-14`).
- La action y la imagen que corre no están fijadas por SHA/digest: secciones nuevas pueden aparecer sin tocar el
  workflow (así llegó «AI Coding This Week»); ver `backlog.d/01` (`Waiver SEC-5`).
