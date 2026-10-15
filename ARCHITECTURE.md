# Arquitectura — nicolasbatistoni/nicolasbatistoni

Arquitectura vigente (DOC-3). Es el repo especial de perfil de GitHub: su `README.md` es la portada de la cuenta
`nicolasbatistoni`. No hay código de aplicación ni workflows.

## Estado actual: un README manual

| Zona | Quién la escribe |
|---|---|
| Todo el `README.md`: presentación, «Acerca de mí», «Lenguajes y Tecnologías» y «Contacto» | Manual, por rama + PR |

El generador de terceros que reescribía una zona del README a diario (`anmol098/waka-readme-stats`, con la imagen
`:master` sin fijar y un PAT del titular) se retiró el 2026-10-15 junto con su zona generada y su gráfico
(`assets/bar_graph.png`). El motivo: corría código mutable de terceros con un token que lee todos los repos del
titular, y mostraba métricas de vanidad (líneas de código, commits por hora del día).

## Lo que viene

Un generador propio, versionado y testeado en este repo, que dibuja los KPIs del titular para clientes y
recruiters (co-diseñados con ChatGPT) como tarjetas SVG claras y oscuras con la estética del design system Otara
Labs. Ver `backlog.d/01-generador-propio.md`. Cuando entre, este documento describe su flujo, sus fuentes, sus
zonas y sus invariantes.
