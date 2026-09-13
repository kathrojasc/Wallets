# CLAUDE.md — Instrucciones para Claude Code

Este archivo le dice a Claude Code cómo trabajar sobre este repositorio. Léelo entero antes de modificar nada.

## Qué es el proyecto

Panel web ("dashboard") para el **gestor de un local** que administra un programa de fidelización. El negocio ofrece a sus clientes dos tipos de tarjeta que se guardan en la wallet del móvil:

- **Tarjetas de sellos** (fidelidad): el cliente suma sellos por visita y canjea un premio. Gratis para el cliente.
- **Membresías** de pago: el cliente paga una cuota y obtiene usos o acceso ilimitado por una vigencia.

El panel permite crear tarjetas, ver clientes, enviar notificaciones, gestionar dispositivos de escaneo (staff), ver estadísticas y administrar el plan de suscripción del gestor (Free / Starter / Growth).

Es un **prototipo de alta fidelidad**, no un producto con backend. Data mock, sin llamadas de red (salvo la fuente Inter por CDN).

## Arquitectura

- **SPA sin framework.** Un único `index.html` contiene el HTML de **todas** las vistas y **todo** el JavaScript (inline, en `<script>` al final del body). No hay bundler de módulos ni router.
- **Vistas:** cada pantalla es `<section class="view" data-view="NOMBRE">`. Se muestran/ocultan con la función global **`go('NOMBRE')`** que alterna la clase `.active`. Vistas existentes: `inicio`, `estadisticas`, `clientes`, `tarjetas`, `crear`, `crearTipo`, `notificaciones`, `scanner`, `config`, `planes`, `checkout`, `locales`, `crearLocal`, `localDetail` (las 3 últimas gateadas a Growth) (+ pantallas de auth/onboarding y varios modales).
- **Navegación:** los items con `data-view` o `data-goto` llaman a `go()`. El sidebar izquierdo (`.nav-item`) cambia de vista.
- **Estado:** en memoria (arrays JS mock) + `localStorage` para lo persistente:
  - `w2_plan` → plan actual: `free` | `starter` | `growth`.
  - `w2_prof_rules` → reglas de perfilado de clientes (VIP/Habitual/Casual/En riesgo) configuradas en "Mis clientes".
  - `w2_scan_insight_min` → si el insight de App Scanner está minimizado.
  - `w2_cards_view` → vista de "Mis tarjetas": `grid` | `list`.
- **Estilos:** `assets/styles.css`. Tokens de diseño en `:root` (colores, radio, sombra, ancho máx.). Todos los íconos son **SVG inline** o `data:` URIs en el CSS; no hay librería de íconos.

## Tecnologías

- HTML5, CSS3 (custom properties, grid, flex), JavaScript ES2019+ vanilla.
- Tipografía **Inter**, empaquetada localmente en `assets/fonts/` (sin CDN).
- **Vite** solo como servidor de desarrollo / build de sitio estático (no hay framework).

## Cómo ejecutar

```bash
npm install      # genera package-lock.json
npm run dev      # servidor con hot-reload
```

## Cómo hacer build

```bash
npm run build    # -> dist/  (sitio estático)
npm run preview  # sirve dist/
```

## Convenciones de código

- **CSS compacto y por token.** Reutiliza las variables de `:root` (`--blue`, `--ink`, `--muted`, `--radius`, etc.). No inventes colores nuevos si ya existe un token equivalente.
- **Una regla CSS por línea** donde sea razonable; el archivo ya sigue ese estilo.
- **JavaScript:** funciones y IIFEs pequeñas y autocontenidas al final de `index.html`. Cada bloque nuevo va en su propia IIFE `(() => { ... })()` para no contaminar el scope global. Expón al global solo lo imprescindible (p. ej. `go`, `window.__setPlan`).
- **HTML canónico:** cierra todas las etiquetas, comillas dobles en atributos, no self-closing en elementos no-void.
- **Layout con flex/grid + `gap`**, no márgenes por elemento ni espacios en blanco del código como separación.
- **Español** en toda la UI y los textos (público peruano; moneda `S/`).

## Convenciones de componentes (patrones ya establecidos)

Reutiliza estos patrones en vez de inventar nuevos:

