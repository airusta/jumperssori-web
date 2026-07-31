# jumperssori-web

Sitio institucional estático del **Centro Infantil Jumperssori** (landing).
Separado del sistema de cámaras (`camaras-server-v2`). Se hostea en el cPanel del dominio
(Neothek · «Plan Plata», `coyote.smart-hosts.com`) y se publica vía **Cloudflare** (DNS autoritativo).

> Plan y contexto de infraestructura: ver `camaras-server-v2/docs/PLAN-WEB-INSTITUCIONAL.md`.

## Estructura

```
jumperssori-web/
├── index.html      # landing (single-file, estático — sin build)
├── assets/         # imágenes/recursos (opcional, se crea cuando haya)
└── .cpanel.yml     # tareas de deploy a cPanel (Git Version Control)
```

## Entornos

| Entorno | Hostname | Docroot cPanel | Rama |
|---|---|---|---|
| **Staging** | `staging.jumperssori.com` | `~/staging.jumperssori.com` | `main` |
| **Producción** | `www.jumperssori.com` + apex | `~/public_html` | `main` (o tag) |

## Deploy (cPanel Git™ Version Control)

1. En cPanel → **Git Version Control** → clonar este repo (`airusta/jumperssori-web`).
2. cPanel lee `.cpanel.yml` → **Update from Remote** + **Deploy HEAD Commit** copia `index.html`
   al docroot del subdominio.
3. Publicar el hostname como registro **A → 50.28.39.135** (proxied) en **Cloudflare**.

## Reglas

- **Solo estático** (el hosting corre CentOS 7.9 EOL: nada dinámico expuesto).
- **Sin rostros identificables de menores** en el sitio público (privacidad).
- Contenido con `[corchetes]` = placeholder pendiente de datos reales.
- Marca: azul `#5fa7da`, naranja `#ff914d`, amarillo `#ffe680` (ADR-0020 del proyecto de cámaras).
