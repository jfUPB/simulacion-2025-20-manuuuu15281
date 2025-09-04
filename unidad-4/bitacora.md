# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> 
> Profe para mi obra decidí aplicar el concepto de **coordenadas polares** para modelar unas particulas tipo "orbitas" con un tipo de movimiento radial que tenga mucha naturalidad, finalmente para graficar pasé de polares a cartesianas. 

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> 
> De la unidad 3 utilicé el concepto de modelar la atracción gravitacional y en este caso las pasrticulas orbitantes están siendo atraidas por un tipo de "atractor" que está en el centro, este lo que hace es que hace que las trayectorias de esas particulas tiendan a curvarse hacia el centro. 

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> 
>  Utilicé el concepto de motion 101, esta vez un poco diferente ya que le añadimos un tipo de amortiguamiento (para la velocidad radial y angular, esto para evitar que se dispararan las velocidades y ayudar a estabilizar el sistema). En este caso no usé vectores sino escalares, así que pude implementar la suma normal sin necesidad del.add. El motion 101 en este caso lo apliqué a la velocidad angular y radial e implementé que el movimiento sea dependiente del tiempo ( deltaTime (dt)).
```js
// --- INTEGRACIÓN (Motion 101) ---
// Velocidades <- aceleraciones
this.vr += this.ar * dt;
this.vtheta += this.atheta * dt;

// Amortiguamiento proporcional al tiempo
this.vr *= (1 - radialDamp * dt);
this.vtheta *= (1 - angularDamp * dt);

// Posiciones <- velocidades
this.r += this.vr * dt;
this.theta += this.vtheta * dt;
```

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> 
> De la unidad 1 decidí utilizar el **perlin noise** porque necesitaba que mis particulas orbitantes tuvieran movimientos más fluidos y lo que modifica es: la **aceleración angular** que hace que el radio se expanda o contraiga suavemente y la **aceleración angular** que está induciendo variaciones en la velocidad de giro. Lo que logré implementando esto fueron trazos fluidos, con ensanchamientos/estrechamientos y cambios sutiles de curvatura, generando un patrón vivo y no repetitivo.

