# Ministerio de Música — Contexto del proyecto

## Descripción
Aplicación web single-page (`index.html`) para gestión del ministerio de música de la iglesia. Todo el código está en un único archivo HTML con CSS y JavaScript inline. Los datos se persisten en `localStorage` del navegador.

Repositorio: https://github.com/bosioinmobiliaria-lang/ministerio-alabanza

---

## Secciones de la app (menú actual, en orden)

- **Cronograma mensual** — planificación mensual: servicios (sábados/domingos), devocional, versículo, desafío, nuevas canciones. Genera imagen PNG descargable.
- **Coordinación** — guión del servicio: bloques texto/canción/versículo/oración, reordenables con ↑↓. Autosave 800ms. Genera PNG optimizado para iPad vertical.
- **Canciones** — biblioteca unificada (`mm_canciones`): tono iglesia, tono original, artista, nota de ejecución, link YouTube, categorías, flag "nueva", secciones estructuradas con acordes+letra.
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

### Sesión 3 (2026-06-07) — commits e971e40 → 064cdc5
- **Unificación de biblioteca**: `mm_letras` migrado a `mm_canciones`. `getLetras()`/`saveLetras()` son aliases de `getCanciones()`/`saveCanciones()`. IDs de guión remapeados en migración IIFE al inicio.
- **Editor de secciones por tipo**: Intro/Estrofa/Coro/Puente/Outro/Interludio con campos separados acordes+letra. Luego reemplazado por un único textarea libre por sección.
- **Editor de secciones — un solo textarea por sección** (commit 95beda7):
  - `_CHORD_TOKEN` / `_isChordLine(line)`: detecta líneas de acordes si >60% de tokens no vacíos matchean `^[A-G](#|b)?(m|maj|min|aug|dim|sus|add)?[0-9]*(\/[A-G](#|b)?)?$`
  - `_getSecciones(c)`: migra old `{acordes,letra}` → `{tipo,contenido}`; también maneja `c.lyrics`/`c.chords` legacy.
  - `_syncCmSecciones()`: lee `.cm-sec-contenido` textarea.
  - `transposeChordsBy(n)`: transpone solo líneas de acordes dentro de `contenido`.
  - Data model: `{tipo, contenido}` — el contenido mezcla acordes y letra como vienen de cualquier fuente.
- **Estadísticas** (pestaña nueva): 4 secciones calculadas desde `mm_mes_*` keys de localStorage.
- **Renombrado y reorden del menú** con atributos `data-page` en nav items.
- **Botón imprimir PNG por canción** → formato A4 multipágina (commit bad6e5c):
  - `_measureSectionH(s)`: mide altura real de cada sección via DOM antes de paginar.
  - `_paginateSecciones(secciones, p1H, pNH)`: distribuye secciones en páginas sin cortar ninguna.
  - `_buildCancionPageHTML(c, secciones, pageNum, totalPages)`: página 1 con header completo (nombre + badge tono + capo), páginas 2+ con header compacto (nombre gris + N/M).
  - 1 página → PNG directo; múltiples → ZIP con JSZip (ya cargado).
  - `htmlToCanvas()` acepta `extraOpts` (spread) para pasar `height:1123` y `backgroundColor:'#ffffff'`.
- **Tipografía de impresión** (commits 03644e6 → 064cdc5):
  - Acordes: 20px Courier New, bold, rojo `#dc2626`, `font-style:normal` explícito.
  - Letra coro: 20px Courier New, weight 500, azul `#2563a8`, sin cursiva.
  - Letra estrofa/puente/intro: 20px Courier New, weight 500, negro `#111827`.
  - Agrupación en pares acorde+letra: `line-height:1.2` dentro del par, `margin-bottom:4px` entre pares.
  - Entre secciones: `margin-top:28px` (primera sección sin margen). Etiqueta 11px gris `#9ca3af`.
  - **Mismo tratamiento en vista de Coordinación** (no-print): pares agrupados, naranja `#ea580c` para acordes de coro en pantalla, azul `#2563a8` italic para letra de coro en pantalla.
- **Orden alfabético A→Z** en todos los pickers y la biblioteca (localeCompare 'es').

---

