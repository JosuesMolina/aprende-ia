# Informe de validación UX y accesibilidad

**Fecha:** 2026-09-15  
**Alcance:** implementación local de `index.html`, `css/style.css`, `js/app.js` y activos locales.  
**Método:** revisión estática y cálculo WCAG con sRGB. No se modificó el producto ni se hizo commit/push.

## Resultado ejecutivo

Hay dos bloqueantes para declarar el MVP listo:

1. **El progreso por módulo nunca se completa.** `state.completed` se consulta en la portada, pero no hay ninguna instrucción que agregue `ia-basica` (ni `problemas`) a ese arreglo. Tras completar quiz y misión se ganan 220 XP, pero la portada permanece en `0 de 7`, `0%` y el módulo sigue como “Disponible”. Esto rompe la funcionalidad principal de progreso/“módulos completados”.
2. **El botón principal falla WCAG AA en modo oscuro.** En la media query oscura, `--color-action` se cambia a `#64A8FF` pero `.button` conserva texto blanco. El contraste real es **2.45:1**, inferior a 4.5:1 para texto normal. Además, la barra activa contra su pista conserva un contraste de **1.95:1**, inferior a 3:1 para componente no textual.

## Evidencia de automatización

### Comandos ejecutados

```powershell
node --version
node --check js/app.js
npm list -g --depth=0
Test-Path node_modules\axe-core
Test-Path node_modules\playwright
Test-Path node_modules\puppeteer
```

### Datos crudos

```text
Node: v22.14.0
node --check js/app.js: exit 0 (sin salida; sintaxis válida)
npm global: (empty)
node_modules/axe-core: False
node_modules/playwright: False
node_modules/puppeteer: False
Binarios de navegador detectados por Get-Command: ninguno (sólo npx/npm)
```

### axe/Lighthouse: limitación concreta

No fue posible ejecutar un análisis real de axe-core ni Lighthouse: el repositorio no contiene esas dependencias, no hay navegador automatizable detectado y el entorno no permite instalar dependencias/red para este informe. Por tanto, **no hay resultados “antes/después” de axe o Lighthouse** y no se debe presentar este informe como sustituto de una corrida en navegador.

Para cerrar esta limitación cuando haya un navegador disponible, ejecutar contra una vista servida localmente (y repetir en tema claro/oscuro, 320/768/1280 px):

```powershell
npx --yes serve .
npx --yes lighthouse http://localhost:3000 --only-categories=accessibility --output=json --output-path=docs/lighthouse-accessibility.json
npx --yes @axe-core/cli http://localhost:3000 --save docs/axe.json
```

## Contraste calculado (WCAG)

Fórmula: luminancia relativa sRGB y `(L1 + .05) / (L2 + .05)`. Los valores son ratios sin redondear para la decisión; se muestran a dos decimales.

| Contexto CSS efectivo | Ratio | Umbral | Resultado |
| --- | ---: | ---: | --- |
| `#1D1D1F` texto / `#FFF` superficie | 16.83:1 | 4.5:1 | Pasa |
| `#424245` texto secundario / `#FFF` | 10.01:1 | 4.5:1 | Pasa |
| `#0071E3` acción / blanco (botón claro) | 4.70:1 | 4.5:1 | Pasa |
| `#107C41` éxito / `#EAF6EE` | 4.75:1 | 4.5:1 | Pasa |
| `#C5221F` error / `#FDECEC` | 5.08:1 | 4.5:1 | Pasa |
| `#F5F5F7` texto / `#152238` superficie oscura | 14.64:1 | 4.5:1 | Pasa |
| `#C7C7CC` texto secundario / `#152238` | 9.46:1 | 4.5:1 | Pasa |
| `#64A8FF` acción / `#152238` | 6.52:1 | 4.5:1 | Pasa |
| **blanco (texto `.button`) / `#64A8FF` (botón oscuro)** | **2.45:1** | **4.5:1** | **Falla / bloqueante** |
| `#0B1320` / `#64A8FF` (alternativa prevista en tokens) | 7.61:1 | 4.5:1 | Pasa |
| `#0071E3` barra / `#E5E5EA` pista clara | 3.74:1 | 3:1 | Pasa |
| **`#64A8FF` barra / `#E5E5EA` pista heredada en oscuro** | **1.95:1** | **3:1** | **Falla** |
| `#D2D2D7` borde / blanco | 1.51:1 | 3:1 no textual | Falla para borde que identifica controles |
| `#34445D` borde / `#152238` | 1.62:1 | 3:1 no textual | Falla para borde que identifica controles |

Los últimos dos bordes afectan a campos, opciones de quiz y botones secundarios cuando el borde es la única delimitación visible frente a una superficie idéntica.

## Checklist UX (Nielsen) y accesibilidad manual