- `.card` — contenedor base (fondo blanco, borde suave, `--radius`).
- `.card.kpi` — número grande con título y sublínea (`.t` / `.n` / `.d`).
- `.chart-card`, `.vcard` — tarjetas de detalle con `<h4>` + `.statline` / `.barline` / `.rows` / `.split` / `.minirow`.
- `.grid.g4` / `.g3` / `.g2` / `.gauto` — rejillas (gauto = `auto-fit minmax(250px,1fr)`).
- `.pill` (+ `.hab` / `.cas` / `.risk`) — etiquetas de perfil de cliente.
- `.badge` (+ `.alerta` / `.oport` / `.logro` / `.tip` / `.tend`) — etiquetas de insight.
- `.kindseg` — selector segmentado (tabs) de Estadísticas.
- `.lockpanel` / `.lockrow` / `.up-badge` — bloques "disponible en plan superior".
- `.js-detail` + `.card-detail` — patrón de **detalle inline expandible** dentro de una tarjeta (drill-down sin modal).
- Modales: `.modal-overlay` + `.modal` con clase `.open`; cerrar con Escape / click en overlay / botón `.sm-close`.
- Toast: `.in-toast` (helper `toast(msg)` en varias IIFEs).

## Reglas de UX/UI

- **Público con poca formación técnica/matemática.** Lenguaje llano, sin jerga ("Premios entregados", no "tasa de canje"; "Clientes que vuelven", no "retención"). Explicar sin saturar de texto.
- **Máx. 1–2 colores de acento por pantalla.** Azul `--blue` es el acento principal; naranja `--orange` para advertencias suaves; rojo `--red` para riesgo/negativo; verde `--green` para positivo.
- **Sin emojis** en la UI (salvo los ya presentes en algún dato de ejemplo).
- **Íconos** siempre SVG inline con `stroke="currentColor"`, `stroke-width` ~1.7–1.8.
- Enlaces usan `.link` (azul, semibold).

## Sistema de diseño

Ver `PROJECT_CONTEXT.md` → sección "Sistema de diseño" para colores HEX, tipografía, espaciado, radios, sombras, breakpoints. Los valores viven en `:root` de `assets/styles.css`.

## Componentes que deben reutilizarse

`.card`, `.kpi`, `.chart-card`, `.vcard`, `.pill`, `.badge`, `.kindseg`, `.lockpanel`, `.js-detail`/`.card-detail`, modales `.modal-overlay/.modal`, toast `.in-toast`. Antes de crear uno nuevo, busca si ya existe.

## Archivos que NO se deben modificar innecesariamente

- `assets/styles.css` **tokens de `:root`**: no los renombres ni cambies sus valores sin una razón de diseño acordada; muchísimas reglas dependen de ellos.
- La función global **`go()`** y el contrato `data-view` / `.view.active`: es el núcleo de la navegación.
- Las claves de `localStorage` (`w2_plan`, `w2_prof_rules`, `w2_scan_insight_min`): cambiar sus nombres rompe el estado guardado.
- `window.__setPlan` y la lógica de `data-tier` (free/starter/growth) en Estadísticas.

## Restricciones importantes

- No hay backend; no añadas llamadas a APIs reales sin acordarlo. La data es mock e intencional.
- No conviertas el proyecto a un framework "de una" — ver `START_HERE.md`.
- No reemplaces recursos faltantes por otros distintos en silencio (ver `ASSETS_INVENTORY.md`).

## Funcionalidades ya implementadas / pendientes / errores conocidos

Ver `PROJECT_CONTEXT.md` (secciones DONE / IN PROGRESS / TODO / KNOWN ISSUES).

## Criterios antes de modificar el diseño

1. ¿El cambio respeta el lenguaje llano y "menos es más" de la UI?
2. ¿Reutiliza tokens y componentes existentes?
3. ¿Mantiene la coherencia entre las 3 pestañas de Estadísticas (General/Sellos/Membresías) y el gating por plan?
4. ¿Conserva el mapeo indicador → plan según el Excel de referencia (ver PROJECT_CONTEXT)?
5. Ante la duda sobre alcance, pregunta antes de rediseñar zonas no solicitadas.
