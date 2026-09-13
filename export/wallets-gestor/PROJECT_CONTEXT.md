# PROJECT_CONTEXT.md — Wallets, Panel del Gestor de Local

## Objetivo del proyecto

Dar al gestor de un local (dueño/encargado) un panel simple para **crear y administrar un programa de fidelización** basado en tarjetas de sellos y membresías guardadas en la wallet del móvil del cliente, y **entender su negocio** con indicadores y sugerencias accionables.

## Problema que resuelve

Los negocios pequeños/medianos quieren fidelizar clientes pero no tienen herramientas simples: las plantillas físicas de sellos se pierden y no dan datos. Este panel digitaliza el programa, registra visitas desde caja (escaneo de QR) y convierte esos datos en indicadores fáciles de entender y en acciones (avisos, campañas).

## Público objetivo

Gestores de local en Perú (cafeterías, restaurantes, gimnasios, spas, retail de proximidad, etc.), **con poca formación técnica y matemática**. Por eso la UI usa lenguaje cotidiano y evita jerga y saturación de datos. Moneda: `S/` (soles).

## Funcionalidades implementadas (DONE)

- **Autenticación / onboarding** (pantallas mock) y modal de bienvenida.
- **Inicio:** saludo, hero con "insight del día", accesos.
- **Mis tarjetas:** listado de tarjetas de sellos y membresías; estado vacío; crear tarjeta.
- **Crear tarjeta:** selector de tipo (dos filas seleccionables: sellos / membresía) → flujo de creación con vista previa de la tarjeta wallet, QR y póster imprimible.
- **Mis clientes:** tabla de clientes con columna **Perfil** (VIP / Habitual / Casual / En riesgo); buscador y filtros; **panel de reglas de perfil** configurable por el gestor (por frecuencia de visitas/mes y recencia), persistido en `localStorage` (`w2_prof_rules`); drawer de detalle de cliente con su "billetera" (acordeón de sus tarjetas) y datos editables.
- **Notificaciones:** crear campañas por segmento; sin opciones de cumpleaños (se movieron a la config de la tarjeta).
- **App Scanner:** cuentas de scanner para el staff (usuario + contraseña) en **formato tabla**; modal de creación con credenciales; estado en línea, sellos dados y clientes nuevos por scanner; **insight con IA** minimizable (`w2_scan_insight_min`); resumen (KPIs) e insight arriba.
- **Estadísticas** (foco principal de las últimas iteraciones): ver más abajo.
- **Configuración:** datos del negocio; **Plan y facturación** (movido aquí desde el menú); reglas; eliminar cuenta.
- **Planes y checkout:** Free / Starter / Growth; el checkout activa el plan y actualiza toda la app (`window.__setPlan`, `localStorage w2_plan`).
- **Locales (multi-local):** gateado a Growth — nav item oculto en Free/Starter, acceso directo a `locales`/`crearLocal`/`localDetail` redirige a Planes con toast. Reactivo a `window.__setPlan`.
- **Sistema de Insights:** ver sección propia más abajo.
- **Top clientes por frecuencia (Inicio):** tabla nueva, gateada a Starter, conectada a la misma fuente de datos que "Mis clientes" (`window.__clients`) — ver sección de Insights.

### Sistema de Insights

9 insights mockeados en 6 pantallas (Inicio ×4, Mis clientes, Notificaciones, Locales, Detalle de tarjeta, Detalle de local), auditados contra la hoja "Insights del Gestor" del Excel de referencia (`Dashboard_Final_v15.xlsx`) y corregidos para que su umbral/copy coincida con su ficha:

| Insight | Pantalla | Ficha | Plan |
|---|---|---|---|
| "20 clientes a 1 sello del premio" | Inicio (hero + top insights) | #5 | Free |
| "12 clientes están a 1 sello del premio" | Detalle de tarjeta | #5 | Free |
| "Tus clientes del programa visitan más seguido" | Mis clientes | #2 | Free |
| "38 clientes llevan +21 días sin volver" | Notificaciones | #3 | Free |
| "La tasa de canje bajó 12 pts" | Inicio | #8 | Starter |
| "Los VIP crecieron +18%" | Inicio | #13 | Starter |
| "14 membresías vencen esta semana" | Inicio | indicador #10 | Starter |
| "Barranco tiene 20 pts menos de retención" | Locales | #16 | Growth |
| Insight dinámico de retención | Detalle de local | #16 | Growth |

