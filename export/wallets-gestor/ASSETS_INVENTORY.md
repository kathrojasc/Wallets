# ASSETS_INVENTORY.md — Inventario de recursos

Este proyecto es casi 100% autocontenido: la mayoría de la "iconografía" e ilustraciones son **SVG inline** dentro de `index.html` o **data:URIs** dentro de `assets/styles.css`, por lo que viajan en el propio código y no son archivos aparte.

## Recursos incluidos físicamente en el ZIP

| Archivo | Tipo | Dónde se usa | Origen | Cómo recrearlo |
|---|---|---|---|---|
| `assets/avatar.jpg` | Imagen JPEG | Foto del usuario del panel (Katherine Rojas), esquina del sidebar. Cargada vía `background: url(avatar.jpg)` en `.avatar` (styles.css). | Foto de placeholder usada en el prototipo. | Reemplazar por la foto real del usuario, cuadrada (se recorta a círculo de 28px). Mantener el nombre o actualizar la ruta en `.avatar`. |
| `assets/styles.css` | CSS | Todos los estilos + tokens + íconos data:URI. | Escrito a mano en el proyecto. | — (es código fuente, no un asset generado) |
| `assets/fonts/inter-latin-variable.woff2` | Fuente variable WOFF2 | Tipografía de toda la UI, vía `@font-face` en `styles.css` (subset `latin`, pesos 400–800). | Descargada de Google Fonts (`fonts.gstatic.com`), empaquetada localmente. | Regenerar con `curl` a `fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800` y tomar la URL del bloque `/* latin */`. |
| `index.html` | HTML/JS | La app completa. | Escrito a mano en el proyecto. | — |

## Recursos que NO son archivos (viven en el código)

| Recurso | Tipo | Dónde | Notas |
|---|---|---|---|
| Todos los íconos de UI | SVG inline | `index.html` (y algunos como `data:` URI en `styles.css`) | `stroke="currentColor"`, sin librería externa. Editables en el propio markup. |
| Ilustraciones "hero" / arte de tarjeta 3D, pin de local, campana, etc. | SVG inline con `<defs>`/gradientes | `index.html` (secciones Inicio, drawers, modales) | Dibujadas a mano en SVG; se pueden ajustar cambiando paths/gradientes. |
| Checks de listas, chevrons de `<select>`, texturas de "grano" | `data:image/svg+xml` en CSS | `assets/styles.css` (`.pc-feats li::before`, `select.input`, `.auth-grain`, etc.) | Codificados en la propia regla CSS. |
| Códigos QR / póster imprimible | Generados en runtime por JS (canvas/SVG) | `index.html` (`qrSVG`, `downloadQRpng`) | Se generan en el navegador; no hay imagen QR guardada. |

## Recursos externos por CDN

Ninguno. Inter se empaquetó localmente (ver tabla anterior); el proyecto ya no depende de ningún CDN.

## Screenshots de referencia

No se incluyen capturas por defecto. Si las quieres dentro del ZIP como referencia visual, pídemelo y las agrego en `docs/screenshots/`.
