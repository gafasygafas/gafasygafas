# Probador virtual G&G

App aparte (independiente del Vision Check) para que la persona se pruebe monturas de Gafas & Gafas con la
cámara del celular o con una foto. Prototipo con 5 monturas; la idea es luego montarlo en la web
(por ejemplo, botón «Pruébatela» en cada producto).

Link: https://probador-gafasygafas.vercel.app · Proyecto Vercel: `probador-gafasygafas` (carpeta raíz `probador/`).

## Cómo funciona

- **Detección de la cara:** MediaPipe Face Landmarker (Google), versión `0.10.35`, corre dentro del navegador.
  La imagen de la cámara o la foto **no sale del celular**.
- **Carga desde CDN:** el detector (≈ 13 MB) se carga desde jsDelivr y el modelo desde Google, no desde Vercel,
  para no gastar el límite de transferencia del proyecto. Vercel solo sirve la página y las fotos de las monturas
  (≈ 700 KB en total).
- **Tamaño real:** el diámetro del iris (≈ 11,7 mm) sirve de regla; con el ancho del lente y el puente de cada
  montura (tomados de la ficha de la web) se dibuja la montura a escala. Se limita a un rango humano de distancia
  entre pupilas (54–72 mm). Botones ± permiten ajustar. **Es una aproximación**: la pantalla lo dice con una etiqueta
  «Tamaño aproximado» sobre la imagen y un aviso con las medidas de la ficha (lente · puente) que invita a probársela
  en tienda.
- **Enlace directo:** `?ref=WD1168` abre el probador con esa montura (para enlazarlo luego desde cada producto).
- **Posición:** centro de los lentes entre las pupilas (2 mm más abajo), inclinación según la línea de los ojos,
  suavizado entre cuadros para que no tiemble.
- **Modo foto:** si la cámara no está disponible (por ejemplo, en el navegador interno de Instagram) o la persona
  no da permiso, puede subir una foto.

## Monturas (carpeta `monturas/`)

`catalogo.json` tiene por montura: código, categoría, precio, link al producto, ancho de lente, puente y sus colores
(imagen recortada `.webp`, centro de cada lente en la imagen y color de muestra).

| Código | Material | Colores en el prototipo |
|---|---|---|
| WD1168 | Acetato | Negro, Miel, Carey |
| GYG3003 | Acetato | Negro azul, Vino carey, Carey azul |
| MF0612 | TR90 | Negro, Café, Azul, Morado, Rojo |
| F99062 | Metálico | 1 (la web tiene una sola foto para sus 5 colores) |
| WK4010 | Híbrido | Amarillo (la foto de Menta es de ambiente y no sirve) |

Las imágenes se generaron desde las fotos de producto de la web (vista frontal, fondo claro): se quita el fondo,
se borran las patillas que se ven a través del lente y se calcula el centro de cada lente. Se conservan los
**colores originales** de la foto (sin aclarar), se borra el reflejo de la mesa bajo el aro y el borde blanco del
fondo. Los colores muy claros (transparente, gris claro) y las gafas de sol con lente oscuro no se recortan bien de
forma automática.

## Medición

Eventos en `dataLayer`/`gtag`: `pr_start {modo}`, `pr_frame {modelo, color}`, `pr_photo`, `pr_cta_buy`,
`pr_cta_agenda`, `pr_camera_error`. Los botones de la tienda y de agendar llevan
`utm_source=probador&utm_medium=app&utm_campaign=probador-virtual&utm_content=<modelo>`.

## Límites conocidos

- Es una prueba 2D de frente: con giros grandes de cabeza no se ven las patillas.
- La escala depende de la detección del iris: con poca luz o lejos de la cámara puede variar (para eso el ±).
- Requiere HTTPS y un navegador con acceso a cámara.
