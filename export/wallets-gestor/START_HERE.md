# START HERE — Prompt inicial para Claude Code

Copia y pega el siguiente mensaje a Claude Code la primera vez que abras este proyecto.
No dejes que empiece a cambiar código todavía: primero debe entender el proyecto.

---

**Prompt para pegar en Claude Code:**

> Antes de tocar nada, sigue estos pasos en orden y detente al final a esperar mis instrucciones:
>
> 1. Lee `CLAUDE.md` completo.
> 2. Lee `PROJECT_CONTEXT.md` completo.
> 3. Lee `README.md` completo.
> 4. Inspecciona la estructura completa del proyecto (lista de archivos y carpetas). El código de la app vive casi todo en `index.html` (HTML + CSS en `assets/styles.css` + JavaScript inline dentro de `index.html`).
> 5. **No modifiques código todavía.**
> 6. Verifica que entiendes la arquitectura: es un prototipo de una sola página (SPA sin framework) donde las "vistas" se muestran/ocultan con la función global `go(nombreDeVista)` y el estado se guarda en `localStorage`.
> 7. Ejecuta el proyecto si es posible: `npm install` y luego `npm run dev`. Abre la URL que imprime Vite.
> 8. Comprueba que el estado real del código coincide con lo descrito en `PROJECT_CONTEXT.md` (secciones DONE / IN PROGRESS / TODO / KNOWN ISSUES).
> 9. Dime qué encontraste: resumen de la arquitectura, lista de archivos que consideras relevantes, y cualquier diferencia entre la documentación y el código real.
> 10. **Espera mis siguientes instrucciones antes de hacer cambios importantes.** Respeta especialmente la sección "DO NOT CHANGE WITHOUT REVIEW" de `PROJECT_CONTEXT.md`.

---

## Nota sobre migración a framework

Este proyecto es HTML/CSS/JS vanilla (sin React/Vue). Si tu objetivo es migrarlo a React/Next/Vue, **no lo hagas automáticamente**: primero acuerda conmigo el plan de migración. La app funciona hoy tal cual y toda la lógica está en `index.html`.
