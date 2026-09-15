# Aprende IA — Design tokens v1

**Estado:** especificación de diseño, sin implementación.  
**Objetivo:** una interfaz educativa clara, táctil y tranquila, con una lectura rápida para personas principiantes y suficiente densidad para el uso de oficina.

## Principios y decisión de apariencia

Se adopta una apariencia **adaptativa**: clara por defecto (`light`), con variante oscura automática cuando el sistema solicita `prefers-color-scheme: dark`; en una iteración posterior se podrá exponer un selector manual. La superficie clara es la prioridad de enseñanza porque facilita lectura prolongada, impresión/capturas y los estados de tarjetas; la oscura no será un mero “invertir colores”, sino una variante con superficies elevadas y contrastes verificados.

La decisión sigue la recomendación de Apple de adoptar la apariencia del sistema y usar colores semánticos/adaptativos, en vez de fijar colores por contexto. La jerarquía se apoya en tipografía, espacios y elevación, no sólo en color; así los estados correcto/incorrecto siguen siendo comprensibles para personas con daltonismo.

**Referencias de criterio**

- Apple HIG: [Color](https://developer.apple.com/design/human-interface-guidelines/color), [Typography](https://developer.apple.com/design/human-interface-guidelines/typography), [Layout](https://developer.apple.com/design/human-interface-guidelines/layout), [Icons](https://developer.apple.com/design/human-interface-guidelines/icons).
- WCAG 2.2 AA: [1.4.3 Contraste mínimo](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html) exige 4.5:1 para texto normal y 3:1 para texto grande; [1.4.11 Contraste no textual](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html) cubre controles e indicadores; [2.5.8 Tamaño mínimo del objetivo](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html) establece 24 × 24 CSS px como mínimo AA.
- Nielsen Norman Group: [Dark Mode: How Users Think About It and Issues to Avoid](https://www.nngroup.com/articles/dark-mode-users/) — el modo oscuro no sustituye una interfaz legible ni debe ser la única apariencia.

Los ratios indicados se calcularon sobre colores sólidos sRGB, con la fórmula de luminancia de WCAG; no se redondean para declarar conformidad (se muestran redondeados a dos decimales).

## Color

### Tokens semánticos — claro

| Token | Hex | Uso | Contraste verificado |
|---|---:|---|---:|
| `--color-bg` | `#F5F5F7` | Fondo de aplicación | `--color-text`: **15.46:1** |
| `--color-surface` | `#FFFFFF` | Tarjetas, menús, campos | `--color-text`: **16.46:1** |
| `--color-text` | `#1D1D1F` | Títulos y texto principal | sobre superficie: **16.46:1** |
| `--color-text-secondary` | `#424245` | Metadatos, apoyo, etiquetas | sobre superficie: **10.01:1** |
| `--color-action` | `#0071E3` | CTA, enlace, progreso activo | blanco sobre acción: **4.70:1** |
| `--color-success` | `#107C41` | Correcto/completado | blanco sobre éxito: **5.27:1** |
| `--color-warning` | `#8A4B00` | Atención/revisión | blanco sobre aviso: **6.80:1** |
| `--color-danger` | `#C5221F` | Error/no guardado | blanco sobre error: **5.80:1** |
| `--color-info-soft` | `#E8F2FF` | Fondo de información | texto principal: **14.89:1** |
| `--color-success-soft` | `#EAF6EE` | Fondo de éxito | texto principal: **15.16:1** |
| `--color-warning-soft` | `#FFF4E5` | Fondo de aviso | texto principal: **15.49:1** |
| `--color-danger-soft` | `#FDECEC` | Fondo de error | texto principal: **14.73:1** |

No usar `--color-action` para texto pequeño sobre blanco: su ratio es 4.70:1 y cumple AA, pero el texto de cuerpo debe preferir `--color-text` por legibilidad. Todo estado se expresa por **icono + etiqueta + color**, nunca por color solo.

### Tokens semánticos — oscuro

| Token | Hex | Uso | Contraste contra `--color-bg` `#0B1320` |
|---|---:|---|---:|
| `--color-bg` | `#0B1320` | Fondo de aplicación | — |
| `--color-surface` | `#152238` | Tarjeta elevada | — |
| `--color-text` | `#F5F5F7` | Texto principal | **18.61:1** |
| `--color-text-secondary` | `#C7C7CC` | Texto de apoyo | **11.05:1** |
| `--color-action` | `#64A8FF` | Acción, foco, enlace | **7.61:1** |
| `--color-success` | `#69DB9A` | Éxito | **10.81:1** |
| `--color-warning` | `#FFC46B` | Aviso | **11.84:1** |
| `--color-danger` | `#FF8B87` | Error | **8.24:1** |

Los botones de fondo claro en modo oscuro deben usar texto `#0B1320`, no blanco. Revisar cada combinación final cuando intervengan transparencias, degradados o imágenes.

## Tipografía

```css
--font-sans: -apple-system, BlinkMacSystemFont, "SF Pro Text", "SF Pro Display",
  "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
--font-mono: ui-monospace, "SFMono-Regular", Menlo, Consolas, monospace;
```

| Rol | Tamaño / línea | Peso | Uso |
|---|---|---:|---|
| `display` | `clamp(2rem, 6vw, 3.5rem) / 1.08` | 700 | Hero “Aprende IA” |
| `h1` | `2rem / 1.15` | 700 | Título de vista/módulo |
| `h2` | `1.5rem / 1.25` | 700 | Sección |
| `h3` | `1.125rem / 1.35` | 600 | Tarjeta/actividad |
| `body` | `1rem / 1.55` | 400 | Lectura base; nunca menor a 16 px |
| `body-sm` | `0.875rem / 1.45` | 400 | Ayuda y metadatos; contraste AA |
| `label` | `0.8125rem / 1.25` | 600 | Etiquetas y progreso |

Usar `rem`, permitir el zoom del navegador y no usar texto rasterizado. Reservar mayúsculas para etiquetas breves; no para párrafos ni instrucciones.

## Espaciado y grid

**Escala 4/8:** `4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96` px. Todo padding, gap y margen toma uno de estos valores.

| Contexto | Regla |
|---|---|
| Teléfono | 4 columnas; margen lateral 16 px; gutter 16 px; contenido a ancho completo |
| Tableta | 8 columnas; margen 24 px; gutter 20 px |
| Escritorio | 12 columnas; max-width 1200 px; margen 32 px; gutter 24 px |
| Lectura | max-width 720 px; evita líneas excesivamente largas |
| Tarjeta | padding 20 px móvil / 24 px desde tableta; `gap: 16px` |
| Objetivo táctil | 44 × 44 px preferido (alineado con Apple); nunca menor de 24 × 24 px WCAG AA, incluyendo separación efectiva |

## Forma, bordes y elevación

| Token | Valor | Aplicación |
|---|---|---|
| `--radius-sm` | 8 px | Chips, badges, controles pequeños |
| `--radius-md` | 12 px | Campos, botones, tarjetas compactas |
| `--radius-lg` | 20 px | Tarjetas de módulo, paneles de resultados |
| `--radius-pill` | 999 px | Progreso, etiquetas de XP |
| `--border-subtle` | 1 px `rgba(29,29,31,.12)` | Delimitación silenciosa en claro |
| `--shadow-1` | `0 1px 2px rgba(15,23,42,.08)` | Controles sobre superficie |
| `--shadow-2` | `0 8px 24px rgba(15,23,42,.12)` | Tarjeta en hover / diálogo no modal |
| `--shadow-3` | `0 20px 48px rgba(15,23,42,.18)` | Modal o celebración; uso excepcional |

La elevación también debe tener borde/superficie diferenciada: no depender sólo de una sombra, especialmente en pantallas de bajo contraste.

## Movimiento y respuesta

| Token | Valor | Uso |
|---|---|---|
| `--ease-standard` | `cubic-bezier(.2, .8, .2, 1)` | Transiciones generales |
| `--duration-fast` | 120 ms | Press, foco, icono |
| `--duration-standard` | 200 ms | Hover, tarjeta, feedback |
| `--duration-slow` | 320 ms | Cambio de vista, celebración discreta |

- Animar sólo opacidad y `transform`; nunca bloquear una respuesta, la navegación ni la lectura.
- Para `prefers-reduced-motion: reduce`, desactivar transiciones no esenciales, confeti y desplazamiento; conservar cambios de estado visibles instantáneos.
- El foco de teclado usa anillo de 3 px `--color-action`, offset de 3 px; no eliminar `outline` sin reemplazo equivalente.
- Éxito/error: feedback inmediato en texto, icono y contenedor; las animaciones son decorativas.

## Iconografía y marcas de aplicaciones

1. **Iconos funcionales locales:** guardar SVG propios en `assets/icons/`; usar `currentColor`, `viewBox="0 0 24 24"`, sin texto incrustado. Tamaño visual: 16 px en texto, 20 px en botones, 24 px en navegación/tarjetas, 32 px sólo para estado vacío. Alinear `vertical-align: -0.125em` junto a texto o usar contenedor flex (`align-items: center; gap: 8px`).
2. **Modo:** por defecto `monochrome` con `currentColor`; iconos que indican éxito/error heredan el token semántico correspondiente. No mezclar estilos rellenados, contorneados y 3D en el mismo control.
3. **Logos de ChatGPT, Claude, Gemini y conectores:** son marcas, no iconos del sistema. Guardar versiones SVG/PNG aprobadas localmente en `assets/brands/`, con nombre explícito (`chatgpt.svg`, `claude.svg`, `gemini.svg`, `google-drive.svg`, etc.), fondo transparente y `alt` textual. Mostrar a 24 px en filas y 32 px en tarjetas; preservar proporción, no recolorear, recortar ni usar como único significado.
4. **Licencia/atribución:** antes de incorporar cada marca, registrar en `assets/brands/README.md` la URL de la guía de marca y permiso/uso aplicable. Si no hay activo aprobado, usar una cápsula textual (“ChatGPT”) en lugar de dibujar una imitación.
5. Toda acción con sólo icono necesita un nombre accesible (`aria-label`) y tooltip visible al foco/hover. Los logos decorativos llevan `alt=""`; los que identifican una elección llevan, por ejemplo, `alt="ChatGPT"`.

## Criterio de aceptación visual

- Cuerpo, etiquetas, botones, placeholders, foco y feedback cumplen como mínimo WCAG AA para su color de fondo real.
- A 200% de zoom, no se pierde contenido ni controles; el grid se apila sin scroll horizontal.
- La barra de progreso comunica porcentaje y texto (“3 de 7 módulos”), no sólo color/longitud.
- Cada resultado de quiz comunica “Correcto” o “Aún no” y una explicación, además de su color e icono.
- Probar en 320 px, 768 px y 1280 px; con teclado, lector de pantalla y reducción de movimiento.
