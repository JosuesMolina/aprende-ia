# Aprende IA

MVP estático para GitHub Pages de un curso práctico de inteligencia artificial para principiantes.

## Ejecutar y publicar

No requiere instalación ni backend. Abre `index.html` en un navegador o activa **Settings → Pages → Deploy from a branch → main / (root)** en GitHub.

## Arquitectura

- `index.html`: punto de entrada estático y accesible.
- `css/style.css`: interfaz responsive sin framework.
- `js/app.js`: datos de módulos, navegación, XP, quizzes y persistencia con `localStorage`.

Para añadir una lección, agrega su metadato al arreglo `modules` y crea su vista/actividades en `app.js` (en una iteración posterior puede separarse cada módulo en su propio archivo).