## ¿Cómo resolviste la interacción?
> 
>Para la interacción quise hacer una integración con la materia de visión artificial. Con el profe Antonio estamos viendo en clase un concepto de "frame difference" donde lo que me llamó la atención para implementar en esta obra interactiva es una mascara binaria que decidí adaptar a mi concepto de simulación: la mascara actuará como un borrador donde los pixeles donde se detecte movimiento eliminará los trazos que hayan.
>Lo que hace puntualmente es que compara el frame actual con el frame previo en escala de brillo, binariza por un umbral y genera una máscara de movimiento. Donde hay movimiento, se dibujan círculos borradores sobre la capa persistente, mapeando coordenadas de la cámara al lienzo.
>
> Algo por resaltar es que mi obra está diseñada para funcionar en un lugar donde haya poca iluminación (una sala oscura es el lugar indicado) ya que la interacción es muy sensible a la luz y la capta como un movimiento más. Adjunto video de cómo funciona la obra en una habitación completamente oscura.
> 
[Aquí el link al video que demuestra lo que digo :)](https://drive.google.com/file/d/1HVrcUwL1yIJ5rySaE3CEJfVegALZB_-7/view?usp=sharing)

## Enlace a la obra en el editor de p5.js

[Aquí está mi obra](https://editor.p5js.org/manuuuu15281/sketches/AnDJ1z7Eg)

## Código de la obra 

``` js

let layer;                 // capa de trazos persistentes
let capture, prevFrame;    // video y frame previo (para diferencia)
let prevReady = false;

const N = 300;             // número de "orbiters"
let movers = [];

let G = 9000;              // "constante gravitacional" (ajusta sensación)
let noiseAccel = 35;       // aceleración radial extra por Perlin (px/s^2)
let omegaAccel = 0.6;      // aceleración angular por Perlin (rad/s^2)
let noiseSpeed = 0.35;     // velocidad de avance del ruido (s^-1)

let radialDamp = 0.9;      // amortiguamiento radial (1/s)
let angularDamp = 0.7;     // amortiguamiento angular (1/s)

let maxRadius;             // límite de radio
let eraserSize = 60;       // tamaño base del borrador
let eraserOpacity = 3;    // opacidad del borrador (menor = más sutil)
let showEraserIndicator = true; // círculo fantasma del borrador

// Parámetros de detección de movimiento
let thresholdMovement = 80; // umbral para detectar movimiento general
let lastVideoTime = -1;
let movementMask;          // imagen binaria de movimiento
let movementSlider;        // control de sensibilidad
let opacitySlider;         // control de opacidad del borrador

function setup() {
  createCanvas(900, 600);
  pixelDensity(1);

  // Capa de dibujo persistente
  layer = createGraphics(width, height);
  layer.pixelDensity(1);
  layer.clear();
  layer.colorMode(HSB, 360, 100, 100, 100);
  layer.strokeWeight(1.6);

  // Video y frame previo (resolución reducida para mejor performance)
  capture = createCapture(VIDEO);
  capture.size(160, 120);
  capture.hide();
  prevFrame = createImage(160, 120);
  movementMask = createImage(160, 120);

  // Control de sensibilidad y opacidad
  movementSlider = createSlider(10, 80, thresholdMovement, 1);
  movementSlider.position(20, 20);
  movementSlider.style('width', '150px');
  
  opacitySlider = createSlider(5, 50, eraserOpacity, 1);
  opacitySlider.position(20, 50);
  opacitySlider.style('width', '150px');

  // Partículas / orbiters en base polar
  maxRadius = min(width, height) * 0.47;
  for (let i = 0; i < N; i++) {
    // radios variados y ángulos aleatorios
    let r0 = random(60, maxRadius);
    let th0 = random(TWO_PI);
    movers.push(new PolarMover(th0, r0, i));
  }

  // Fondo inicial sutil
  background(10);
}

// Clase con Motion 101 en polar
class PolarMover {
  constructor(theta, r, idx) {
    // Estado polar
    this.theta = theta;            // ángulo (rad)
    this.r = r;                    // radio (px)
    this.vtheta = random(-0.6, 0.6); // vel. angular (rad/s)
    this.vr = random(-10, 10);     // vel. radial (px/s)

    // Aceleraciones (se recalculan cada frame)
    this.atheta = 0;
    this.ar = 0;

    // Ruido
    this.no = random(1000);        // offset en Perlin
    this.idx = idx;

    // Posición cartesiana previa (para trazar líneas suaves)
    this.prev = this.cartesian();
  }

  // Conversión polar -> cartesiano (desde el centro del lienzo)
  cartesian() {
    const v = p5.Vector.fromAngle(this.theta, this.r);
    return createVector(width / 2 + v.x, height / 2 + v.y);
  }

  step(dt) {
    // --- FUERZAS / ACELERACIONES ---

    // 1) Gravedad central (radial hacia el centro)
    // a_r = -G / (r^2 + eps)  (suavizado para evitar explosiones numéricas)
    const eps = 1500; // suavizado
    this.ar = -G / (this.r * this.r + eps);

    // 2) Ruido Perlin sobre el radio (aceleración radial "respiración")
    const nr = noise(this.no);
    const arNoise = map(nr, 0, 1, -noiseAccel, noiseAccel);
    this.ar += arNoise;

    // 3) Pequeño ruido angular (aceleración angular)
    const na = noise(this.no + 777.0);
    this.atheta = map(na, 0, 1, -omegaAccel, omegaAccel);

    // --- INTEGRACIÓN (Motion 101) ---

    // Velocidades <- aceleraciones
    this.vr += this.ar * dt;
    this.vtheta += this.atheta * dt;

    // Amortiguamiento proporcional al tiempo
    this.vr *= (1 - radialDamp * dt);
    this.vtheta *= (1 - angularDamp * dt);

    // Posiciones <- velocidades
    this.r += this.vr * dt;
    this.theta += this.vtheta * dt;

    // Límites suaves para r
    this.r = constrain(this.r, 18, maxRadius);

    // Avanzar el "tiempo" del ruido
    this.no += noiseSpeed * dt;

    // --- DIBUJO EN LA CAPA PERSISTENTE ---
    const p = this.cartesian();
    // Color por índice y radio (paleta fría/néon)
    const hue = map(this.idx, 0, N, 180, 320);
    const bri = map(this.r, 18, maxRadius, 40, 100);
    layer.stroke(hue, 80, bri, 22); // alpha bajo para acumulación elegante
    layer.line(this.prev.x, this.prev.y, p.x, p.y);
    this.prev = p;
  }
}

// Función auxiliar para obtener brillo de un píxel
function getPixelBrightness(x, y, sourcePixels, sourceWidth) {
  const index = (y * sourceWidth + x) * 4;
  const r = sourcePixels[index];
  const g = sourcePixels[index + 1];
  const b = sourcePixels[index + 2];
  return (r * 0.299 + g * 0.587 + b * 0.114);
}

// Detección de movimiento y aplicación del borrador
function applyComputerVisionEraser() {
  capture.loadPixels();
  if (capture.pixels.length === 0) return;

  thresholdMovement = movementSlider.value();
  const currentTime = capture.time();
  
  // Verificar si hay un nuevo frame
  if (currentTime === lastVideoTime) return;

  // Primer frame: inicializar referencia
  if (!prevReady) {
    prevFrame.copy(capture, 0, 0, capture.width, capture.height, 0, 0, prevFrame.width, prevFrame.height);
    prevFrame.loadPixels();
    prevReady = true;
    lastVideoTime = currentTime;
    return;
  }

  prevFrame.loadPixels();
  movementMask.loadPixels();

  let w = capture.width;
  let h = capture.height;
  let movementPixels = 0;
  let totalPixels = w * h;

  // Crear máscara binaria de movimiento
  for (let y = 0; y < h; y++) {
    for (let x = 0; x < w; x++) {
      const curBrightness = getPixelBrightness(x, y, capture.pixels, w);
      const prevBrightness = getPixelBrightness(x, y, prevFrame.pixels, w);
      const diff = Math.abs(curBrightness - prevBrightness);

      const idx = (y * w + x) * 4;
      
      if (diff > thresholdMovement) {
        // Píxel con movimiento detectado
        movementMask.pixels[idx] = 255;     // R
        movementMask.pixels[idx + 1] = 255; // G
        movementMask.pixels[idx + 2] = 255; // B
        movementMask.pixels[idx + 3] = 50; // A
        movementPixels++;
      } else {
        // Sin movimiento
        movementMask.pixels[idx] = 0;       // R
        movementMask.pixels[idx + 1] = 0;   // G
        movementMask.pixels[idx + 2] = 0;   // B
        movementMask.pixels[idx + 3] = 50; // A
      }
    }
  }

  movementMask.updatePixels();

  // Aplicar borrado basado en la máscara
  if (movementPixels > 20) { // umbral más bajo para detectar cualquier movimiento
    applyMaskBasedErasure();
  }

  // Actualizar frame previo
  prevFrame.copy(capture, 0, 0, w, h, 0, 0, prevFrame.width, prevFrame.height);
  prevFrame.updatePixels();
  lastVideoTime = currentTime;
}

// Aplicar borrado sutil basado en la máscara binaria
function applyMaskBasedErasure() {
  movementMask.loadPixels();
  
  // Borrado con transparencia
  layer.erase(eraserOpacity); // opacidad controlable del borrado
  
  // Recorrer la máscara y borrar suavemente donde hay movimiento
  for (let y = 0; y < movementMask.height; y += 3) { // paso de 3 para performance y suavidad
    for (let x = 0; x < movementMask.width; x += 3) {
      const idx = (y * movementMask.width + x) * 4;
      
      if (movementMask.pixels[idx] > 128) { // píxel con movimiento detectado
        // Mapear coordenadas del video al canvas
        let canvasX = map(x, 0, movementMask.width, 0, width);
        let canvasY = map(y, 0, movementMask.height, 0, height);
        
        // Espejo horizontal para sensación natural
        canvasX = width - canvasX;
        
        // Borrado sutil con círculo pequeño
        layer.circle(canvasX, canvasY, eraserSize * 0.4);
      }
    }
  }
  
  layer.noErase();
}

function draw() {
  // Lienzo base muy oscuro (para ver la capa con brillo)
  background(255);

  const dt = deltaTime / 1000;

  // Actualizar partículas
  for (const m of movers) {
    m.step(dt);
  }

  // Aplicar borrador por visión artificial
  applyComputerVisionEraser();

  // Componer capa de trazos
  image(layer, 0, 0);

  // Opcional: dibujar un pequeño indicador del "pozo" gravitacional
  noFill();
  stroke(255, 20);
  circle(width / 2, height / 2, 24);

  // Obtener valores de los controles
  thresholdMovement = movementSlider.value();
  eraserOpacity = opacitySlider.value();

  // Mostrar info de controles
  fill(0, 180);
  noStroke();
  textSize(12);
  text(`Sensibilidad: ${thresholdMovement}`, 180, 35);
  text(`Opacidad borrador: ${eraserOpacity}%`, 180, 65);
  
}

```

## Captura de pantalla representativa

<img width="925" height="707" alt="image" src="https://github.com/user-attachments/assets/1fd71d72-ce6c-44a5-8c0b-15a61b088573" />






