# Informe de auditoría — Parte 2: Laboratorio cruzado

**Sitio auditado:** Club Deportivo — Juan José Abril Landín (ficha N.º 1).  
**Archivos contrastados:** `index.html` (236 líneas), `css/estilos.css` (175 líneas), `docs/ficha.md`.  
**Método:** comparación del código original con la ficha, capturas de DevTools/validadores proporcionadas, prueba de comportamiento en Chromium con el mismo HTML y CSS y consulta de normas oficiales. El código auditado no fue modificado.

## 1. Expectativa previa y verificación

| Requisito | Qué se espera encontrar (antes de verificar) | Resultado de la verificación |
|---|---|---|
| **Base 1. Semántica** | `header`, `nav`, `main`, dos `section` o `article` con encabezados, `aside`, `footer`, jerarquía `h1`/`h2`. | **Cumple la estructura.** Tres `section` con `h2`, más `aside` y `footer` (líns. 25–232). Existe un enlace interno sin destino (H1). |
| **Base 2. Formulario** | Cinco o más campos, `label`, tipos apropiados, validación HTML y `fieldset`/`legend`. | **Parcial.** Nueve `input` (tres de texto/teléfono, tres radio y tres checkbox), etiquetas y agrupación correctas; faltan instrucciones de restricciones y obligatoriedad (H2). |
| **Base 3. Tabla** | Tabla real con `caption`, `thead`, `tbody` y `th scope`. | **Cumple.** `index.html`, líneas 172–215; encabezados de fila y columna. |
| **Base 4. Responsive** | Grid en estructura general, Flexbox en un componente y sin scroll horizontal de toda la página a 360 px. | **Cumple en prueba.** `nav` usa Flexbox; `body` pasa a Grid en 900 px. En Chromium, `documentElement.scrollWidth = clientWidth = 360 px`. Las tablas pueden desplazarse dentro de la sección a anchuras menores. |
| **Base 5. Color** | Variables CSS `--color-...` y relación ≥ 4,5:1 en texto normal. | **Parcial.** Blanco sobre azul ≈ 9,31:1; negro sobre gris ≈ 19,09:1. Una variable empleada no está definida (H3). |
| **Base 6. Teclado** | Alcanzar y usar enlaces y controles mediante teclado, con foco visible. | **Parcial.** Se comprobó recorrido con `Tab` y contorno de foco de 3 px, pero su contraste no llega a 3:1 sobre algunas superficies (H4); un enlace no conduce al destino prometido (H1). |
| **Base 7. Validadores** | Cero errores W3C HTML y CSS. | **Cumple según capturas E01–E02.** Son evidencias previas de validación; no se confunde una variable sin definir con un error de sintaxis. |
| **Base 8. Imágenes** | `alt` correspondiente a la función de cada imagen. | **Cumple razonablemente.** Única imagen de encabezado, con `alt="Logo del club deportivo"` (lín. 28). |
| **Ficha: temática** | Página sobre un club deportivo. | **Cumple.** Clubes, inscripción y horarios/precios. |
| **Ficha: campos** | Grupo de `radio` y grupo de `checkbox`. | **Cumple.** `fieldset` y `legend` independientes (líns. 102–147); radio con un `name` compartido y checkbox de selección múltiple. |
| **Ficha: breakpoint** | Cambio estructural principal a 900 px. | **Cumple.** `@media (min-width: 900px)` (CSS líns. 66–80); Grid ausente a 899 px y presente a 900 px. |
| **Ficha: tipografía** | `clamp()` para tipografía fluida; ninguna media query modifica tamaños de texto. | **Cumple.** `clamp()` en `body`, `h1`, `h2`; la única media query modifica el layout, no `font-size`. |

## 2. Hallazgos comprobados

