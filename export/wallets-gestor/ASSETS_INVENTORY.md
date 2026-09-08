# ASSETS_INVENTORY.md — Inventario de recursos

Este proyecto es casi 100% autocontenido: la mayoría de la "iconografía" e ilustraciones son **SVG inline** dentro de `index.html` o **data:URIs** dentro de `assets/styles.css`, por lo que viajan en el propio código y no son archivos aparte.

## Recursos incluidos físicamente en el ZIP

| Archivo | Tipo | Dónde se usa | Origen | Cómo recrearlo |
|---|---|---|---|---|
| `assets/avatar.jpg` | Imagen JPEG | Foto del usuario del panel (Katherine Rojas), esquina del sidebar. Cargada vía `background: url(avatar.jpg)` en `.avatar` (styles.css). | Foto de placeholder usada en el prototipo. | Reemplazar por la foto real del usuario, cuadrada (se recorta a círculo de 28px). Mantener el nombre o actualizar la ruta en `.avatar`. |
| `assets/styles.css` | CSS | Todos los estilos + tokens + íconos data:URI. | Escrito a mano en el proyecto. | — (es código fuente, no un asset generado) |
| `index.html` | HTML/JS | La app completa. | Escrito a mano en el proyecto. | — |

## Recursos que NO son archivos (viven en el código)

| Recurso | Tipo | Dónde | Notas |
|---|---|---|---|
| Todos los íconos de UI | SVG inline | `index.html` (y algunos como `data:` URI en `styles.css`) | `stroke="currentColor"`, sin librería externa. Editables en el propio markup. |
| Ilustraciones "hero" / arte de tarjeta 3D, pin de local, campana, etc. | SVG inline con `<defs>`/gradientes | `index.html` (secciones Inicio, drawers, modales) | Dibujadas a mano en SVG; se pueden ajustar cambiando paths/gradientes. |
| Checks de listas, chevrons de `<select>`, texturas de "grano" | `data:image/svg+xml` en CSS | `assets/styles.css` (`.pc-feats li::before`, `select.input`, `.auth-grain`, etc.) | Codificados en la propia regla CSS. |
| Códigos QR / póster imprimible | Generados en runtime por JS (canvas/SVG) | `index.html` (`qrSVG`, `downloadQRpng`) | Se generan en el navegador; no hay imagen QR guardada. |

## Recurso externo por CDN (no empaquetado)

| Recurso | Origen | Uso | Alternativa fiel |
|---|---|---|---|
| Tipografía **Inter** (pesos 400–800) | Google Fonts: `https://fonts.googleapis.com/css2?family=Inter:...` | Fuente de toda la UI (`<link>` en `index.html`). | Para offline / producción: descargar Inter (SIL Open Font License) desde rsms.me/inter o Google Fonts, colocarla en `assets/fonts/` y declarar `@font-face` en `styles.css`, quitando el `<link>` al CDN. |

> **No incluido y por qué:** no fue posible empaquetar Inter en el ZIP porque se sirve por CDN. Todo lo demás (imágenes, íconos, ilustraciones) está incluido físicamente o embebido en el código. No se ha sustituido ningún recurso por otro distinto.

## Screenshots de referencia

No se incluyen capturas por defecto. Si las quieres dentro del ZIP como referencia visual, pídemelo y las agrego en `docs/screenshots/`.
