# Práctica 3.4 – Estilos de vida entre generaciones

| Campo | Detalle |
|---|---|
| Asignatura | Desarrollo Sustentable (ACD-0908) |
| Carrera | Ingeniería en Sistemas Computacionales |
| Institución | Tecnológico Nacional de México – Campus Mazatlán |
| Unidad / Tema | 3. Escenario socio-cultural / 3.4 Estilos de vida, consumo y estrategias de sustentabilidad |
| Práctica | 3.4-P8 Página web "Estilos de vida entre generaciones" |
| Integrante | Cesar Jesus Gamez Mendoza (individual) |
| Herramientas | IA generativa, Make (Custom webhook, Make AI Agent, Webhook response), GitHub Pages |
| Página publicada | [https://cesargm-max.github.io/3.4-Pagina-web-Estilos-de-vida-entre-generaciones/](https://cesargm-max.github.io/3.4-Pagina-web-Estilos-de-vida-entre-generaciones/) |
| Video | Enlace en [`video/enlace.txt`](video/enlace.txt) |

## Estructura del repositorio

| Carpeta / archivo | Contenido |
|---|---|
| `README.md` | Este reporte técnico con explicaciones y respuestas a las preguntas de reflexión. |
| `codigo/` | `index.html` (página web) y `blueprint.json` (escenario de Make exportado). |
| `imagenes/` | Capturas del escenario completo de Make y de la página funcionando. |
| `video/enlace.txt` | Enlace al video de YouTube con la prueba en vivo. |
| `resultados/` | PDF con lo que se aprendió en la práctica. |

## Introducción

La forma en que una familia se alimenta, se viste, se transporta o maneja su basura ha cambiado mucho en tres generaciones. Algunos hábitos de los abuelos eran más sustentables por necesidad (reparar, reutilizar, comprar a granel) y algunos de hoy son mejores por la información y la tecnología (reciclaje, energías limpias). En esta práctica se entrevistó a tres generaciones de mi familia y comunidad, se capturaron sus respuestas en una página web y una inteligencia artificial las compara y propone conclusiones, que analizo con criterio propio.

## Objetivo

Distinguir los estilos de vida y de consumo de tres generaciones de mi entorno, construir sin programar una página web conectada a Make e IA para comparar sus respuestas y formular conclusiones propias sobre la sustentabilidad de esos hábitos.

## Desarrollo

| Actividad | Qué se hizo |
|---|---|
| 1. Cuestionario | Se prepararon 8 preguntas iguales para las tres personas (comida, ropa, electrónicos, transporte, basura, desechables, agua y qué se considera necesario para vivir bien). El abuelo/a y el padre/madre respondieron pensando en cuando tenían entre 18 y 25 años; el amigo/a respondió sobre el presente. |
| 2. Entrevistas | Se pidió permiso a cada persona, se anotaron las respuestas con detalle y se registró solo la edad, sin nombres ni otros datos personales. **[PENDIENTE]** |
| 3. Webhook | En Make se creó el escenario `Generaciones_Equipo1` con un **Custom webhook**. |
| 4. Página con IA | Se generó `index.html` (un solo archivo con CSS y JS): título y explicación, tres secciones (abuelo/a, padre/madre, amigo/a) con campo numérico "Edad" y 8 áreas de texto, botón "Comparar respuestas" con validación de campos, envío con `fetch` por `POST` y `URLSearchParams` con los campos `abuelo`, `padre` y `amigo`, mensaje "Analizando respuestas...", respuesta en texto plano con `white-space: pre-line`, mensaje de error y diseño responsive en tonos ambientales. |
| 5. Estructura de datos | Con **Run once** activo se envió una consulta y el webhook detectó los campos `abuelo`, `padre` y `amigo`. |
| 6. Make AI Agent | Conexión *Make's AI Provider* (sin Knowledge), Input con `{{1.abuelo}}`, `{{1.padre}}` y `{{1.amigo}}` e instrucciones que obligan a usar únicamente lo que dicen las respuestas y a responder en texto plano con formato fijo (comparación por tema, análisis y conclusiones). |
| 7. Respuesta | **Webhook response** con status 200, cuerpo = respuesta del agente y headers `Content-Type: text/plain; charset=utf-8` y `Access-Control-Allow-Origin: *`. Escenario guardado y activo con **Immediately as data arrives**. |
| 8. Publicar y analizar | `index.html` publicado con GitHub Pages; se capturaron las tres entrevistas reales, se leyó el análisis de la IA y se revisaron los créditos consumidos. **[PENDIENTE]** |

## Código

La carpeta [`codigo/`](codigo/) contiene [`index.html`](codigo/index.html) (publicado en GitHub Pages) y [`blueprint.json`](codigo/blueprint.json), el escenario de Make `Generaciones_Equipo1` exportado (Custom webhook → Make AI Agent → Webhook response).

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Estilos de vida entre generaciones</title>
<style>
  :root {
    --verde-oscuro: #1f4d2b;
    --verde: #2e7d46;
    --verde-claro: #e6f2e6;
    --tierra: #8a6d3b;
    --cielo: #dff0f7;
    --texto: #1d2b20;
  }
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: "Segoe UI", Roboto, Arial, sans-serif;
    color: var(--texto);
    background: linear-gradient(180deg, var(--cielo) 0%, var(--verde-claro) 100%);
    min-height: 100vh;
    line-height: 1.5;
  }
  header { background: var(--verde-oscuro); color: #fff; padding: 28px 16px; text-align: center; }
  header h1 { margin: 0 0 6px; font-size: clamp(1.6rem, 5vw, 2.4rem); }
  header p { margin: 0; opacity: .9; }
  main { max-width: 1100px; margin: 0 auto; padding: 20px 16px 40px; }
  .tarjeta {
    background: #fff;
    border-radius: 14px;
    padding: 20px;
    box-shadow: 0 4px 14px rgba(31, 77, 43, .15);
    margin-bottom: 20px;
    border-left: 6px solid var(--verde);
  }
  .tarjeta h2 { margin-top: 0; color: var(--verde-oscuro); font-size: 1.2rem; }
  .columnas { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
  .persona { background: #fff; border-radius: 14px; padding: 16px; box-shadow: 0 4px 14px rgba(31, 77, 43, .15); border-top: 6px solid var(--verde); }
  .persona h2 { margin: 0 0 12px; color: var(--verde-oscuro); font-size: 1.15rem; }
  .persona label { display: block; font-weight: 600; font-size: .9rem; margin: 12px 0 4px; }
  input[type="number"], textarea {
    width: 100%;
    font: inherit;
    padding: 8px 10px;
    border: 2px solid #b9d6bf;
    border-radius: 8px;
    color: var(--texto);
    background: #fcfffc;
  }
  textarea { min-height: 64px; resize: vertical; }
  input:focus, textarea:focus { outline: none; border-color: var(--verde); }
  .acciones { text-align: center; margin-top: 8px; }
  button {
    font-size: 1.05rem;
    padding: 12px 28px;
    border-radius: 10px;
    border: 2px solid var(--verde);
    background: var(--verde);
    color: #fff;
    font-weight: 700;
    cursor: pointer;
  }
  button:hover:not(:disabled) { background: var(--verde-oscuro); }
  button:disabled { opacity: .6; cursor: not-allowed; }
  #aviso { margin-top: 12px; color: #7a1f1f; font-weight: 600; min-height: 1.2em; }
  #resultado {
    margin-top: 18px;
    padding: 16px;
    background: var(--verde-claro);
    border: 2px dashed var(--verde);
    border-radius: 10px;
    white-space: pre-line;
    min-height: 60px;
    overflow-wrap: anywhere;
    text-align: left;
  }
  #resultado.error { background: #fbeaea; border-color: #b03a3a; color: #7a1f1f; }
  footer { text-align: center; font-size: .85rem; color: var(--tierra); padding: 0 16px 24px; }
  @media (max-width: 800px) {
    .columnas { grid-template-columns: 1fr; }
    button { width: 100%; }
  }
</style>
</head>
<body>
<header>
  <h1>Estilos de vida entre generaciones</h1>
  <p>Desarrollo Sustentable · TecNM Campus Mazatlán</p>
</header>

<main>
  <section class="tarjeta">
    <h2>¿De qué trata esta página?</h2>
    <p>
      Se entrevistó a tres generaciones sobre sus hábitos de consumo para compararlos: un abuelo o abuela,
      un padre o madre y un amigo o amiga. El abuelo y el padre responden pensando en cuando tenían entre
      18 y 25 años; el amigo responde sobre el presente. Al final, una inteligencia artificial compara
      las respuestas y propone conclusiones.
    </p>
  </section>

  <div class="columnas" id="columnas"></div>

  <div class="acciones">
    <button id="comparar" type="button">Comparar respuestas</button>
    <div id="aviso" role="alert"></div>
  </div>

  <section class="tarjeta" style="margin-top:20px">
    <h2>Análisis de la IA</h2>
    <div id="resultado" aria-live="polite"></div>
  </section>
</main>

<footer>Práctica 3.4 · Estilos de vida, consumo y estrategias de sustentabilidad.</footer>

<script>
  // PEGA AQUI LA URL DEL WEBHOOK DE MAKE
  const WEBHOOK_URL = "https://hook.us2.make.com/5eqv13elaer5x6qiguqqqdyljpcdarjo";

  const PREGUNTAS = [
    "¿Cómo se conseguía la comida en casa: mercado, tienda, súper o cultivo propio?",
    "¿Qué se hacía con la ropa o los zapatos viejos o rotos?",
    "¿Qué aparatos electrónicos tenías y cada cuánto los cambiabas?",
    "¿Cómo te transportabas a la escuela o al trabajo?",
    "¿Qué se hacía con la basura? ¿Se separaba o se reutilizaba algo?",
    "¿Usabas bolsas, botellas o envases desechables?",
    "¿Cómo se usaba y se cuidaba el agua en casa?",
    "¿Qué consideras necesario para vivir bien?"
  ];
  const PERSONAS = [
    { id: "abuelo", titulo: "Abuelo/a" },
    { id: "padre", titulo: "Padre/Madre" },
    { id: "amigo", titulo: "Amigo/a" }
  ];

  const columnas = document.getElementById("columnas");
  const boton = document.getElementById("comparar");
  const aviso = document.getElementById("aviso");
  const resultado = document.getElementById("resultado");

  // Construye las tres secciones con su campo de edad y las 8 preguntas
  PERSONAS.forEach(p => {
    const sec = document.createElement("section");
    sec.className = "persona";
    let html = '<h2>' + p.titulo + '</h2>' +
      '<label for="' + p.id + '-edad">Edad</label>' +
      '<input type="number" id="' + p.id + '-edad" min="1" max="120" inputmode="numeric">';
    PREGUNTAS.forEach((q, i) => {
      html += '<label for="' + p.id + '-' + (i + 1) + '">' + (i + 1) + '. ' + q + '</label>' +
        '<textarea id="' + p.id + '-' + (i + 1) + '"></textarea>';
    });
    sec.innerHTML = html;
    columnas.appendChild(sec);
  });

  function campoVacio(id) {
    return document.getElementById(id).value.trim() === "";
  }

  function textoDe(id) {
    let t = "Edad: " + document.getElementById(id + "-edad").value.trim() + "\n";
    PREGUNTAS.forEach((q, i) => {
      t += (i + 1) + ". " + q + ": " + document.getElementById(id + "-" + (i + 1)).value.trim() + "\n";
    });
    return t.trim();
  }

  boton.addEventListener("click", async () => {
    aviso.textContent = "";
    // Verifica que todos los campos estén llenos antes de enviar
    const faltan = PERSONAS.some(p => {
      if (campoVacio(p.id + "-edad")) return true;
      return PREGUNTAS.some((q, i) => campoVacio(p.id + "-" + (i + 1)));
    });
    if (faltan) {
      aviso.textContent = "Faltan campos por llenar. Completa la edad y las 8 respuestas de las tres personas.";
      return;
    }

    boton.disabled = true;
    resultado.className = "";
    resultado.textContent = "Analizando respuestas, esto puede tardar unos segundos...";
    try {
      const cuerpo = new URLSearchParams();
      cuerpo.append("abuelo", textoDe("abuelo"));
      cuerpo.append("padre", textoDe("padre"));
      cuerpo.append("amigo", textoDe("amigo"));
      const respuesta = await fetch(WEBHOOK_URL, { method: "POST", body: cuerpo });
      if (!respuesta.ok) throw new Error("HTTP " + respuesta.status);
      resultado.textContent = await respuesta.text();
    } catch (error) {
      resultado.className = "error";
      resultado.textContent = "No se pudo obtener el análisis, intenta de nuevo.";
    } finally {
      boton.disabled = false;
    }
  });
</script>
</body>
</html>
```

## Imágenes

**[PENDIENTE]** Aquí van las capturas: escenario completo y ordenado en Make, el Make AI Agent configurado, la página con las respuestas capturadas y el análisis de la IA, y el historial de ejecuciones con los créditos. Se guardan en la carpeta `imagenes/` y se enlazan con `![descripción](imagenes/archivo.png)`.

## Video

El enlace al video con la prueba en vivo está en [`video/enlace.txt`](video/enlace.txt). **[PENDIENTE]**

## Resultados

El PDF con lo que se aprendió en esta práctica irá en [`resultados/`](resultados/). **[PENDIENTE]**

### Comparación de las tres generaciones

**[PENDIENTE]** Tabla con la edad y un resumen de las respuestas de cada persona por tema (comida, ropa, electrónicos, transporte, basura, desechables, agua y vivir bien), junto con la comparación que devolvió la IA.

### Créditos consumidos

**[PENDIENTE]** Créditos que aparecen en el historial del escenario por cada análisis.

### Verificación del análisis de la IA

**[PENDIENTE]** Conclusiones de la IA con las que estoy de acuerdo y con las que no, y por qué.

## Preguntas de reflexión

**1. ¿Qué generación tiene el estilo de vida más sustentable según sus respuestas? ¿Fue por convicción o por necesidad?**
**[PENDIENTE]**

**2. ¿Qué cambios en la sociedad (publicidad, tecnología, ingreso, ciudad) explican las diferencias de consumo entre las tres generaciones?**
**[PENDIENTE]**

**3. ¿Están de acuerdo con el análisis de la IA? ¿Qué dejó fuera o qué interpretó mal?**
**[PENDIENTE]**

**4. ¿Qué relación tienen estos hábitos con los principios de la Carta de la Tierra o con la Agenda 21?**
**[PENDIENTE]**

**5. ¿Qué hábito de generaciones anteriores están dispuestos a retomar y cómo lo harían?**
**[PENDIENTE]**

## Conclusiones

**[PENDIENTE]**

## Fuente

Entrevistas realizadas a tres personas (abuelo/a, padre/madre y amigo/a) de mi familia y comunidad; análisis comparativo generado con Make AI Agent a partir de las respuestas capturadas.
