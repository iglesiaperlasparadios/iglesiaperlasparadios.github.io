# Ministerio de Música — Contexto del proyecto

## Descripción
Aplicación web single-page (`index.html`) para gestión del ministerio de música de la iglesia. Todo el código está en un único archivo HTML con CSS y JavaScript inline. Los datos se persisten en `localStorage` del navegador.

Repositorio: https://github.com/bosioinmobiliaria-lang/ministerio-alabanza

---

## Secciones de la app (menú actual, en orden)

- **Cronograma mensual** — planificación mensual: servicios (sábados/domingos), devocional, versículo, desafío, nuevas canciones. Genera imagen PNG descargable.
- **Coordinación** — guión del servicio: bloques texto/canción/versículo/oración/evento, reordenables con ↑↓. Autosave 800ms. Genera PNG optimizado para iPad vertical. Genera imagen "Programa del domingo".
- **Canciones** — biblioteca unificada (`mm_canciones`): tono iglesia, tono original, artista, nota de ejecución, link YouTube, categorías, flag "nueva", secciones estructuradas con acordes+letra. Vista previa de impresión con slider de font size.
- **Listas armadas** — sets de canciones por sección (Principales, Santa Cena, Ofrenda, Intervalo, Final).
- **Bandas** — plantillas de equipos reutilizables (Coordina, Voz 1/2/3, Teclado, Guitarra Acústica x2, Eléctrica, Batería, Bajo).
- **Participación** — seguimiento de presencia por músico con alertas.
- **Estadísticas** — 4 secciones: canciones por uso, músicos por participación, uso de tonos, canciones sin usar en 60 días.
- **Configuración** — nombre de iglesia, ministerio, ciudad, logo.

---

## Historial de cambios

### Sesión 1 (2026-06-06)
- Inicialización del repositorio git local y primer push.
- **IMAGE GENERATION v3 → v4**: colores por servicio, avatares circulares en roles, textos más grandes, sin footer.

### Sesión 2 (2026-06-07)
- **Nueva pestaña "Coordinación"** (antes "Guión"): biblioteca de letras + constructor de guión por fecha.
- Autosave 800ms, múltiples guiones por fecha, generación PNG para iPad.

### Sesión 3 (2026-06-07) — commits e971e40 → 5e3db2c
- **Unificación de biblioteca**: `mm_letras` migrado a `mm_canciones`.
- **Editor de secciones — un solo textarea por sección** (commit 95beda7).
- **Estadísticas** (pestaña nueva).
- **Botón imprimir PNG por canción** → formato A4 multipágina (commit bad6e5c).
- **Tipografía de impresión refinada** (Courier New, pares acorde+letra).
- **Orden alfabético A→Z** en todos los pickers y la biblioteca.
- **Conectar Coordinación con Cronograma mensual** (commit d149fe5):
  - Picker de fecha en el guión.
  - Importación automática de canciones del cronograma.
  - Banner del servicio (coordinador, nombre).
  - Protección contra sobreescritura.
  - Lista de guiones (landing) con tarjetas por fecha.
  - Duplicar/eliminar guión.
  - Storage migrado a `mm_guiones` (array) desde `mm_guion_YYYY-MM-DD`.
- **Guiones incluidos en backup/restore** (commit d842b00).

### Sesión 4 (2026-06-11) — commits d19b00e → 78de4ad

#### Vista previa de impresión de canciones (commit d19b00e)
- Botón 🖨️ abre modal `modal-print-preview` en lugar de descargar directamente.
- Slider 14px→26px (default 20px): regenera vista en tiempo real.
- Indicador de cantidad de páginas se actualiza al cambiar font size.
- Paginación y renderizado parametrizados con `fontSize`.
- Botón "Descargar PNG" usa el font size elegido.

#### Corrección de detección de acordes y colores (commits d76438e → 5064744)
- **`_isChordLine`** mejorado:
  - Tokens que son separadores puros (`-`, `/`, `–`, `—`) se descartan antes de evaluar.
  - Tokens con guion interno (`Am-G-D`) se parten en sus partes.
  - Resultado: `D - A - Bm - G`, `D / A / G`, `Am-G-D-F` detectados correctamente como acordes.
