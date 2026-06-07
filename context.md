# Ministerio de Música — Contexto del proyecto

## Descripción
Aplicación web single-page (`index.html`) para gestión del ministerio de música de la iglesia. Todo el código está en un único archivo HTML con CSS y JavaScript inline. Los datos se persisten en `localStorage` del navegador.

Repositorio: https://github.com/bosioinmobiliaria-lang/ministerio-alabanza

---

## Secciones de la app

- **Mes completo** — planificación mensual: servicios (sábados/domingos), devocional, versículo, desafío, nuevas canciones. Genera imagen PNG descargable.
- **Mis bandas** — plantillas de equipos reutilizables (Coordina, Voz 1/2/3, Teclado, Guitarra Acústica x2, Eléctrica, Batería, Bajo).
- **Canciones** — biblioteca con tonalidad iglesia, tonalidad original, artista, nota de ejecución, link YouTube, categorías espirituales, flag "nueva".
- **Listas armadas** — sets de canciones por sección (Principales, Santa Cena, Ofrenda, Intervalo, Final), listos para asignar a un servicio.
- **Participación** — seguimiento de presencia por músico con alertas (3 semanas seguidas sin servicio / en todos los servicios).
- **Configuración** — nombre de iglesia, ministerio, ciudad, logo (se usa en la imagen generada).

---

## Historial de cambios

### Sesión 1 (2026-06-06)
- Inicialización del repositorio git local.
- El archivo ya estaba como `index.html` (no necesitó renombrarse).
- Primer push al repositorio remoto (había una versión anterior subida manualmente vía GitHub UI — se fusionó manteniendo la versión local).
- **IMAGE GENERATION v3 → v4**: reemplazo completo del bloque de generación de imagen.
  - Cada columna de servicio usa su propio color de `DOMINGO_COLORS` en lugar del color único del mes.
  - Roles con avatar circular (36×36px) en lugar de emoji inline.
  - `ROLES_ROW_H` subió de 44 a 56px.
  - Header con fondo `IMG_BG` (gris claro) en lugar de blanco.
  - Se eliminó el footer con nombre de iglesia/ministerio.
  - Textos más grandes: versículo 17px, canciones 15.5px, nombre en roles 19px.
  - Ancho de columna subió a 260px (antes 248px).

---

### Sesión 2 (2026-06-07)
- **Nueva pestaña "Guión"** — implementación completa sin tocar código existente.
  - Biblioteca de letras (`mm_letras`): canciones con letra completa, tono, capo, autor, acordes. CRUD con modal.
  - Constructor del guión (`mm_guion_YYYY-MM-DD`): secuencia de bloques con tipos texto/canción/versículo/oración.
  - Reordenar bloques con botones ↑↓.
  - Múltiples guiones por fecha; carga automática del más reciente al entrar.
  - Autosave con debounce 800ms, mismo patrón que la pestaña "Mes".
  - Generación de imagen PNG ~900px ancho optimizada para iPad vertical.
  - Tarjetas por tipo: texto→azul, canción→DOMINGO_COLORS ciclando, versículo→teal, oración→violeta.
  - Letra de canciones con `white-space:pre-wrap`, mínimo 19px, acordes en monospace.

## Pendientes / ideas

- [ ] Agregar `.gitignore` para excluir `.DS_Store` y otros archivos de macOS.
- [ ] Incluir `mm_letras` y `mm_guion_*` en el sistema de backup/restore.
- [ ] Opción de duplicar un guión existente como punto de partida.
- [ ] Evaluar si agregar un footer opcional de nuevo (configurable desde la sección Configuración).
- [ ] Posibilidad de exportar imagen por servicio individual (además del mes completo).
- [ ] Vista previa en tiempo real del header de la imagen al editar Configuración.
- [ ] Soporte para más de 4 servicios en el mes sin que la imagen se deforme.
- [ ] Modo claro/oscuro para la interfaz de la app.

---

## Notas técnicas

- Generación de imagen: usa `html2canvas` v1.4.1 a escala 2.5x.
- ZIP: usa `jszip` v3.10.1 (importado pero actualmente la descarga es una sola imagen PNG).
- Fuentes: `Outfit` (sans-serif) y `Fraunces` (serif) via Google Fonts.
- Iconos: Tabler Icons v2.44.0.
- Autosave: debounce de 800ms en `localStorage`, con soporte multi-mes (`mm_mes_YEAR_MONTH`).
- Historial: guarda hasta 24 servicios anteriores para el panel espejo.