Mecanismo: cada bloque de insight lleva `class="ins-gate" data-tier="free|starter|growth"`; se muestra/oculta con la misma escala `RANK` que ya usa Estadísticas (no es un mecanismo paralelo). Free ve 4/9, Starter 7/9 (acumulativo), Growth 9/9.

### Estadísticas (detalle)

- **3 pestañas** (`.kindseg`): **General · Sellos · Membresías** (por defecto General). No hay pestaña "Todos".
- Cada indicador es una tarjeta con `data-tier` = `free` | `starter` | `growth`. El **plan actual** decide qué se ve; al mejorar el plan, los indicadores de ese nivel aparecen con datos en su lugar.
- **Detalle inline:** las tarjetas que lo ameritan tienen "Ver detalle" (`.js-detail`) que **expande dentro de la misma tarjeta** (`.card-detail`, a todo el ancho) una tabla con la lista real (clientes que se alejan, cerca del premio, top 10, socios sin uso, por renovar, etc.). **No se usan modales** para esto.
- **Panel lateral derecho "Ideas para hoy"** fijo y consistente en las 3 pestañas (sugerencias del asistente).
- **Panel "También disponible en planes superiores"** por pestaña (`.lockpanel` / `.lockrow`): lista informativa de lo que se desbloquea; **sin botón de "Ver planes"** (el upgrade vive en el sidebar "Mejora tu plan").

## Flujos de usuario principales

1. **Onboarding → crear primera tarjeta:** bienvenida → "Crear tarjeta" → elegir tipo → diseñar → compartir QR/póster.
2. **Registro de visita (conceptual):** el staff inicia sesión en la app scanner con el usuario/contraseña que le da el gestor; al escanear, se suma sello / uso y, si el cliente se registra, se atribuye a ese scanner.
3. **Entender el negocio:** Estadísticas → elegir pestaña (General/Sellos/Membresías) → leer KPIs → "Ver detalle" para ver la lista de clientes detrás del número → actuar (aviso/campaña).
4. **Actuar sobre clientes en riesgo:** Estadísticas o Ideas → "Ver clientes" → enviar recordatorio.
5. **Mejorar de plan:** sidebar "Mejora tu plan" → Planes → Checkout → se activa y aparecen los indicadores del nuevo plan.

## Decisiones UX/UI importantes (con motivo)

- **Lenguaje llano en toda la UI.** Motivo: público con poca formación técnica. Ej.: "Premios entregados" (no "tasa de canje"), "Clientes que vuelven" (no "retención"), "Clientes que se están alejando" (no "en riesgo de abandono").
- **Estadísticas por 3 pestañas de tipo.** Motivo: antes los indicadores de sellos, membresías y generales estaban mezclados y desordenados; el usuario pidió un selector claro. Se descartó la pestaña "Todos" por redundante.
- **Detalle inline (no modal).** Motivo: el usuario pidió explícitamente "vista interna en el widget" y rechazó los modales.
- **Ideas en panel lateral derecho.** Motivo: antes se repetían en cada pestaña; el usuario lo marcó como redundante.
- **Gating por plan con paneles informativos (no muros de tarjetas borrosas).** Motivo: la versión con ~15 tarjetas bloqueadas/borrosas saturaba al usuario Free. Se consolidó en un panel por pestaña + revelado real al mejorar el plan.
- **Facturación dentro de Configuración.** Motivo: el usuario pidió sacarla del menú lateral; el upgrade ya está en el sidebar.
- **App Scanner = cuentas usuario/contraseña en tabla.** Motivo: se pidió pasar de "dispositivos vinculados por código" a cuentas de staff, y de tarjetas a lista/tabla.
- **Reglas de perfil configurables por el gestor.** Motivo: que el dato "Perfil" (VIP/Habitual/Casual/En riesgo) sea coherente en toda la plataforma y ajustable al negocio.