- **Colores unificados** en vista de coordinación (pantalla):
  - Acordes: siempre naranja `#ea580c` (eliminado rojo `#dc2626`).
  - Letra de coro: azul `#2563a8` + cursiva.
  - Letra del resto: negro `#111827` sin cursiva.
- **Colores en impresión/print preview**: misma lógica (acordes naranja, coro azul cursiva).
- `font-style:normal` explícito en todas las líneas de acordes sin excepción.

#### Tipos de sección de canciones (commits 85455a8, 1b8ea04)
- Agregado `final` y `pre-coro`.
- Eliminado `outro`.
- Orden actual: `intro`, `estrofa`, `pre-coro`, `coro`, `puente`, `interludio`, `final`.

#### Bloque tipo Evento en Coordinación (commit 0842275)
- Botón 🎯 Evento en la barra de agregar bloques.
- 7 tipos predefinidos con emoji, color pastel y borde:
  `ofrenda` 🎁, `santa-cena` 🍞, `predicacion` 📖, `oracion-g` 🙏, `bienvenida` 👋, `anuncio` 📢, `altar` ✝️.
- Campo `detail` libre (quién, duración, etc.).
- Card en el guión: fondo pastel, borde izquierdo 4px, título Fraunces serif 22px bold.
- En el listado del constructor: color del borde varía por tipo.

#### Color de fondo en bloque de texto (commit 55c7807)
- Modal de texto: 5 chips de color (blanco, amarillo, verde, azul, violeta).
- Campo `bgColor` guardado en el bloque.
- Card del guión aplica el color al área de contenido.
- Listado del constructor: cuadradito de color junto a "TEXTO" cuando no es blanco.

#### Programa del domingo (commit 78de4ad)
- Botón "📋 Programa del domingo" en violeta, visible cuando el guión tiene fecha.
- Modal `modal-programa` con vista previa sobre fondo gris + botón descargar.
- `buildProgramaHTML()`: imagen ~800px con fondo crema `#f9f8f6`:
  - **Header**: nombre de iglesia + "Programa del domingo" + fecha completa + nombre del servicio. Borde inferior en color del mes (`MES_ACCENT`).
  - **Equipo**: coordinador destacado con fondo accent, músicos en grilla 2 columnas con emoji de instrumento.
  - **Programa**: bloques del guión en orden. Si no hay bloques, muestra canciones del cronograma como fallback.
- `_updateProgramaBtn()`: muestra/oculta el botón según si hay fecha asignada.

---

## Arquitectura de datos (localStorage)

| Key | Contenido |
|-----|-----------|
| `mm_canciones` | Array de canciones unificado |
| `mm_bandas` | Array de plantillas de banda |
| `mm_listas` | Array de listas armadas |
| `mm_historial` | Historial de servicios (hasta 24) |
| `mm_config` | Configuración de la iglesia |
| `mm_mes_YYYY_M` | Datos del mes (servicios, versículo, devocional, desafío, nuevas) |
| `mm_guiones` | Array de guiones de coordinación (reemplazó `mm_guion_YYYY-MM-DD`) |

### Estructura de una canción (`mm_canciones`)
```js
{
  id: string,
  name: string,
  key: string,         // tono iglesia
  keyOrig: string,     // tono original
  author: string,
  playNote: string,
  youtube: string,
  nueva: boolean,
  cats: string[],
  capo: string,
  chordsOnly: boolean,
  secciones: [{tipo, contenido}],
  usosCount: number
}
```

### Tipos de sección (`_SEC_TIPOS`)
`intro`, `estrofa`, `pre-coro`, `coro`, `puente`, `interludio`, `final`

