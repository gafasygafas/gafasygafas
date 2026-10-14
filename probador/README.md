# Probador virtual G&G

App aparte (independiente del Vision Check) para que la persona se pruebe las monturas de Gafas & Gafas con la
cámara del celular o con una foto, vea las fotos reales del producto, el precio con cada tipo de lente y pase a
comprar. La idea es luego montarlo en la web (por ejemplo, botón «Pruébatelas» en cada producto).

Link: https://probador-gafasygafas.vercel.app · Proyecto Vercel: `probador-gafasygafas` (carpeta raíz `probador/`).

## Pantallas

1. **Inicio** (mismo estilo de gafasygafas.com.co: fondo hueso, encabezado negro, Anton, tarjetas con borde negro):
   titular, botones «Abrir cámara» / «Subir foto», y la vitrina de monturas con filtros (Acetato, TR90, Metálicas…).
   Cada tarjeta usa la foto de producto de la web. Al tocarla se elige cámara o foto.
2. **Probador**: imagen con la montura puesta (etiqueta «Tamaño aproximado»), filtros y tira de monturas, ficha
   (código, material, precio, colores, ajuste ±, aviso de tamaño con lente · puente · brazo), **«Así es en la
   tienda»** (fotos de la web, se amplían al tocar), **«Elige tus lentes»** (las mismas opciones y precios del
   configurador de la web) y el botón **«Elegir lentes y comprar»**, que abre la ficha del producto con el color ya
   elegido (`?attribute_color=…`). Además «Guardar foto» (con marca G&G) y «Agendar examen».

## Cómo funciona

- **Detección de la cara:** MediaPipe Face Landmarker (Google), versión `0.10.35`, corre dentro del navegador.
  La imagen de la cámara o la foto **no sale del celular**.
- **Carga desde CDN:** el detector (≈ 13 MB) se carga desde jsDelivr y el modelo desde Google; las fotos de
  producto se cargan desde gafasygafas.com.co. Vercel solo sirve la página y los recortes de las monturas
  (≈ 40 KB cada uno, se cargan solo cuando se usan).
- **Tamaño real:** el diámetro del iris (≈ 11,7 mm) sirve de regla; con el ancho del lente y el puente de cada
  montura (tomados de la ficha de la web) se dibuja la montura a escala. Se limita a un rango humano de distancia
  entre pupilas (54–72 mm). Botones ± permiten ajustar. **Es una aproximación** y la pantalla lo dice.
- **Enlace directo:** `?ref=WD1168` abre la vitrina con esa montura elegida (para enlazarlo desde cada producto).
- **Posición:** centro de los lentes entre las pupilas (2 mm más abajo), inclinación según la línea de los ojos,
  suavizado entre cuadros para que no tiemble.
- **Modo foto:** si la cámara no está disponible (por ejemplo, en el navegador interno de Instagram) o la persona
  no da permiso, puede subir una foto.

## Monturas (carpeta `monturas/`)

`catalogo.json` tiene por montura: código (SKU), categoría, precio, link, lente · puente · brazo (de la ficha),
fotos de la web y sus colores (recorte `.webp` de 760 px, miniatura `-t.webp`, centro de cada lente, color de
muestra, nombre de la variación para preseleccionarla en la web y foto de esa variación).

**Recorte (v3):** se parte de la foto de producto de la web en alta resolución.
- La silueta exterior sale de un modelo de segmentación (ISNet), que da bordes limpios y respeta la forma real.
- Los lentes se detectan por color y se toman como formas convexas y simétricas (izquierdo = espejo del derecho),
  así desaparecen las patillas y plaquetas que se ven a través del cristal.
- El borde se «desmezcla» del fondo para que no quede halo blanco, y se conservan los colores originales.

Cada recorte se revisó a mano a tamaño completo. Quedan por fuera:
- las monturas totalmente transparentes (se verían como un hilo);
- las gafas de sol y clip-on con lente oscuro;
- las fotos que no son de frente;
- los modelos con la patilla atravesando los dos lentes;
- las referencias sin medidas (o con medidas erradas) en la ficha web.

Precios de lentes: están en `LENSES` dentro de `index.html`; si cambian en la web, hay que actualizarlos ahí.

## Medición

Eventos en `dataLayer`/`gtag`:
- `pr_start {modo}`
- `pr_card {modelo}`
- `pr_frame {modelo, color}`
- `pr_photo`
- `pr_cta_buy`
- `pr_cta_agenda`
- `pr_camera_error`

Los enlaces a la tienda y a agendar llevan `utm_source=probador&utm_medium=app&utm_campaign=probador-virtual&utm_content=<modelo>`.

## Límites conocidos

- Es una prueba 2D de frente: con giros grandes de cabeza no se ven las patillas.
- La escala depende de la detección del iris: con poca luz o lejos de la cámara puede variar (para eso el ±).
- Requiere HTTPS y un navegador con acceso a cámara.