## Arquitectura de datos (localStorage)

| Key | Contenido |
|-----|-----------|
| `mm_canciones` | Array de canciones unificado (incluye las que antes eran `mm_letras`) |
| `mm_bandas` | Array de plantillas de banda |
| `mm_listas` | Array de listas armadas |
| `mm_historial` | Historial de servicios (hasta 24) |
| `mm_config` | Configuración de la iglesia |
| `mm_mes_YYYY_M` | Datos del mes (servicios, versículo, devocional, desafío, nuevas) |
| `mm_guion_YYYY-MM-DD` | Guión del servicio por fecha (array de bloques) |

### Estructura de una canción (`mm_canciones`)
```js
{
  id: string,          // timestamp
  name: string,
  key: string,         // tono iglesia
  keyOrig: string,     // tono original
  author: string,
  playNote: string,    // nota de ejecución
  youtube: string,
  nueva: boolean,
  cats: string[],      // categorías espirituales
  capo: string,
  chordsOnly: boolean, // mostrar solo acordes en guión
  secciones: [{tipo, contenido}],  // nuevo formato unificado
  usosCount: number
}
```

### Tipos de sección (`_SEC_TIPOS`)
`intro`, `estrofa`, `coro`, `puente`, `outro`, `interludio`

---

## Funciones clave

### Canciones
- `getCanciones()` / `saveCanciones()` — CRUD sobre `mm_canciones`
- `getLetras()` / `saveLetras()` — aliases de las anteriores
- `_isChordLine(line)` — detecta líneas de acordes (>60% tokens matchean regex)
- `_getSecciones(c)` — normaliza formatos legacy al nuevo `{tipo,contenido}`
- `_renderSeccionesHTML(secciones, chordsOnly, scale, printFonts)` — renderiza secciones con pares acorde+letra. `printFonts=true` activa tipografía para impresión (Courier New, tamaños mayores, sin colores de pantalla).
- `_measureSectionH(s)` — mide altura DOM de una sección (para paginación)
- `_paginateSecciones(secs, p1H, pNH)` — distribuye secciones en páginas A4
- `_buildCancionPageHTML(c, secs, pageNum, total)` — HTML de una página A4
- `printCancion(id)` — genera y descarga PNG (o ZIP si multipágina)
- `transposeChordsBy(n)` — transpone solo líneas de acordes en `contenido`

### Guión / Coordinación
- `buildGuionCardCancion(letra, idx)` — tarjeta de canción en el guión (usa `_renderSeccionesHTML` con `scale='large'`)
- `buildGuionHTML()` — HTML completo del guión para PNG
- `htmlToCanvas(html, targetId, extraOpts)` — wrapper de html2canvas a 2.5x

---

## Notas técnicas

- **html2canvas** v1.4.1 a escala 2.5x. Para páginas A4 se pasa `{height:1123, backgroundColor:'#ffffff'}` via `extraOpts`.
- **JSZip** v3.10.1 — usado para descarga multipágina de canciones.
- **Fuentes**: `Outfit` (sans-serif), `Fraunces` (serif) via Google Fonts. `Courier New` monospace solo en impresión de canciones.
- **Iconos**: Tabler Icons v2.44.0.
- **Autosave**: debounce 800ms en mes y guión.
- **Deploy**: GitHub Pages auto-deploy al hacer push a `main`. No hay `gh` CLI — se usa `git push` directo con credenciales guardadas.
- **Detección de acordes**: `_CHORD_TOKEN = /^[A-G](#|b)?(m|maj|min|aug|dim|sus|add)?[0-9]*(\/[A-G](#|b)?)?$/` — línea es acorde si >60% de tokens no vacíos matchean.

---

## Pendientes / ideas

- [ ] Agregar `.gitignore` para excluir `.DS_Store` y otros archivos de macOS.
- [ ] Incluir `mm_guion_*` en el sistema de backup/restore.
- [ ] Opción de duplicar un guión existente como punto de partida.
- [ ] Posibilidad de exportar imagen por servicio individual (además del mes completo).
- [ ] Soporte para más de 4 servicios en el mes sin que la imagen se deforme.
- [ ] Modo claro/oscuro para la interfaz de la app.
