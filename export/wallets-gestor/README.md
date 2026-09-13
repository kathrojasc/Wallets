# Wallets — Panel del Gestor de Local

Prototipo hi-fi (alta fidelidad) de un panel web para gestores de local que administran un programa de fidelización basado en **tarjetas de sellos** y **membresías de pago** guardadas en la wallet del móvil del cliente.

> **Importante:** este proyecto es un **prototipo funcional en HTML/CSS/JavaScript vanilla** (sin framework, sin backend). Toda la data es de ejemplo (mock) y vive en el propio `index.html`. Sirve como referencia de diseño e interacción de altísima fidelidad, listo para continuar el desarrollo o migrarlo a un stack de producción.

## Requisitos

- **Node.js 18+** (solo para el servidor de desarrollo; la app en sí no requiere build).
- Un navegador moderno (Chrome, Edge, Safari, Firefox).
- No requiere internet: la tipografía **Inter** está empaquetada localmente en `assets/fonts/`.

## Instalación

```bash
npm install
```

> Esto genera el `package-lock.json` (no se incluye en el ZIP porque se crea al instalar). Es el único paso "manual" tras descargar.

## Dependencias

- **vite** (solo dev): sirve el proyecto y hace el build de un sitio estático. No hay dependencias de runtime; la app es HTML/CSS/JS puro.

## Comandos

| Comando | Qué hace |
|---|---|
| `npm run dev` | Levanta el servidor de desarrollo (Vite) y abre la app con recarga en caliente. |
| `npm run build` | Genera el sitio estático en `dist/`. |
| `npm run preview` | Sirve el build de `dist/` para verificar el resultado. |

### Alternativa sin Node

Como es HTML estático, también puedes abrirlo con cualquier servidor estático:

```bash
npx serve .
# o
python3 -m http.server 8000
```

(Evita abrir `index.html` con doble clic vía `file://`: algunos navegadores bloquean la carga de `assets/styles.css` con esa ruta. Usa un servidor local.)

## Estructura del proyecto

```
wallets-gestor/
├── index.html            # La app completa: HTML de todas las vistas + TODO el JavaScript (inline)
├── assets/
│   ├── styles.css        # Sistema de diseño + estilos de todas las vistas (tokens en :root)
│   ├── avatar.jpg        # Foto del usuario del panel (esquina inferior del sidebar)
│   └── fonts/            # Inter empaquetada localmente (variable, subset latin)
├── package.json          # Scripts de dev/build con Vite
├── .gitignore
├── README.md             # Este archivo
├── CLAUDE.md             # Instrucciones para Claude Code
├── PROJECT_CONTEXT.md    # Contexto, decisiones, sistema de diseño, estado
├── START_HERE.md         # Prompt inicial para Claude Code
└── ASSETS_INVENTORY.md   # Inventario de recursos (imágenes, íconos, fuentes)
```

## Cómo continuar el desarrollo

1. Lee `CLAUDE.md` y `PROJECT_CONTEXT.md` antes de tocar nada.
2. La app es una SPA sin router: las vistas son `<section class="view" data-view="...">` y se muestran con la función global `go('nombre')`. Añadir una vista = añadir su `<section>` + un item de nav + (si aplica) su lógica.
3. El estado del plan (Free/Starter/Growth) se guarda en `localStorage` (`w2_plan`) y controla qué indicadores se ven en Estadísticas.
4. Si vas a migrar a React/Vue/Next, hazlo por vistas y conserva el sistema de diseño de `assets/styles.css` (o pórtalo a tokens/Tailwind). No conviertas todo de golpe.
