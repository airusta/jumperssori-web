# Manual de operación y mantenimiento — jumperssori-web

Guía práctica para **operar y mantener** el sitio del Centro Infantil Bilingüe Jumperssori.
No hace falta ser programador: la mayoría son cambios de texto + apretar un botón en cPanel.

> **Última actualización:** 2026-08-01
> Contexto técnico: `README.md` (este repo) · limitación del hosting: DT-003 (en `camaras-server-v2`).

---

## 0. Panorama en 30 segundos

- El sitio es **estático** (HTML). El código vive en GitHub: `github.com/airusta/jumperssori-web`.
- Hay **dos entornos**, ambos en el hosting cPanel:
  - **Staging** (`staging.jumperssori.com`) → para **probar** antes de publicar.
  - **Producción** (`jumperssori.com` + `www`) → lo que ve el público.
- **Flujo:** se edita el código → se sube a GitHub → cPanel lo **despliega** con un botón.
- **Nada más cambia:** cámaras (`camaras.`), WhatsApp, redes y correo son independientes.

```
  Editar (local)  →  git push (GitHub)  →  cPanel "Deploy"  →  Sitio en vivo
                                              │
                              ┌───────────────┴───────────────┐
                          STAGING (probar)              PRODUCCIÓN (público)
                       rama main                     rama production
```

---

## 1. Accesos que necesitás (guardados en el gestor de contraseñas)

| Recurso | Para qué | Dónde |
|---|---|---|
| **cPanel** (`jumperss`) | Desplegar, archivos, correo | `coyote.smart-hosts.com:2083` |
| **Cloudflare** | DNS del dominio | dashboard de Cloudflare |
| **GitHub** (`airusta`) | Código del sitio | `github.com/airusta/jumperssori-web` |
| **Canva** | Artes/logo de marca | canva.com |

> Las contraseñas **no van en este repo** — van en el gestor de contraseñas del admin.

---

## 2. Hacer un cambio y publicarlo (flujo normal)

### 2.1 Editar
El sitio es un solo archivo: **`index.html`**. La mayoría de los cambios (textos, teléfono, enlaces)
son buscar-y-reemplazar ahí. Las imágenes van en **`assets/`**.

### 2.2 Subir a GitHub
```bash
git add -A
git commit -m "describe el cambio"
git push origin main
```

### 2.3 Probar en STAGING
En cPanel → **Git™ Version Control** → *Administrar* el repo `jumperssori-web` → pestaña **Pull or Deploy**:
1. **Update from Remote** (trae el último commit)
2. **Deploy HEAD Commit**
3. Abrí `https://staging.jumperssori.com` y revisá (con **Ctrl+F5** para saltar el caché).

### 2.4 Publicar en PRODUCCIÓN
Cuando staging está OK, se **promueve** a producción (ver §4).

> **Regla de oro:** nunca publiques directo a producción sin haberlo visto antes en staging.

---

## 3. Puesta en marcha de PRODUCCIÓN (una sola vez)

Esto se hace **la primera vez** que se sale a producción. Después, publicar es solo repetir §4.

### 3.1 Crear la rama de producción (ya creada)
La rama **`production`** contiene lo que se publica al público. Se actualiza fusionando `main`.

### 3.2 Crear el clon de producción en cPanel
cPanel → **Git Version Control → Create**:
- **Clone URL:** `https://github.com/airusta/jumperssori-web.git`
- **Repository Path:** `/home2/jumperss/repositories/jumperssori-web-prod`  ← **debe terminar en `-prod`**
- Después de clonar → *Administrar* → **Checkout** la rama **`production`**.

> El `.cpanel.yml` detecta el `-prod` en la ruta y publica en **`public_html`** (apex/www).
> El otro clon (sin `-prod`) sigue publicando en staging. Así un mismo repo sirve a los dos entornos.

### 3.3 Apuntar el dominio a nuestro sitio (corte DNS)
> ⚠️ **Antes de tocar el DNS:** exportá la zona actual de `jumperssori.com` en Cloudflare (respaldo de rollback).

En **Cloudflare → DNS**, cambiar los registros del apex y www (hoy apuntan a Canva `103.169.142.0`):
- `A` · `@` (o `jumperssori.com`) → **`50.28.39.135`** · **Proxied** (nube naranja)
- `A` o `CNAME` · `www` → **`50.28.39.135`** (o `CNAME www → jumperssori.com`) · **Proxied**
- SSL/TLS en modo **Full**.

### 3.4 Verificar
- `https://jumperssori.com` y `https://www.jumperssori.com` cargan el sitio nuevo.
- **`camaras.jumperssori.com` sigue funcionando** (no se tocó) — abrir para confirmar.
- El **correo** llega normal (Zoho, no se tocó).

---

## 4. Publicar a producción (cada vez, después del §3)

1. Fusionar lo probado de `main` a `production` y subir:
   ```bash
   git checkout production
   git merge main
   git push origin production
   git checkout main
   ```
2. En cPanel → Git Version Control → clon **`...-prod`** → **Update from Remote** → **Deploy HEAD Commit**.
3. Abrir `https://jumperssori.com` con **Ctrl+F5** y verificar.

---

## 5. Mantenimiento: tareas comunes