| Criterio | Estado | Evidencia / hallazgo |
| --- | --- | --- |
| Visibilidad del estado | Parcial | XP y feedback aparecen visualmente; falta anuncio `aria-live` y la portada no refleja módulo terminado. |
| Correspondencia con el mundo real | Pasa | Lenguaje claro, tareas de oficina y ejemplos entendibles. |
| Control y libertad | Parcial | Hay “Ruta del curso” y reinicio con confirmación; no hay navegación de historial/URL por pantalla ni deshacer un reinicio. |
| Consistencia | Parcial | Botones/estados son consistentes, pero módulo 05 denomina “Misión práctica” a contenido sin campo, evaluación ni finalización. |
| Prevención de errores | Parcial | Confirmación antes de reset; el parseo de `localStorage` no está protegido y un valor corrupto puede impedir que cargue toda la app. |
| Reconocer antes que recordar | Pasa | Criterios del prompt y ejemplos se mantienen visibles. |
| Flexibilidad/eficiencia | Parcial | Copiar y abrir ChatGPT reducen pasos; no hay atajos ni rutas enlazables para llegar a una pantalla concreta. |
| Diseño minimalista | Pasa | Jerarquía, tarjetas y contenido focalizado; las cápsulas/emoji no sustituyen las instrucciones. |
| Recuperación de errores | Parcial | Hay mensajes para copiar y prompt vacío; no hay recuperación del estado local corrupto. |
| Ayuda/documentación | Parcial | Instrucciones in situ buenas; no hay aviso breve de qué se almacena localmente ni de límites de persistencia por dispositivo/navegador. |
| Idioma y estructura | Pasa | `lang="es"`, `main`, títulos y botones nativos. |
| Teclado/foco | Parcial | Los controles nativos son alcanzables y hay `:focus-visible`; el contraste del foco en claros parece suficiente, pero requiere prueba real de tabulación. |
| Lectores de pantalla | Falla parcial | Actualizaciones dinámicas (quiz, XP, criterios, copiado) no tienen región `aria-live`; no se anunciarán de forma fiable. |
| Movimiento | Pasa | Hay reducción de movimiento que elimina transiciones. |
| Objetivos táctiles | Parcial | `.button` define 44 px; `.quiz-option` tiene padding pero no una altura mínima explícita. Normalmente será suficiente con 16 px/1.55 + 28 px, pero necesita comprobación visual real. |

## Verificación funcional estática

- **Sintaxis JS:** pasa (`node --check`).
- **Quiz:** botones nativos; al responder se deshabilitan, se marca correcto/incorrecto y se muestra explicación. La respuesta correcta concede 100 XP una sola vez mediante `awards.quiz`.
- **Misión módulo 01:** textarea, criterios, evaluación y copia funcionan en el flujo fuente; la misión concede 120 XP una vez si detecta 3 criterios. El contenido inyectado desde el textarea se escapa antes de re-renderizar.
- **Persistencia:** usa la clave `aprendeIA.progress` en `localStorage` para XP, insignias, texto y última pantalla. No hay backend ni claves API.
- **Bloqueante de progreso:** no existe `state.completed.push(...)`, ni una función equivalente. `completedCount()`, porcentaje, estado de tarjeta e insignias de portada nunca cambian por completar una misión.
- **Riesgo de estabilidad:** `JSON.parse(localStorage.getItem(...))` no tiene `try/catch`; un valor manualmente corrupto deja de cargar el script. `exerciseText` tampoco se valida como cadena antes de usar `.replace`.
- **Navegación:** “Continuar” siempre abre módulo 01; no el siguiente disponible. `#modulo-1` sólo se lee al cargar, pero las acciones no actualizan el hash. Módulo 05 se abre con su tarjeta y el botón volver funciona.
- **Botones externos:** enlaces a ChatGPT usan `target="_blank" rel="noopener"` (correcto); los botones de copia tienen fallback visible si Clipboard API falla.
- **Imágenes:** las tres imágenes tienen `alt`, `width` y `height`, evitando saltos de layout. Sus textos alternativos describen grupos, no cada producto; sumados al texto inmediatamente adyacente pueden ser repetitivos para lector de pantalla.

## Activos, iconos y licencias

Evidencia revisada: `assets/icons/{app-ai,app-google,app-work}.svg`, `assets/icons/README.md`, `assets/brands/README.md`, `docs/licenses-icons.md`.

- No hay logos de terceros ni recursos remotos incluidos: los tres SVG locales son pictogramas abstractos y la documentación declara que son originales y **CC0-1.0**.
- No se detectó una copia visual directa de ChatGPT, Claude, Gemini, Google, Slack, Notion, Asana o Canva en los activos revisados. Esto reduce el riesgo de marca.
- La declaración CC0 vive en documentación; no hay archivo de licencia CC0 de texto completo ni autor/titular identificado. Para una distribución formal, añadir `LICENSE` con el texto CC0-1.0 y atribuir/identificar la cesión del creador.
- `app-google.svg` usa un rectángulo verde con una “E” blanca. Aunque se documenta como abstracto, su nombre y alt lo vinculan a Google Workspace; mantenerlo como grupo genérico o reemplazarlo por una marca oficialmente aprobada con su guía de uso registrada antes de presentarlo como logo de Google.
- La petición de usar iconos de aplicaciones **no está cubierta literalmente**: se muestran sólo tres pictogramas de grupos y una línea de texto; no hay iconos individuales de ChatGPT, Claude, Gemini, Gmail, Drive, etc. Si se incorporan logos reales, registrar cada origen, guía de marca y permiso de uso en `assets/brands/README.md`.

## Prioridad de corrección

1. **P0:** al completar ambas actividades, registrar el módulo una sola vez en `state.completed`, guardar, y actualizar portada/progreso/estado. Definir la condición de finalización del módulo 05.
2. **P0:** en modo oscuro usar texto `#0B1320` en `.button` de acción y ajustar pista/barra de progreso para obtener ≥3:1; probar ambos temas.
3. **P1:** elevar contraste de los bordes que delimitan input, quiz y botón secundario a ≥3:1 contra su fondo.
4. **P1:** añadir regiones `aria-live="polite"` para feedback de quiz, misión, copiado y XP; anunciar criterios de forma no dependiente de color.
5. **P1:** envolver lectura/escritura de `localStorage` en recuperación segura y validar el esquema mínimo.
6. **P2:** convertir la misión de módulo 05 en una actividad evaluable o renombrarla como ejemplo; añadir ruta/hash para pantallas y una explicación de datos locales.
7. **P2:** decidir entre iconos abstractos de categorías (y alt decorativo cuando el texto ya identifica) o logos oficiales individuales con autorización/documentación.