## Reglas de diseño

- Máx. 1–2 colores de acento por pantalla. Azul = acento principal.
- Sin emojis en la UI. Íconos SVG inline (`stroke="currentColor"`).
- "Menos es más": nada de relleno, stats o íconos innecesarios.
- Reutilizar tokens y componentes existentes antes de crear nuevos.

## Casos de uso, estados vacíos y de error

- **Estados vacíos:** "Mis tarjetas" y "Mis clientes" tienen empty-state con ilustración + CTA (p. ej. "Ir a Mis tarjetas"). App Scanner tiene su empty-state ("Aún no tienes scanners").
- **Validación:** el modal de crear scanner valida nombre obligatorio y unicidad de usuario; el panel de reglas de perfil valida que los umbrales vayan de mayor a menor.
- **Estados de error:** al ser prototipo mock no hay errores de red; las acciones muestran un **toast** de confirmación (`.in-toast`).
- **Feedback:** toasts para acciones (crear scanner, aplicar reglas, activar plan, "acción enviada" en los detalles).

## Responsive behavior

- Layout de app con sidebar + main (`.app` flex). El sidebar puede colapsar (`.side.collapsed`).
- Rejillas responsivas: `.gauto` = `repeat(auto-fit, minmax(250px,1fr))`.
- Estadísticas usa layout de 2 columnas (contenido + panel lateral de Ideas); **por debajo de 1100px** el panel de Ideas pasa a apilarse (`@media(max-width:1100px)`).
- Otros breakpoints en el CSS: `860px` (planes/onboarding), `820px`, `720px`, `640px` (selector de tipo a ancho completo).
- Diseñado para escritorio primero (es un panel de gestión); usable en tablet.

## Restricciones

- Sin backend ni datos reales (todo mock e intencional).
- Inter está empaquetada localmente (`assets/fonts/inter-latin-variable.woff2`); no depende del CDN de Google Fonts.
- Pensado para navegadores modernos.

## Problemas conocidos (KNOWN ISSUES)

- Los **datos de las listas de detalle** (drill-down) son de ejemplo curado, no derivan de un único dataset central.
- No hay pruebas automatizadas.

## Deuda técnica

- Todo el JS vive inline en `index.html` (archivo grande). Para producción convendría separar en módulos y/o migrar a un framework por vistas.
- La data mock está dispersa en varias IIFEs; convendría centralizar un "store".
- El sistema de diseño está en un solo CSS grande; se podría tokenizar (CSS vars ya existen) y/o portar a Tailwind.

## Próximos pasos sugeridos

1. Conectar los indicadores y listas a datos reales (definir el modelo de datos: cliente, escaneo, tarjeta, membresía, local, scanner).
2. Backend + auth reales.
3. Pruebas automatizadas (hoy no hay ninguna).
4. (Opcional) Migración por vistas a React/Vue conservando el sistema de diseño.

---

## Sistema de diseño

Los valores viven en `:root` de `assets/styles.css` (primeras ~28 líneas) salvo que se indique otra cosa.

> Existe además un **design system formal** (Foundations, Components, Patterns, Rules) publicado como canvas de Claude Design a partir de este CSS/HTML: https://claude.ai/code/artifact/ac42707f-883d-4091-bf54-fd7586c41f13 — si el enlace ya no es accesible, se puede regenerar siguiendo esta misma sección + `CLAUDE.md`.

### Colores (HEX)

| Token | HEX | Uso |
|---|---|---|
| `--bg` | `#ffffff` | Fondo general |
| `--side` | `#f7f5f3` | Fondo del sidebar |
| `--ink` | `#0b1220` | Texto fuerte / números |
| `--slate` | `#334155` | Títulos de tarjeta / KPI |
| `--slate-2` | `#475569` | Texto secundario |
| `--muted` | `#646975` | Labels / secciones |
| `--muted-2` | `#64748b` | Texto atenuado |
| `--muted-3` | `#94a3b8` | Texto muy atenuado |
| `--line` | `#f1f3f7` | Líneas de filas |
| `--line-card` | `#ebedf2` | Borde de tarjeta |
| `--line-head` | `#e6e8eb` | Borde de header |
| `--blue` | `#2743d6` | Acento principal |
| `--blue-ink` | `#1e2f9e` | Azul hover/fuerte |
| `--blue-soft` | `#6f80e2` | Azul suave |
| `--blue-active` | `#f2f5fc` | Fondo botón de rango activo |
| `--green` | `#1f9d42` | Positivo |
| `--red` | `#d92121` | Negativo / riesgo |
| `--orange` | `#e57614` | Advertencia suave |
| `--violet` | `#6a4fcc` | Acento secundario |
| `--teal` | `#0c8478` | Acento secundario |

