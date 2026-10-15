# Un generador propio del README de perfil, con los KPIs del titular para clientes y recruiters
> Contexto: el 2026-10-15 el titular pidió descartar la plantilla de terceros (`anmol098/waka-readme-stats`) y hacer
> un README propio con los KPIs más relevantes para su rol de cara a clientes y recruiters, co-diseñados con ChatGPT
> y con la estética diseñada en Claude Design (design system Otara Labs).
> **Scope:**
> - KPIs con definición, ventana y fuente escritas, que se muestran aunque empeoren;
> - generador en Python 3.12 (Typer, pytest), sin actions ni imágenes de terceros sin fijar;
> - tarjetas SVG claras y oscuras (`<picture>` con `prefers-color-scheme`) con las fuentes embebidas;
> - un workflow propio, con permisos mínimos, que lee con un token de solo lectura de la org (`ORG_READ_TOKEN`) y
>   commitea una sola vez, y solo si cambiaron los datos (idempotente: sin hora de corrida).
> **Done:**
> - TDD del cálculo de cada KPI y del render; e2e del workflow con un control negativo;
> - las capturas en claro y oscuro, revisadas también por ChatGPT;
> - dos `workflow_dispatch` seguidos: el primero deja a lo sumo un commit y el segundo ninguno;
> - ARCHITECTURE.md describe el flujo nuevo.