Todas se hacen editando `index.html`, luego el flujo §2 (staging) y §4 (producción).

| Quiero cambiar… | Qué edito |
|---|---|
| **Número de WhatsApp** | Buscar `59175322827` (aparece en botones y carrusel) |
| **Dirección** | Buscar `Av. Satélite 649, El Alto` |
| **Teléfono** | Buscar `59175322827` (en `tel:` y texto) |
| **Redes** | Buscar `facebook.com`, `instagram.com`, `tiktok.com` |
| **Textos** (titulares, niveles, metodología) | Buscar el texto y reemplazar |
| **Edades / horarios** | Buscar `[Edad]` (niveles) y completar |
| **Testimonios** | Buscar `[Testimonio` y `[Nombre]` |
| **Colores de marca** | En el `<style>`, variables `--azul`, `--naranja`, etc. **No usar colores fuera de la paleta.** |

### 5.1 Cambiar o actualizar el logo
Los logos están en `assets/` (`logo-nav.png` naranja, `logo-nav-white.png` blanco). Para actualizarlos:
exportar la nueva versión desde Canva (PNG **transparente**), reemplazar el archivo en `assets/` con el
**mismo nombre**, y desplegar.

### 5.2 Agregar fotos (galería de Áreas)
1. Poner las fotos en `assets/` (formato `.jpg` o `.webp`, optimizadas < 300 KB).
2. En `index.html`, en la sección `#areas`, reemplazar los bloques de color por `<img src="assets/tu-foto.jpg" ...>`.
3. ⚠️ **Sin rostros identificables de menores** en fotos públicas.

### 5.3 Reglas que NO se rompen
- **Solo contenido estático** (el hosting es CentOS 7.9 EOL: nada de PHP/WordPress).
- **Sin colores fuera de la paleta** oficial.
- **Sin menores identificables** en el sitio público.

---

## 6. Volver atrás (rollback)

**Si un cambio salió mal en producción:**
- **Rápido:** en cPanel, en el clon `-prod`, **Deploy** un commit anterior (o revertir en git):
  ```bash
  git checkout production
  git revert HEAD        # deshace el último cambio
  git push origin production
  ```
  Luego **Update from Remote + Deploy** en el clon `-prod`.
- **Corte DNS salió mal:** en Cloudflare, volver los registros `A @` y `www` al valor del respaldo (§3.3).

---

## 7. Verificación / checklist antes de publicar

- [ ] Los **botones de WhatsApp** abren el chat con el número correcto.
- [ ] **Redes** (FB, IG, TikTok) abren el perfil correcto.
- [ ] **"Ingresar en vivo"** lleva a `camaras.jumperssori.com`.
- [ ] Se ve bien en **móvil** (la mayoría del tráfico viene de FB/IG).
- [ ] No hay textos entre `[corchetes]` (placeholders sin completar).
- [ ] Al compartir el link en WhatsApp/FB, se ve la **imagen de vista previa** (Open Graph).

---

## 8. Problemas frecuentes (troubleshooting)

| Síntoma | Causa / solución |
|---|---|
| **WhatsApp dice "número no está en WhatsApp"** | El sitio en vivo tiene una versión vieja → **Update from Remote + Deploy** y **Ctrl+F5**. |
| **Los cambios no aparecen** | Caché del navegador → **Ctrl+F5** (o recarga forzada en el móvil). O falta **Deploy** en cPanel. |
| **Íconos/enlaces no funcionan** | Igual que arriba: falta desplegar la última versión. |
| **El deploy falla en cPanel** | Puede ser el shell del hosting. Alternativa: subir los archivos a mano por **Administrador de archivos** al docroot. |
| **No se puede clonar por SSH** | Es **DT-003** (hosting con OpenSSH viejo): por eso el repo es **público** y se clona por **HTTPS**. |
| **El certificado da error** | Poner el subdominio en **Proxied** (naranja) en Cloudflare + SSL **Full**. |

---

## 9. Mapa del ecosistema (qué toca qué)

| Sitio | Hostname | Se gestiona en | ¿Este manual lo toca? |
|---|---|---|---|
| **Landing** | `jumperssori.com` + `www` | Este repo → cPanel `public_html` | ✅ Sí |
| **Staging** | `staging.jumperssori.com` | Este repo → cPanel staging | ✅ Sí (pruebas) |
| **Cámaras** | `camaras.jumperssori.com` | Proyecto `camaras-server-v2` (on-premise) | ❌ No |
| **WhatsApp** | `whatsapp.` · `api.` | Meta / wa.me | ❌ No |
| **Redes** | `facebook.` · `tiktok.` · `call.` | Redirects de Cloudflare | ❌ No |
| **Correo** | (MX) | Zoho Mail | ❌ No |

---

## 10. A quién recurrir

- **Contenido/diseño del sitio:** editar `index.html` (este repo) y desplegar.
- **Hosting/cPanel:** proveedor **Neothek** (`portal.neothek.com` → Soporte). Ticket abierto por DT-003 (SSH).
- **DNS/dominio:** **Cloudflare** (DNS autoritativo).
- **Cámaras:** proyecto `camaras-server-v2` (server on-premise en el jardín).
