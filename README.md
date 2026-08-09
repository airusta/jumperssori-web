# jumperssori-web

Sitio institucional estático del **Centro Infantil Bilingüe Jumperssori** (landing page).
Separado del sistema de cámaras (`camaras-server-v2`). Se hostea en el cPanel del dominio
(Neothek · «Plan Plata», `coyote.smart-hosts.com`) y se publica vía **Cloudflare** (DNS autoritativo).

> **Última actualización:** 2026-08-01
> 📖 **Para operar y mantener el sitio: [MANUAL-OPERACION.md](MANUAL-OPERACION.md).**
> Plan e infraestructura: `camaras-server-v2/docs/PLAN-WEB-INSTITUCIONAL.md` · limitación del hosting: DT-003.

---

## Estructura

```
jumperssori-web/
├── index.html            # landing (single-file, estático, sin build)
├── particles.html        # PRUEBA: fondo animado de partículas (plexus) con colores de marca
├── assets/
│   ├── logo-nav.png       # logo naranja (nav) — exportado de Canva
│   ├── logo-nav-white.png # logo blanco (footer) — recoloreado
│   └── logo-kit-1/2/3.png # variantes originales del logo (referencia de marca)
├── .cpanel.yml           # deploy a cPanel (copia *.html + assets/ al docroot)
├── .gitattributes        # fuerza LF en .cpanel.yml/YAML (deploy Linux)
└── README.md
```

**Stack:** HTML/CSS estático, sin build. Tipografía **Baloo 2** (display) + **Nunito** (cuerpo) por Google Fonts.
`particles.html` usa Tailwind CDN + particles.js 2.0.0 (solo esa página de prueba).

---

## Entornos

| Entorno | Hostname | Docroot cPanel | Rama |
|---|---|---|---|
| **Staging** | `staging.jumperssori.com` | `/home2/jumperss/public_html/staging.jumperssori.com` | `main` |
| **Producción** | `www.jumperssori.com` + apex | `/home2/jumperss/public_html` | `main` (pendiente, ver abajo) |

Repo **público** en `github.com/airusta/jumperssori-web` (necesario para clonar por HTTPS, ver DT-003).

---

## Deploy (cPanel Git™ Version Control)

1. cPanel → **Git Version Control** → repo clonado en `/home2/jumperss/repositories/jumperssori-web`.
   - **Clone URL por HTTPS** (`https://github.com/airusta/jumperssori-web.git`), **no SSH**: el hosting bloquea
     el puerto 22 saliente y su OpenSSH 7.4 (CentOS 7.9) es incompatible con GitHub (ver **DT-003**).
2. Para publicar cambios: **Update from Remote** → **Deploy HEAD Commit**. El `.cpanel.yml` copia `*.html` + `assets/`
   al docroot del subdominio.
3. DNS en **Cloudflare**: registro `A staging → 50.28.39.135`, **Proxied**. TLS resuelto por Cloudflare.

**Loop de iteración:** editar local → `commit` + `push` → cPanel *Update from Remote* + *Deploy HEAD Commit*.

---

## Marca (paleta oficial — usar SOLO estos colores)

| Color | Hex | Uso |
|---|---|---|
| Azul cielo | `#5FA7DA` | Primario, secciones destacadas |
| Naranja | `#FF914D` | Botones y llamadas a la acción |
| Amarillo suave | `#FFE680` | Acentos y destacados |
| Verde menta | `#5CB693` | Acentos secundarios |
| Gris | `#949393` | Texto secundario |
| Blanco cálido | `#FFFEFA` | Fondo general |

**Logo real** exportado del brand kit «JUMPERSSORI» de Canva (wordmark inclinado + «CENTRO INFANTIL
BILINGÜE» flanqueado por dos puntos azules). Variantes en `assets/`.

**Dirección de diseño:** cálido y jugado para familias, formas redondeadas, mobile-first. Se aplicaron
principios anti-slop de tres skills (Emil Kowalski / Impeccable / taste-skill): sin em-dashes, íconos de
librería (Tabler), motion con easing fuerte, `prefers-reduced-motion`, sin «tells» de IA.

---

## Secciones de la landing (según brief)

1. **Hero** — titular «Donde tus hijos aprenden jugando, en dos idiomas» + botón WhatsApp.
2. **Niveles** — Nido / Parvulario / Preparatorio (edad + descripción).
3. **Metodología** — bilingüe + Montessori, con 4 beneficios (incluye «Cámaras en vivo»).
4. **Áreas** — galería de instalaciones (psicomotricidad, aulas, juego).
5. **Testimonios** — 2-3 tarjetas de familias.
6. **Inscripciones** — CTA fondo azul cielo + datos de contacto.
7. **Footer** — logo blanco, contacto, redes.

---

## Reglas

- **Solo estático** (el hosting corre CentOS 7.9 EOL: nada dinámico expuesto).
- **Sin rostros identificables de menores** en el sitio público (privacidad).
- **Sin colores fuera de la paleta** oficial.
- Contenido con `[corchetes]` = **placeholder pendiente de datos reales**.

---

## Pendientes

- [ ] **Número de WhatsApp** del bot (hoy `https://wa.me/591XXXXXXXX` en hero, inscripciones y particles.html).
- [ ] **Contenido real**: edades por nivel, direcciones, teléfono, horarios, testimonios (con permiso).
- [ ] **Fotos reales** de instalaciones para la galería de «Áreas» (sin menores identificables).
- [ ] **Links de Facebook e Instagram** en el footer.
- [ ] **Acentos del copy** (algunas palabras quedaron sin tilde en el borrador).
- [ ] **Decidir `particles.html`**: descartar, dejar como pantalla de entrada, o integrar sutil al hero.
- [ ] **Corte a producción**: reemplazar el `www` actual (hecho en Canva, apunta a `103.169.142.0`) por
      este sitio → apuntar `A @` y `CNAME www` a `50.28.39.135` en Cloudflare.