### Estructura de un guión (`mm_guiones`)
```js
{
  id: string,        // timestamp
  date: string,      // "YYYY-MM-DD"
  title: string,
  blocks: [
    {type:'cancion', id, songId},
    {type:'texto',   id, label, content, bgColor},   // bgColor: #ffffff | #fef9c3 | #dcfce7 | #dbeafe | #ede9fe
    {type:'versiculo', id, reference, content},
    {type:'oracion', id, content},
    {type:'evento',  id, eventType, detail},         // eventType: ofrenda|santa-cena|predicacion|oracion-g|bienvenida|anuncio|altar
  ]
}
```

---

## Funciones clave

### Canciones
- `getCanciones()` / `saveCanciones()` — CRUD sobre `mm_canciones`
- `_CHORD_TOKEN` / `_isChordLine(line)` — detecta líneas de acordes. Descarta separadores puros (`- / – —`), parte tokens internos (`Am-G → Am G`), luego >60% de tokens deben matchear el regex.
- `_getSecciones(c)` — normaliza formatos legacy al nuevo `{tipo,contenido}`
- `_renderSeccionesHTML(secciones, chordsOnly, scale, printFonts, fontSize)` — renderiza secciones. En pantalla: acordes naranja `#ea580c`, coro azul+cursiva, resto negro. En impresión: igual, Courier New, fontSize configurable (default 22px).
- `_measureSectionH(s, fontSize)` / `_paginateSecciones(secs, p1H, pNH, fontSize)` — paginación A4 con fontSize variable.
- `_buildCancionPageHTML(c, secs, pageNum, total, fontSize)` — HTML de una página A4.
- `printCancion` reemplazado por:
  - `openPrintPreview(id)` — abre modal con vista previa
  - `updatePrintPreview()` — regenera páginas al mover el slider
  - `downloadPrintCancion()` — genera canvas con el fontSize elegido y descarga

### Guión / Coordinación
- `getGuiones()` / `saveGuiones()` — CRUD sobre `mm_guiones`
- `_findServiceForDate(date)` — busca servicio en `mm_mes_YYYY_M` por fecha ISO
- `buildGuionCardEvento(b)` — card pastel con emoji, serif, borde izquierdo por tipo
- `buildGuionCardText(b)` — aplica `b.bgColor` al fondo del área de contenido
- `buildProgramaHTML()` — imagen del programa combinando equipo del cronograma + bloques del guión
- `openProgramaModal()` / `downloadPrograma()` — modal y descarga del programa
- `_updateProgramaBtn()` — muestra botón cuando hay guión con fecha asignada
- `htmlToCanvas(html, targetId, extraOpts)` — wrapper de html2canvas a 2.5x

### Backup / Restore
- `exportBackup()` — exporta todo: config, historial, bandas, canciones, listas, guiones, mesActual (con servicios completos).
- `confirmImport()` — restaura todo. Guiones: maneja formato nuevo (`{id,...}`) y legacy (`{date,...}`).

---

## Notas técnicas

- **html2canvas** v1.4.1 a escala 2.5x.
- **JSZip** v3.10.1 — descarga multipágina de canciones.
- **Fuentes**: `Outfit` (sans-serif), `Fraunces` (serif), `Courier New` monospace (impresión canciones).
- **Iconos**: Tabler Icons v2.44.0.
- **Autosave**: debounce 800ms.
- **Deploy**: GitHub Pages auto-deploy al hacer push a `main`. No hay `gh` CLI.
- **Migración storage**: IIFE al inicio convierte `mm_guion_YYYY-MM-DD` → `mm_guiones` array.

---

## Colores del sistema

| Uso | Color |
|-----|-------|
| Acordes (pantalla e impresión) | `#ea580c` naranja |
| Letra coro | `#2563a8` azul + cursiva |
| Letra estrofa/puente/intro/final | `#111827` negro |
| Acento del mes | `MES_ACCENT[month]` (array de 12) |
| Colores de canción en guión | `DOMINGO_COLORS` (4 colores rotativos) |
| Fondo imagen cronograma | `#f0f2f5` |
| Fondo imagen programa | `#f9f8f6` crema |