Colores de acento adicionales usados en componentes (no en `:root`): tipos de cliente en Estadísticas (`#9fb0f2` casual, `#5e74ec` habitual, `#2f40d6` VIP); membresías/naranja de marca `#b8651e`–`#d98a2b`; badges de plan Starter (degradado `#3d51e0→#6a3fd0`) y Growth (`#b8651e→#d98a2b`).

### Tipografía

- Familia: **Inter** (Google Fonts), fallback `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`.
- Pesos cargados: 400, 500, 600, 700, 800. **Regla del proyecto:** nada usa 700+ como "bold" visual; el "bold" de la UI es **600 (semibold)**.
- Base body: `font-size:13.5px`, `line-height:1.45`, `letter-spacing:-0.005em`, antialias activado.
- Escala aproximada: números KPI ~26px; bignum de detalle ~32px; `h1` de topbar; títulos de tarjeta `h4` ~14px; labels ~11–12.5px; `sec-label` en mayúsculas con tracking.

### Espaciado

- Rejillas: `gap:16px` base (`.grid`). Padding de tarjeta típico 20–24px.
- Layout de Estadísticas: `gap:20px` entre contenido y panel lateral; `16px` entre filas de tarjetas dentro de una pestaña (`.kgroup .grid + .grid{margin-top:16px}`).

### Border radius

- `--radius: 16px` (tarjetas). Botones/píldoras: 8–11px o `9999px` (pills/badges). Chips de tipo: `9999px`.

### Sombras

- `--shadow: 0 1px 3px rgba(0,0,0,.10), 0 1px 2px -1px rgba(0,0,0,.10)`.
- Sombras más marcadas en hover de tarjetas de opción y modales (ver reglas puntuales).

### Grid / ancho

- `--maxw: 1032px` (ancho de contenido de referencia). App = sidebar + main con `.wrap` de padding `20px 24px 64px`.
- Clases de rejilla: `.g4` (4 col), `.g3` (3 col), `.g2` (2 col), `.gauto` (`auto-fit minmax(250px,1fr)`).

### Breakpoints

`1100px` (panel de Ideas se apila), `860px` (planes/onboarding), `820px`, `720px`, `640px` (selector de tipo full-width).

### Componentes, variantes y estados

Ver `CLAUDE.md` → "Convenciones de componentes". Resumen: `.card`/`.kpi`/`.chart-card`/`.vcard`; `.pill` (`.hab`/`.cas`/`.risk`); `.badge` (`.alerta`/`.oport`/`.logro`/`.tip`/`.tend`); `.kindseg` (tabs, estado `.on`); `.lockpanel`/`.lockrow`/`.up-badge` (`.starter`/`.growth`); `.js-detail`+`.card-detail` (estado `.expanded`, `.is-open`); modales `.modal-overlay`/`.modal` (`.open`); `.in-toast` (`.show`).

### Iconografía

SVG inline con `stroke="currentColor"`, `stroke-width` ~1.7–1.8, `stroke-linecap/linejoin="round"`. Sin librería externa. Algunos íconos decorativos usan `data:` URIs en el CSS (checks de listas, chevrons de select).

### Reglas responsive

Escritorio primero; el panel de gestión colapsa el sidebar y apila el panel lateral de Ideas en pantallas < 1100px. Rejillas fluidas con `auto-fit`.

---

## Mapeo indicador → plan (según el Excel de referencia del cliente)

Base para el gating de Estadísticas. **No cambiar sin revisar el Excel.**