| N.º | Requisito | Ubicación (archivo y línea o selector) | Hallazgo | Norma que incumple | Evidencia | Severidad |
|---|---|---|---|---|---|---|
| **1 (H1)** | Base 6: navegación completa | `index.html`, **38**, `nav a[href="#preguntasfrecuentes"]` | «Preguntas frecuentes» enlaza a `#preguntasfrecuentes`, pero **no existe el `id` correspondiente**. El enlace no conduce a la sección anunciada. | **Incumplimiento funcional del requisito docente de navegación**. [WHATWG HTML, §7.4.6.4](https://html.spec.whatwg.org/multipage/browsing-the-web.html#scrolling-to-a-fragment) define la navegación a fragmentos. **No es por sí mismo un error de conformidad HTML ni un fallo WCAG probado.** | [E13, código y búsqueda del destino](evidencias/13-h1-fragmento-sin-destino.png); [E10, menú con foco](evidencias/10-foco-enlace-navegacion.png). | **Media** |
| **2 (H2)** | Base 2: formulario y accesibilidad | `index.html`, **84, 90, 96, 109–120** | Se exige mínimo **3 caracteres** para nombres/apellidos, **10 dígitos** para teléfono y selección obligatoria de deporte, sin instrucciones visibles previas sobre restricciones ni campos requeridos. | **WCAG 2.2 [3.3.2, Etiquetas o instrucciones (A)](https://www.w3.org/WAI/WCAG22/Understanding/labels-or-instructions)**; [técnica H90 para campos obligatorios](https://www.w3.org/WAI/WCAG22/Techniques/html/H90). | [E14, extractos de los campos](evidencias/14-h2-instrucciones-formulario.png); [E08, teléfono inválido](evidencias/08-error-formato-telefono.png); [E09, longitud inválida](evidencias/09-error-longitud-nombre.png). | **Media** |
| **3 (H3)** | Base 5: variables de color | `css/estilos.css`, **124**, `table { background-color: var(--color-superficie); }` y `:root` (2–8) | **`--color-superficie` no está declarada** ni tiene valor de respaldo en `var()`. La tabla calcula `background-color: transparent`, distinto del valor pretendido. | [**W3C CSS Custom Properties Level 1, §3.1, Invalid Variables**](https://www.w3.org/TR/css-variables-1/#invalid-variables). Es un error funcional en el uso de la variable, **no un error sintáctico del validador CSS**. | [E15, declaración y valor computado](evidencias/15-h3-variable-no-definida.png); [E02, validador CSS](evidencias/02-validacion-css.png). | **Baja** |
| **4 (H4)** | Base 6: contraste del indicador de foco | `css/estilos.css`, **7 y 158–160**, `--color-foco` y `:focus-visible` | El contorno naranja **#D97706** de 3 px es visible al usar `Tab`, pero contrasta **2,92:1** con el menú azul **#004494**, y **2,90:1** con el fondo gris **#F4F4F4**; no alcanza **3:1**. | **WCAG 2.2 [1.4.11, Contraste no textual (AA)](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast)** para indicadores de foco personalizados frente a colores adyacentes. **No se considera fallo de 2.4.7**, pues el foco existe. | [E16, código y cálculo](evidencias/16-h4-contraste-foco.png); [E10, contorno visible en enlace](evidencias/10-foco-enlace-navegacion.png). | **Media** |

**Punto de WCAG AA que requiere aclaración, no contabilizado como hallazgo:** los campos de nombres, apellidos y teléfono no tienen identificadores de propósito `autocomplete` (líneas 84, 90 y 96). **WCAG 2.2, criterio [1.3.5, Identificación del propósito de entrada (AA)](https://www.w3.org/WAI/WCAG22/Understanding/identify-input-purpose)** resulta aplicable si el formulario recoge información de la **persona que lo está completando**. Si el estudiante inscrito es un tercero, esa condición puede no cumplirse. Antes de atribuir infracción, confirmar quién completa el formulario. Cuando corresponda, son pertinentes los valores `given-name`, `family-name` y `tel` (técnica oficial [H98](https://www.w3.org/WAI/WCAG22/Techniques/html/H98)).

**Observación funcional no contabilizada:** un mismo usuario puede marcar a la vez «Experiencia nula» y «Experiencia básica»; conviene aclarar si el nivel admite selección múltiple. No se registra como infracción porque la ficha **exige** usar un grupo `checkbox`.

## 3. Evidencias de requisitos cumplidos

- [E01: validador W3C HTML](evidencias/01-validacion-html.png) y [E02: validador W3C CSS](evidencias/02-validacion-css.png), capturas originales.
- [E03: 360 px](evidencias/03-vista-360px.png), [E04: 899 px](evidencias/04-vista-899px.png), [E05: 900 px](evidencias/05-vista-900px.png), [E06: 1280 px](evidencias/06-vista-1280px.png), [E07: Grid en DevTools](evidencias/07-grid-devtools-900px.png).
- [E11: enlace de salto al contenido](evidencias/11-enlace-salto-foco.png) y [E12: controles radio/checkbox](evidencias/12-radio-checkbox.png).

**Alcance:** la comprobación de 360 px y Grid se reprodujo en Chromium; las capturas E01–E12 proceden del informe previo. Las nuevas E13–E16 son extractos fieles del código original con resultados de prueba. Esto **no constituye una certificación WCAG 2.2 AA integral**; los cuatro hallazgos incluyen únicamente lo que tiene fundamento y evidencia verificables.
