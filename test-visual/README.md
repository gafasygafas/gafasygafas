# Test visual · Gafas y Gafas

Test de visión en línea (3 minutos, pensado para celular). Si el resultado lo recomienda, termina invitando a
agendar en https://gafasygafas.com.co/agendar-cita-examen-visual/.

Es **un solo archivo** (`index.html`) sin dependencias externas.

## Qué evalúa

| Paso | Cómo | Se recomienda examen si… |
|---|---|---|
| Preguntas rápidas | ¿Usa gafas?, edad, síntomas, último examen | Hay algún síntoma o el último examen fue hace más de 2 años |
| Calibración | Tarjeta tamaño estándar (cédula, débito, crédito, TuLlave) contra la pantalla | — (si la omite, se usa un tamaño estimado) |
| Visión de lejos, cada ojo | E direccional a brazo estirado (≈ 60 cm), escalera de tamaños | Algún ojo peor que ≈ 20/25, o mucha diferencia entre ojos |
| Astigmatismo, cada ojo | Esfera de líneas radiales | Ve líneas más oscuras/borrosas o no está seguro |
| Visión de cerca | Números a 40 cm con 4 opciones | No lee ≈ 0,5 decimal a 40 cm |
| Rejilla de Amsler, cada ojo | Cuadrícula con punto central | Ve líneas onduladas o zonas faltantes (resultado rojo: "consulta pronto") |

Medidas "a prueba de tontos": una pregunta por pantalla, botones grandes, una E de práctica antes de empezar,
botón «No la veo» (evita adivinar), aviso si la pantalla tiene zoom, pantalla siempre encendida durante el test,
y en computador pide alejarse lo necesario según la resolución de la pantalla y permite usar las flechas del teclado.

## Personalizar

Al inicio de `index.html`:

- **Colores**: variables `--brand`, `--accent`, etc. en `:root`.
- **Enlaces y umbrales**: objeto `CONFIG` en el `<script>` (URL de agendamiento, UTM, número de WhatsApp opcional).

## Publicar en WordPress

1. Subir `index.html` al hosting, por ejemplo en `/test-visual/index.html`
   (o como archivo en Medios / con un plugin de "Custom HTML page").
2. Opción A — enlazar directo: `https://gafasygafas.com.co/test-visual/`
3. Opción B — incrustar en una página con un bloque HTML personalizado:

   ```html
   <iframe src="/test-visual/index.html" style="width:100%;height:100vh;border:0" allow="screen-wake-lock"></iframe>
   ```

   El botón de agendar abre la página de citas en la ventana principal (`target="_top"`).

## Analítica

Si la página tiene Google Tag Manager, el test envía eventos a `dataLayer`:
`test_visual_start`, `test_visual_calibrated`, `test_visual_acuity`, `test_visual_astig`, `test_visual_near`,
`test_visual_amsler`, `test_visual_complete` (con `verdict`: ok / warn / bad) y `test_visual_cta_click`.
El enlace de agendar lleva `utm_source=test-visual` y `utm_content=<resultado>`.
Dentro de un iframe, los mismos eventos se envían a la página padre con `postMessage`.

> Es una herramienta orientativa, no un diagnóstico médico.