- **FREE:** Tarjetas entregadas, Clientes activos (30 días), Frecuencia de visita, Clientes nuevos vs. que vuelven, Premios y canjes (canjes + tasa de canje), Altas menos bajas de tarjetas, Membresías activas, Nuevas membresías, Membresías vencidas sin renovar.
- **STARTER:** Retención ("clientes que vuelven"), Clientes que se están alejando, Días y horas con más visitas, Tipos de cliente (Casual/Habitual/VIP), **Edad y sexo** (demográficos — el Excel los ubica en Starter, no en Growth), Top 10 clientes (también replicado como tabla "Top clientes por frecuencia" en Inicio, indicador #25), Cerca del premio (1–2 sellos), Renovación de membresías, Uso de la membresía (sin uso 30d + frecuencia), Niveles de membresía (distribución/ascensos/cerca de subir), De sellos a membresía (conversión + tiempo de vida).
- **GROWTH:** **Todo el módulo "Locales"** (nav, crear/editar/ver local — no solo las comparativas: el Excel clasifica la sección completa como "MULTI-LOCAL — EXCLUSIVO GROWTH", indicadores #43–48), Comparar tus locales, Clientes de varios locales / embajadores, Perfiles avanzados (horario, patrón, velocidad de ciclo, ascendente), Dónde viven tus clientes (distrito), Ventas de tus socios / POS (gasto, ingreso atribuible, productos, frecuencia compra vs escaneo — **requiere integración de caja, V2**).

Las listas de la vista **Planes** están alineadas con este mapeo.

---

## ESTADO ACTUAL

### DONE
- Estadísticas con 3 pestañas (General/Sellos/Membresías), gating por plan y revelado real al mejorar.
- Detalle inline (drill-down) dentro de las tarjetas, sin modales.
- Panel lateral "Ideas para hoy" consistente en las 3 pestañas.
- Paneles "también disponible en planes superiores" por pestaña, sin CTA de Ver planes.
- Estado de plan persistente (`w2_plan`) que actualiza Estadísticas, Configuración y Planes.
- App Scanner con cuentas de staff en tabla + insight minimizable.
- Reglas de perfil de cliente configurables y persistentes.
- Facturación integrada en Configuración; nombres de plan unificados Free/Starter/Growth.
- **Design system formalizado** en un canvas de Claude Design (Foundations/Components/Patterns/Rules), extraído fielmente de `assets/styles.css`/`index.html` — ver enlace en la sección "Sistema de diseño" más abajo.
- **Mis tarjetas:** mini-preview sin progreso simulado (regla fija "N sellos → premio" en vez de "X/10"), dato real de clientes/socios por tarjeta (`.lc-stat`), alturas parejas entre tarjetas, toggle grilla/lista (`w2_cards_view`) reutilizando `.scanner-table`.
- **Drawer de cliente** (`#cliDrawer`) en dos columnas (900px): billetera a la izquierda, datos/historial a la derecha.
- **Componentes que antes eran oscuros sin token** ("Ideas para hoy", toast, botón Starter, App Scanner, wallet toggles) unificados al sistema claro; `.si-badge` reutiliza los mismos tintes que `.badge`.
- Colores casi-duplicados consolidados a los tokens `--green`/`--blue` (dots de estado, iconos de check, CTA del hero) — se conservan aparte los colores que son **elección de marca del gestor** (paleta de la tarjeta/local), que no son duplicados sino opciones reales.
- Inter empaquetada localmente (`assets/fonts/`), sin dependencia del CDN.
- **Locales gateado a Growth**: nav oculto en Free/Starter; `go()` intercepta accesos directos a `locales`/`crearLocal`/`localDetail` y redirige a Planes con toast.
- **Sistema de Insights auditado contra el Excel**: 9 insights en 6 pantallas, copy/umbral corregido donde no coincidía con su ficha, gateados por plan (`.ins-gate` + `data-tier`, misma escala que Estadísticas). Ver sección "Sistema de Insights".
- **Top clientes por frecuencia** nuevo en Inicio, gateado a Starter, conectado a `window.__clients` (mismo array que "Mis clientes") y reactivo a las reglas de perfil vía `window.__refreshTopClientesHome`.
- **Canal de notificaciones reducido a WhatsApp + SMS** (se quitó Email de `#bdayChannel` y `#ntChannel`, junto con el campo "Asunto" que solo aplicaba a Email) y **scanner con Local obligatorio** (se quitó "Todos los locales" de `#scmLocal`) — corrige las 2 discrepancias detectadas al auditar `PRD-plataforma-fidelizacion.md` contra el código real.

### IN PROGRESS
- Ajustes finos de contenido/copy de indicadores según feedback iterativo.
- **Brecha con `PRD-plataforma-fidelizacion.md`**: fuera de las 2 correcciones ya aplicadas, ese PRD describe funcionalidad que todavía no existe en el código — Flow builder por lenguaje natural (§4.4), feed "Está pasando ahora" (§4.2), pantalla dedicada de Escaneos (§4.3-bis), pantalla de Staff/Permisos multi-local (§4.9), catálogo de tarjeta ampliado (cupones/gift cards/descuentos, §4.3), categorización de insights en accionable/sugerencia/dato-curioso (§4.7). No implementar nada de esto sin que el usuario indique por dónde seguir.

### TODO
- Conectar a datos reales (modelo de datos + backend + auth).
- Pruebas automatizadas.
- (Opcional) Migración por vistas a un framework.
- Ver ítems "Pendiente de resolver" de `PRD-plataforma-fidelizacion.md` §9 (carga de saldo en gift cards, viabilidad de WhatsApp Business API, consentimiento de datos en el formulario de registro, facturación del propio SaaS, comportamiento al bajar de plan con Locales/staff huérfanos) — decisiones del usuario, no asumir una por defecto.

### KNOWN ISSUES
- Datos de drill-down son mock curado, no derivan de un dataset único.
- Sin tests.

### DO NOT CHANGE WITHOUT REVIEW
- Tokens de `:root` en `assets/styles.css` (colores, `--radius`, `--shadow`).
- Función global `go()` y contrato `data-view` / `.view.active`.
- Claves de `localStorage`: `w2_plan`, `w2_prof_rules`, `w2_scan_insight_min`.
- `window.__setPlan` y el gating por `data-tier` (free/starter/growth).
- El mapeo indicador → plan de la sección anterior (deriva del Excel del cliente).
- Lenguaje llano de la UI (no reintroducir jerga).
- `.ins-gate` + `data-tier` en bloques de insight (9 instancias, ver "Sistema de Insights") y el `NAV_GATE` de `locales`/`crearLocal`/`localDetail` a Growth — ambos vivos en la misma IIFE ("Gating por plan fuera de Estadísticas").
- `window.__clients` (referencia al array de "Mis clientes", no copiar) y `window.__refreshTopClientesHome` (hook que el motor de reglas de perfil llama en `applyToClients`) — la tabla de Inicio depende de ambos.
- Los umbrales/copy de las 9 fichas de insight documentadas: derivan de `Dashboard_Final_v15.xlsx` → hoja "Insights del Gestor". No cambiar sin revisar esa hoja.
- Canales de notificación limitados a WhatsApp/SMS (`#bdayChannel`, `#ntChannel`) por decisión de `PRD-plataforma-fidelizacion.md` §9 — no reintroducir Email ni el campo "Asunto".
- `#scmLocal` sin opción "Todos los locales" — cada scanner debe tener un Local específico para que las comparativas por sede de Growth sean confiables.

---

## Historial de decisiones (resumen con alternativas descartadas)

| Decisión | Motivo | Alternativas descartadas | Qué evitar cambiar sin razón |
|---|---|---|---|
| Estadísticas en 3 pestañas por tipo | Ordenar indicadores mezclados; el usuario pidió un selector | Pestaña "Todos" (descartada por redundante); dropdown "Tarjeta" (reemplazado) | No reintroducir "Todos" ni mezclar tipos |
| Drill-down inline en la tarjeta | Petición explícita de "vista interna"; rechazo a modales | Modal de detalle (implementado y luego retirado) | No volver a modales para el detalle |
| Ideas en panel lateral derecho | Se repetían por pestaña (redundante) | Repetir bloque en cada pestaña | Mantener un único panel consistente |
| Gating por plan con paneles informativos | ~15 tarjetas borrosas saturaban al usuario Free | Muro de tarjetas bloqueadas/borrosas | No volver al muro borroso |
| Demográficos (edad/sexo) en Starter | El Excel los ubica en Starter | Tenerlos en Growth (era incongruente) | No moverlos a Growth |
| Facturación dentro de Configuración | El usuario la sacó del menú; upgrade ya en sidebar | Item de menú "Facturación" | No devolverla al menú lateral |
| App Scanner = cuentas usuario/contraseña en tabla | Modelo de staff pedido por el usuario; formato lista | Dispositivos por código de vinculación; cards | Mantener tabla y credenciales |
| Reglas de perfil configurables | Coherencia del dato "Perfil" en toda la app | Perfiles fijos por umbrales hardcodeados | No hardcodear los umbrales |
| Nombres de plan Free/Starter/Growth | Unificar con el lenguaje del usuario y el Excel | Free/Pro/Business (inconsistente) | No renombrar los planes |
| Componentes oscuros pasados a claro (Ideas para hoy, toast, botón Starter, etc.) | No tenían token propio y rompían la coherencia con el resto de la app (100% clara) | Formalizar un token "superficie oscura" y dejarlos así | No reintroducir fondos oscuros sin token en componentes nuevos |
| Mini-preview de "Mis tarjetas" sin progreso simulado | El progreso (ej. "6/10 sellos") daba la impresión de ser el avance personal del gestor, no la estructura del programa | Etiqueta "Vista previa" manteniendo el progreso de ejemplo | No mostrar sellos/usos rellenos en la grilla de administración; el progreso real de un cliente vive en su propia tarjeta/drawer |
| Drawer de cliente en 2 columnas (900px) | A 490px mezclaba billetera + datos en una sola columna larga | Mantener una columna angosta | No volver a una sola columna en desktop |
| Inter empaquetada localmente | Quitar la dependencia de red del CDN de Google Fonts | Mantener el `<link>` a fonts.googleapis.com | No reintroducir el `<link>` al CDN sin acordarlo |
| Módulo "Locales" gateado a Growth | El Excel clasifica todo multi-local como exclusivo Growth (indicadores #43–48); el módulo existía sin restricción | Gatear solo las comparativas y dejar el CRUD de locales libre | No abrir Locales a Free/Starter sin revisar el Excel |
| Insights corregidos y gateados por plan | Umbrales/copy no coincidían con sus fichas del Excel (7pts vs. >10pts, rango "1-2" vs. "exactamente 1", 30 días vs. 21 días de la ficha); ninguno tenía gating | Dejar el copy como estaba y solo gatear; o corregir copy sin gating | No cambiar un umbral de insight sin confirmarlo contra la hoja "Insights del Gestor" |
| "Top clientes por frecuencia" construido en Inicio | La documentación describía una tabla que no existía en el código; se decidió construirla en vez de solo corregir la doc | Solo corregir el known issue en la documentación | No duplicar el dataset de clientes — debe leer `window.__clients` |
| Canal de notificaciones reducido a WhatsApp + SMS | `PRD-plataforma-fidelizacion.md` (sección 9) decide "WhatsApp y SMS" como únicos canales; el código tenía Email como canal (con "Asunto" propio) en `#bdayChannel` y `#ntChannel` | Mantener Email como tercer canal | No reintroducir el botón "Email" ni el campo "Asunto" (`#ntSubjectWrap`) en los selectores de canal |
| Scanner con Local obligatorio (sin "Todos los locales") | El PRD maestro requiere que cada scanner esté atado a un Local específico para que las comparativas por sede de Growth sean confiables; `#scmLocal` tenía la opción "Todos los locales" | Dejar la opción "Todos los locales" para scanners multi-sede | No volver a agregar una opción "todos los locales" a `#scmLocal`; cada scanner nuevo debe quedar asignado a Miraflores/San Isidro/Barranco (o el Local real que corresponda) |
