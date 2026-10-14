# G&G Vision Check

Tamizaje visual online (≈ 5 minutos, mobile-first) para Gafas & Gafas. Pensado para el link de las historias de
Instagram y campañas: la persona hace el test y, según el resultado, se le invita a agendar su examen visual en
https://gafasygafas.com.co/agendar-cita-examen-visual/ o a escribir por WhatsApp a la tienda más cercana.

Es **un solo archivo** (`index.html`), sin build y sin dependencias propias. Carga las fuentes (Google Fonts) y el
logo/foto desde el sitio de G&G al abrirse en el navegador de quien lo usa.

## Sistema de diseño

Sigue el manual de marca G&G: negro `#111111` + amarillo `#FFD756` + hueso `#F4F4F2`, tipografías Anton (titulares),
Archivo (cuerpo/UI) y Bodoni Moda italic (acento editorial), botones y tarjetas con radio 10 px, tuteo y tono directo.
Los tokens están en `:root` al inicio de `index.html` (`--gg-*`, `--font-*`). Sin emojis: iconos de línea propios.

## Flujo

1. **Inicio** (fondo negro): titular, prueba social, botón «Hacer el test» o «Elegir un test».
2. Aviso de que es un tamizaje (no diagnóstico) → brillo máximo → **calibración con tarjeta** (la medida se guarda en
   `localStorage` para no repetirla) → «ponte tus gafas o lentes» → síntomas (solo en el test completo).
3. **Pruebas** (en este orden; cada una indica qué ojo tapar y a qué distancia):
   | Prueba | Estímulo | Distancia | Ojos |
   |---|---|---|---|
   | Agudeza visual | Anillo de Landolt C, escalera de tamaños 0,1 → 1,25 | brazo estirado (60 cm) | derecho → izquierdo |
   | Sensibilidad al contraste | Landolt C con contraste decreciente (100 % → 3 %) | brazo estirado | derecho → izquierdo |
   | Astigmatismo | Esfera de líneas (sunburst) | 30 cm | uno a la vez |
   | Visión central | Rejilla de Amsler | 30 cm | uno a la vez |
   | Visión de color | 3 láminas de puntos **propias** (generadas en el navegador), 1 de control + 2 rojo-verde | 30 cm | ambos |
4. Después de **cada respuesta** objetiva aparece un check verde o una cruz roja (con la respuesta correcta) y una fila
   de puntos con el historial; en el resultado se muestran los aciertos de cada prueba.
5. **Resultado** con semáforo por prueba (excelente / bien / revisar) y un nivel global:
   `excelente`, `bien`, `revisar` o `urgente` (si algo raro en la rejilla de Amsler).
6. **Conversión**: botón de agendar (con UTM), WhatsApp (a la tienda más cercana si la persona comparte su ubicación,
   o al número central +57 310 715 7081), enlaces a monturas y Zise & Fit, y el listado de las 9 tiendas con
   «Cómo llegar» y WhatsApp.

Las matemáticas están en `drawC` / `cTest`: abertura `g = distancia × tan(1′) / agudeza`, anillo de `5g`, con la
calibración `px/mm` obtenida de la tarjeta (ISO ID-1, 53,98 mm de lado corto).

## Ajustes rápidos

En el `<script>`, objeto `CONFIG`: URLs, UTM por defecto, WhatsApp central, tiempos y escalas de las pruebas.
`STORES` tiene las tiendas (dirección, horario, WhatsApp, coordenadas).

El link del test acepta `?utm_source=instagram&utm_medium=historias`; esos valores se pasan al enlace de agendar
(por defecto `vision-check` / `app`). La campaña siempre es `resultado-<nivel>`.

## Medición (GA4 / GTM)

Eventos en `dataLayer` (y `gtag` si existe): `vc_start`, `vc_calibration_done`, `vc_calibration_skipped`,
`vc_test_complete {test, eye, result}`, `vc_result_view {nivel}`, `vc_cta_agenda`, `vc_cta_whatsapp {tienda}`.
Dentro de un iframe, los mismos eventos se envían a la página padre con `postMessage`.

## Pendiente / decisiones

- **Texto de la cita**: el sitio dice «examen descontable» en el home y «sin costo» en la página de citas. Por ahora el
  test no promete precio ni gratuidad: el botón dice «Agenda tu examen visual».
- **Captura de leads** (email/WhatsApp con habeas data, `vc_lead_submit`): requiere un backend; no está implementado.
- Marca ZEISS: solo aparece «Lentes Zeiss» como texto, sin logo.
- Las láminas de color son propias y orientativas; validar con optometría antes de campañas grandes.
- El logo en negro (para fondos claros) no existe en el sitio; por ahora el encabezado negro usa el logo blanco.

> Es una herramienta orientativa, no un diagnóstico médico.
