# Ministerio de Alabanza — Contexto

## ¿Qué es?
Una app web para organizar el ministerio de música de la iglesia **Perlas para Dios**. Es un solo archivo, `index.html`, con todo el HTML, CSS y JS adentro. No necesita instalación: se abre en el navegador.

## ¿Para qué sirve?
Para no tener que armar todo a mano cada mes y cada domingo:

- **Cronograma mensual:** quién toca cada servicio, qué canciones van, el versículo, el devocional y el desafío del mes. Genera una imagen para mandar al grupo.
- **Coordinación:** el guión del servicio paso a paso (canciones, textos, versículos, oraciones y eventos como ofrenda, santa cena o predicación). Genera el "Programa del domingo" como imagen.
- **Canciones:** la biblioteca con letra, acordes, tonos y categorías. Vista previa en hojas A4 para descargar en PDF o PNG.
- **Cancionero:** modo lectura para la congregación, con transposición de tono/capo y auto-scroll. Se comparte por link con `?lectura`. Los músicos pueden descargar en PDF lo que están viendo (solo letra, o acordes con el tono y capo elegidos; si cambiaron el original, la hoja lo avisa).
- **Listas armadas y Bandas:** sets de canciones y equipos que se reutilizan.
- **Participación y Estadísticas:** quién está tocando mucho o poco, qué canciones se repiten y cuáles conviene rotar.
- **Configuración:** datos de la iglesia, logo y lista de miembros.

## ¿A quién está dirigida?
- **Al coordinador/líder de alabanza,** que es quien la usa para planificar.
- **A los músicos,** que reciben las imágenes del cronograma y del programa.
- **A la congregación,** que usa el Cancionero en modo lectura desde el celular.

Debe ser fácil de usar, verse bien en el celular y generar imágenes lindas para compartir por WhatsApp.

## Modalidad de trabajo
Trabajamos entre tres:

1. **Esteban:** decide qué se hace, prueba la app y da el visto bueno.
2. **Claude Code (acá):** lee el código, propone cómo hacerlo, implementa y commitea.
3. **Otra IA aparte:** Esteban la usa para pedir sugerencias y segundas opiniones. Esas ideas se traen acá y entre los tres se arma el mejor plan antes de tocar código.

Cómo trabajamos:
- Primero se charla el plan y después se implementa.
- Los cambios son chicos, un tema por commit, con mensajes en español (por ejemplo "Rediseño de…" o "Fix: …").
- Para cambios grandes se trabaja por fases (Fase 1/4, 2/4, …).
- Hacer push a `main` publica la app (GitHub Pages).

## Cómo está hecho (lo mínimo)
- **Datos:** se guardan en el `localStorage` del navegador, en claves `mm_*` (`mm_canciones`, `mm_guiones`, `mm_mes_YYYY_M`, `mm_config`, `mm_miembros`, etc.). Hay backup/restore en JSON.
- **Nube:** solo la biblioteca de canciones se sincroniza con Firebase (login con Google + Firestore). Si Firebase falla, la app sigue funcionando con los datos locales.
- **Imágenes y PDF:** se generan con html2canvas (a 2.5x). Las hojas de canciones las arma `_buildPrintPages` (una sola generación para vista previa, PNG y PDF); el PDF lo arma la app con jsPDF, no la impresión del navegador (en Safari no era confiable). JSZip se usa para el PNG de varias páginas.
- **Acordes:** una sola regla para toda la app (`isChordLine`).
- **Estilo:** fuentes Outfit (sans) y Fraunces (serif), íconos de Tabler Icons. Tema claro con detalles en pastel, y cada mes tiene su color de acento (`MES_ACCENT`).
- **Remotos:**
  - `iglesia` → `iglesiaperlasparadios.github.io` — **es el que publica** (`main` apunta acá).
  - `origin` → `bosioinmobiliaria-lang/ministerio-alabanza` — copia vieja, sin usar desde julio.

## Últimos cambios (hasta sep 2026)
- **Julio–agosto:** rediseño visual de toda la app con un sistema de diseño unificado (menú, Cronograma, Canciones, Cancionero, Coordinación, Participación, Estadísticas).
- **Nuevo:** Cancionero con modo lectura, sincronización de canciones con Firebase, lista de miembros, estadísticas tipo dashboard ("Para rotar" mira los últimos 6 meses) y la importación del guión completo desde el cronograma.
- **Septiembre:** logo nuevo (anillo de colores) e impresión de canciones: hojas A4 sin texto cortado, PDF desde Canciones y desde el Cancionero con tono/capo.
- **Pendiente:** imprimir un domingo completo en un solo PDF.
