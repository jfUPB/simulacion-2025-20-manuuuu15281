![GIF Simulation 2](https://github.com/user-attachments/assets/dcda3f06-18a3-4671-8368-0267ae5b46eb)# Evidencias de la unidad 6

### ACTIVIDAD 1

**Obra 1: Fragments of thought**

<img width="743" height="588" alt="image" src="https://github.com/user-attachments/assets/e14d455d-2ff4-44d4-9336-2b86d56480db" />

Esta obra de Tyler Hobbs me llamó mucho la atención porque luego de ver su explicación de los campos de flujo primero mostrando la matriz de fuerzas, luego mostró en blancos y negros cómo se ve y finalmente cuando agrega el color quedé muy fascinada con el resultado. También me gustó mucho que se puede experimentar de muchas manera con el movimiento del campo de flujo cómo jugar con el mismo o hacer que genere ek movimiento a algo. Me gustó también la manera en que se veía un ejemplo para mostrar el GRID (imagen a continuación):

<img width="513" height="541" alt="image" src="https://github.com/user-attachments/assets/8f77e2a3-e5f6-43ec-b81b-416e2041f19b" />

Me gusta mucho cómo el mismo campo de flujo forman ciertas figuras que se perciben muy bien visualmente, el efecto visual que causa es espectacular. 

**Obra 2: Mirror Removal**

<img width="1874" height="505" alt="image" src="https://github.com/user-attachments/assets/1a424e27-e2fc-4e20-ba28-3a646f67f4aa" />

Esta definitivamente es mi favorita, la manera en que cambia según los colores, las formas generadas y ese reflejo tipo espejo me sorprendió bastante. 


De ambas obras me inspira mucha vida, creo que eso lo logra por el uso de los colores. Además me gusta las formas que logra con los campos de flujo, más que todo en la segunda obra, definitivamente son muy disruptivos. 

### ACTIVIDAD 2


**¿Qué es una fuerza de dirección (steering force)?**

Después de investigar, una fuerza de dirección es la aceleración que aplicas al "agente" para corregir su velocidad actual hacia una velocidad deseada. Este método es muy útil porque evita giros bruscos y por el contrario ocurre suavemente. 


**¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?**

La diferencia más grande que veo es que las otras fuerzas que hemos modelado provienen de leyes del entorno y del estado; no buscan llegar a una meta (cómo si lo hace steering force), solo actúan según ecuaciones físicas. 

**¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?**

Después de comprender cómo funciona el programa de Craig Reynolds, pude llegar a que él mostró que el “vuelo en bandada” se puede simular si cada agente aplica tres steering behaviors: **separación, alineación y cohesión** cada uno produciendo una fuerza de dirección. Esas fuerzas se ponderan, se suman y se limitan y el resultado se aplica como aceleración del agente; de ahí sale el movimiento grupal creíble y orgánico. 

### ACTIVIDAD 3

El campo de flujo se guarda dentro de un arreglo 2D. El tamaño de cada cuadrito lo define la resolución r, por eso el número de columnas es width / r y el de filas height / r. En cada celda se guarda un p5.Vector que marca la dirección del “viento” en ese punto.

Para crear estas direcciones, en init() se recorre toda la rejilla y, para cada celda (i, j), se toma un valor de ruido Perlin(que cambia suavemente), se transforma ese número de [0, 1] a un ángulo entre 0 y 2π, y con ese ángulo se construye el vector (p5.Vector.fromAngle).Además, noiseSeed(random(10000)) “rebaraja” el ruido perlin cada vez que llamas a init(), generando un campo nuevo. 

La manera en que un agente utiliza el campo de flujo para calcular su fuerza de direeción es siguiendo los siguientes pasos:

1. El agente mira en qué cuadrito del campo está.

2. Toma la flechita (vector) de ese cuadrito y la convierte en su dirección/velocidad deseada (ajustada a su velocidad máxima).

3. Calcula la fuerza de dirección como: velocidad deseada menos velocidad actual (esto le dice cuánto girar y acelerar).

4. Limita esa fuerza a su máxima fuerza permitida.

5. Aplica esa fuerza: con eso cambia su aceleración y va corrigiendo la trayectoria para seguir el flujo.

Los parámetros clave que percibí: 

- Cantidad de agentes
- La escala del ruido perlin
- Posición inicial de los agentes (por ahora es aleatoria pero podría ser modificado para que funcione de otra manera)
- Resolución
- MaxSpeed
- MaxForce

Decidí modificar la resoulción, el maxspeed, el maxforce y la cantidad de particulas que se generan. 

Primero lo hice sin modificar el maxforce, solo los demás parámetros mencionados y obtuve unos vehicles mucho más grandes por colocar la resolución = 9, también aumenté la cantidad de agentes de 120 a 320 y el maxSpeed lo cambié de ms a 10. Y se ve de la siguiente manera:

https://github.com/user-attachments/assets/5adf518b-3904-4035-afe4-a4edaf0ae506

Pude observar como cambio la cantidad de vehicles generados (son muchos más ahora), también cambió la velocidad con la que se mueven los agentes el resolution cambió el tamaño de los agentes. 

Luego, en otro intento modifiqué además de los que ya mencioné anteriormente el maxForce y lo coloqué en 0, lo que ocurrió es que no hubo movimiento en el canvas, los vehicles se quedaron estáticos. La siguiente imagén lo muestra:

<img width="814" height="306" alt="image" src="https://github.com/user-attachments/assets/add187e6-5a80-4974-a4c2-f25294dee382" />

Los tramos de código que modifiqué son los siguientes: 

**En la clase vehicle.js**

```js
class Vehicle {
  constructor(x, y, ms, mf) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(0, 0);
    this.r = 9; //Modificado
    this.maxspeed = 10; //Modificado
    this.maxforce = 0;//Modificado
  }
```

**En el sketch.js**

```js
function setup() {
  let text = createP(
    "Hit space bar to toggle debugging lines.<br>Click the mouse to generate a new flow field."
  );

  createCanvas(640, 240);
  flowfield = new FlowField(20);
  // Modificado 
  for (let i = 0; i < 320; i++) {
    vehicles.push(
      new Vehicle(random(width), random(height), random(2, 5), random(0.1, 0.5))
    );
  }
}
```

### ACTIVIDAD 4

Luego de analizar el código y de comprender el concepto de flucking, puedo decir de cada fase lo siguiente: 

**Separación (respeta el espacio personal)**

Cada boid trata de no pegarse demasiado a los demás. Si siente que alguien está “invadiendo” su burbuja, se aparta un poco. Esto evita choques.

**Alineación (sigue el rumbo del grupo cercano)**

Observa hacia dónde se mueven sus vecinos inmediatos y ajusta su dirección para ir más o menos en la misma.

**Cohesión (mantente con la banda)**

Tiende a acercarse al centro del pequeño grupo que tiene cerca. No se lanza encima, solo se aproxima para no quedarse solitp jeje.

Parámetros clave detectados: 

- maxspeed
- maxforce
- r
- desiredSeparation
- neighborDistance
- cantidad de boids
- punto de aparición
- Velocidad inicial


**Modificaciones al código**

Modifiqué maxspeed, r, desiredSeparation, neighborDistance y cantidad de boids. Lo que noté al ir haciendo los cambios es que cuando modifiqué r el tamaño de los boids aumentó (estaba en 3 y lo coloqué en 7), el maxspeed al modificarlo cambió la velocidad de movimiento de los boids y en este caso lo relenticé (lo puse primero en 0 y no se movían, luego le puse 0.9 y ya se movían lentico). Cuando modifiqué el desiredSeparation los boids se dispersaban más porque lo aumenté en 25, para neighborDistance lo reduje en 25 y ahora están más cerca pero llega un punto en el que los últimos dos parámetros no encuentran coherencia entre sí y el enjambre deja de moverse sino que los boids tratando de cumplir con los requisitos se quedan dando vueltas en un mismo punto cada uno

```js
class Boid {
  constructor(x, y) {
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 1));
    this.position = createVector(x, y);
    this.r = 1.0;
    this.maxspeed = 0.9; // Maximum speed Modificada
    this.maxforce = 0.05; // Maximum steering force Modificada
  }
```

```js
separate(boids) {
    let desiredSeparation = 50; //Modificado
    let steer = createVector(0, 0);
    let count = 0;

    ...

```

```js
align(boids) {
    let neighborDistance = 25;
    let sum = createVector(0, 0);
    let count = 0;

...
```

![GIF Simulation 2](https://github.com/user-attachments/assets/ef777a71-2079-4f47-a6a5-788d29d45c74)


### ACTIVIDAD 5: APPLY

Mi idea principal era más bien clara y casi toda se llevó a cabo: 

- Quería que el campo de flujo se moviera o armara algún tipo de forma. Esto no fue tan fácil  de implementar entonces lo descarté después de varios intentos.
- Quería si o si incluir interacción con visión artificial. Entonces implementé uno de los últimos temas que vimos con Antonio: Machine Learning con Teachable Machine. Esto fue un plus para tener más agilidad a la hora de modificar los visuales en tiempo real.
- Quería incluir la presencia de imágenes así que utilicé de referencia un evento de orbitas sonoras del planetario donde el Vj tenia la pantalla fragmentada y la imagen la distorcionaba de diferentes maneras logrando efectos visuales muy cool. Así que me atreví a intentar algo similar, logré hacer algo con una imagen subida previamente pero me parecio funcional porque si hubiese tenido más tiempito para trabajarle a esto lo que hubiese hecho es tener una reserva de imagenes previamente guardadas para usar en diferentes momentos de la canción.
- Para elegir el tema musical hice una experimentación sonora donde elegí una canción que me gustara su composición y colorimetría. En esa busqueda me topé con "Slow Bloom" de EMBRZ que fue el tema que finalmente decidí interpretar.
- Por último, implementé la parametrización de muchos elementos visuales que se pudieran manejar por medio de sliders, botones, teclas y por supuesto reconocimiento de imagenes con ayuda de algoritmos entrenados en teachable machine.
- También algo super importante que añadí es un CRONOMETRO que me ayuda a estar pendiente del tiempo de la canción y me ayuda a seguir la partitura al pie de la letra.


[Link para acceder al código](https://editor.p5js.org/manuuuu15281/sketches/hxJStkRnnF)

https://github.com/user-attachments/assets/44dc01c6-c70e-40e5-bc5a-5e880837c9e2

**Código Sketch.js**

```js
// ==================== GLOBAL VARIABLES ====================
let flowfield;
let lines = [];
let repellerLines = [];
let debug = false;
let song;
let amplitude;
let fft;

let classifier;
let video;
let modelOut = "Ninguno";
const imageModelURL = "https://teachablemachine.withgoogle.com/models/mNUSFLsHg/";

let colorPalettes = {
  warm: { min: 0, max: 60 },
  cool: { min: 180, max: 270 }
};
let currentPalette = colorPalettes.warm;

let params = {
  numLines: 150,
  speed: 2,
  lineLength: 50,
  lineWidth: 3,
  bgOpacity: 50
};

let pulseActive = false;

// Variables para partículas temporales (efecto B)
let temporaryParticles = [];

// Variables para el efecto de parpadeo (efecto Pinza)
let flickerActive = false;
let flickerAlpha = 255;

let allowAISpeed = false;

// Timer variables
let timerStartTime = 0;
let timerPaused = true;
let timerElapsed = 0;

// Image grid variables
let gridImage;
let showGrid = false;
let gridAlpha = 0;
let gridTargetAlpha = 0;
let gridCols = 6;
let gridRows = 4;
let gridCellDelay = [];

let canvasWidth, canvasHeight;

// ==================== TEMPORARY PARTICLE CLASS ====================
class TemporaryParticle {
  constructor(x, y, colorPalette) {
    this.position = createVector(x, y);
    this.velocity = p5.Vector.random2D();
    this.velocity.mult(random(1, 3));
    this.lifespan = 3000; // 3 segundos en milisegundos
    this.creationTime = millis();
    this.colorPalette = colorPalette;
    this.hue = random(colorPalette.min, colorPalette.max);
    this.size = random(5, 15);
  }

  update() {
    this.position.add(this.velocity);
    this.velocity.mult(0.98); // Fricción
  }

  isAlive() {
    return (millis() - this.creationTime) < this.lifespan;
  }

  show() {
    let age = millis() - this.creationTime;
    let alphaValue = map(age, 0, this.lifespan, 255, 0);
    
    push();
    colorMode(HSB, 360, 100, 100, 255);
    fill(this.hue, 80, 100, alphaValue);
    noStroke();
    ellipse(this.position.x, this.position.y, this.size);
    pop();
  }
}

// ==================== FLOW FIELD CLASS ====================
class FlowField {
  constructor(r) {
    this.resolution = r;
    this.cols = Math.floor(width / this.resolution);
    this.rows = Math.floor(height / this.resolution);
    this.field = new Array(this.cols);
    for (let i = 0; i < this.cols; i++) {
      this.field[i] = new Array(this.rows);
    }
    this.noiseScale = 0.1;
    this.timeOffset = 0;
    this.forceDown = false;
    this.init();
  }

  init() {
    noiseSeed(random(10000));
    this.updateField();
  }

  updateField() {
    let xoff = 0;
    for (let i = 0; i < this.cols; i++) {
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        let angle;
        if (this.forceDown) {
          angle = HALF_PI;
        } else {
          angle = map(noise(xoff, yoff, this.timeOffset), 0, 1, 0, TWO_PI * 2);
        }
        this.field[i][j] = p5.Vector.fromAngle(angle);
        yoff += this.noiseScale;
      }
      xoff += this.noiseScale;
    }
  }

  animate(speed) {
    this.timeOffset += speed * 0.001;
    this.updateField();
  }

  show() {
    for (let i = 0; i < this.cols; i++) {
      for (let j = 0; j < this.rows; j++) {
        let w = width / this.cols;
        let h = height / this.rows;
        let v = this.field[i][j].copy();
        v.setMag(w * 0.5);
        let x = i * w + w / 2;
        let y = j * h + h / 2;
        stroke(100, 100, 150, 150);
        strokeWeight(1);
        push();
        translate(x, y);
        rotate(v.heading());
        line(0, 0, w * 0.5, 0);
        pop();
      }
    }
  }

  lookup(position) {
    let column = constrain(Math.floor(position.x / this.resolution), 0, this.cols - 1);
    let row = constrain(Math.floor(position.y / this.resolution), 0, this.rows - 1);
    return this.field[column][row].copy();
  }
}

// ==================== CONTINUOUS LINE PARTICLE CLASS ====================
class ContinuousLine {
  constructor(x, y, colorPalette, isRepeller = false) {
    this.segments = [];
    this.maxSegments = 50;
    this.segments.push(createVector(x, y));

    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    this.maxspeed = 3;
    this.maxforce = 0.3;

    this.colorPalette = colorPalette;
    this.hue = random(this.colorPalette.min, this.colorPalette.max);
    this.strokeW = 3;

    this.pulsePhase = random(TWO_PI);
    this.pulseSpeed = 0.1;

    this.trail = [];
    this.maxTrail = 15;

    this.isRepeller = isRepeller;
    this.repelRadius = 80;
  }

  get position() {
    return this.segments[this.segments.length - 1];
  }

  follow(flow) {
    let desired = flow.lookup(this.position);
    desired.mult(this.maxspeed);
    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce);
    this.applyForce(steer);
  }

  repel(otherLines) {
    if (!this.isRepeller) return;

    for (let line of otherLines) {
      if (line === this || line.isRepeller) continue;

      let d = dist(this.position.x, this.position.y, line.position.x, line.position.y);

      if (d < this.repelRadius && d > 0) {
        let force = p5.Vector.sub(line.position, this.position);
        force.normalize();
        force.mult(map(d, 0, this.repelRadius, 5, 0));
        line.applyForce(force);
      }
    }
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.velocity.limit(this.maxspeed);

    let newPos = p5.Vector.add(this.position, this.velocity);
    this.segments.push(newPos);

    this.trail.push({ pos: newPos.copy(), life: 1.0 });

    if (this.segments.length > this.maxSegments) {
      this.segments.shift();
    }

    for (let i = this.trail.length - 1; i >= 0; i--) {
      this.trail[i].life -= 0.05;
      if (this.trail[i].life <= 0) this.trail.splice(i, 1);
    }
    if (this.trail.length > this.maxTrail) this.trail.shift();

    this.acceleration.mult(0);
    this.pulsePhase += this.pulseSpeed;
  }

  borders() {
    let head = this.position;
    let wrapped = false;

    if (head.x < 0) { head.x = width; wrapped = true; }
    if (head.y < 0) { head.y = height; wrapped = true; }
    if (head.x > width) { head.x = 0; wrapped = true; }
    if (head.y > height) { head.y = 0; wrapped = true; }

    if (wrapped) this.segments = [head.copy()];
  }

  show(audioLevel = 0, pulseActive = false) {
    // Trail
    for (let t of this.trail) {
      push();
      colorMode(HSB, 360, 100, 100, 255);
      if (this.isRepeller) {
        fill(0, 0, 100, t.life * 150);
      } else {
        fill(this.hue, 60, 100, t.life * 150);
      }
      noStroke();
      ellipse(t.pos.x, t.pos.y, 8 * t.life, 8 * t.life);
      pop();
    }

    // Pulsación
    let pulseSize = 1;
    let pulseHueShift = 0;
    if (pulseActive) {
      pulseSize = 1 + sin(this.pulsePhase) * 0.5;
      pulseHueShift = sin(this.pulsePhase) * 30;
    }

    // Línea
    if (this.segments.length > 1) {
      push();
      noFill();

      if (this.isRepeller) {
        stroke(0);
        strokeWeight(5 * pulseSize);
      } else {
        colorMode(HSB, 360, 100, 100, 255);
        let brightness = map(audioLevel, 0, 1, 60, 100);
        let currentHue = (this.hue + pulseHueShift + 360) % 360;
        stroke(currentHue, 80, brightness, 220);
        strokeWeight(this.strokeW * (1 + audioLevel * 0.5) * pulseSize);
      }

      strokeCap(ROUND);
      strokeJoin(ROUND);

      beginShape();
      for (let i = 0; i < this.segments.length; i++) {
        let seg = this.segments[i];
        curveVertex(seg.x, seg.y);
      }
      endShape();

      pop();
    }
  }

  updateColor(palette) {
    if (!this.isRepeller) {
      this.colorPalette = palette;
      this.hue = random(palette.min, palette.max);
    }
  }
}

// ==================== P5.JS PRELOAD ====================
function preload() {
  classifier = ml5.imageClassifier(imageModelURL + "model.json");
}

// ==================== P5.JS SETUP ====================
function setup() {
  let container = document.getElementById('canvasContainer');
  canvasWidth = container.offsetWidth;
  canvasHeight = container.offsetHeight;

  let canvas = createCanvas(canvasWidth, canvasHeight);
  canvas.parent('canvasContainer');

  colorMode(HSB, 360, 100, 100, 255);

  video = createCapture(VIDEO);
  video.size(160, 120);
  video.hide();

  classifyVideo();

  amplitude = new p5.Amplitude();
  fft = new p5.FFT();

  flowfield = new FlowField(20);

  for (let i = 0; i < params.numLines; i++) {
    lines.push(new ContinuousLine(random(width), random(height), currentPalette));
  }

  initGridDelays();

  setTimeout(setupControls, 100);
}

// ==================== P5.JS DRAW ====================
function draw() {
  const bgA = map(params.bgOpacity, 0, 100, 0, 255);
  background(0, 0, 0, bgA);

  drawImageGrid();

  let level = amplitude.getLevel();
  fft.analyze();
  let bass = fft.getEnergy("bass");
  let treble = fft.getEnergy("treble");

  flowfield.animate(params.speed);

  if (debug) flowfield.show();

  applyAIEffects(bass, treble, level);

  // Aplicar efecto de parpadeo si está activo
  if (flickerActive) {
    flickerAlpha = floor(random(2)) * 255;
    push();
    fill(0, 0, 0, 255 - flickerAlpha);
    rect(0, 0, width, height);
    pop();
  } else {
    flickerAlpha = 255;
  }

  // Actualizar y mostrar partículas temporales
  for (let i = temporaryParticles.length - 1; i >= 0; i--) {
    temporaryParticles[i].update();
    temporaryParticles[i].show();
    
    if (!temporaryParticles[i].isAlive()) {
      temporaryParticles.splice(i, 1);
    }
  }

  // Repelentes
  for (let repeller of repellerLines) {
    repeller.follow(flowfield);
    repeller.update();
    repeller.borders();
    repeller.maxspeed = params.speed;
    repeller.repel(lines);
    repeller.show(level, pulseActive);
  }

  // Líneas normales
  for (let line of lines) {
    line.follow(flowfield);
    line.update();
    line.borders();
    line.maxspeed = params.speed;
    line.maxSegments = params.lineLength;
    line.strokeW = params.lineWidth;
    line.show(level, pulseActive);
  }

  // Video de referencia
  push();
  tint(255, 150);
  image(video, width - 180, height - 150, 170, 130);
  noFill();
  stroke(255, 200);
  strokeWeight(2);
  rect(width - 180, height - 150, 170, 130);
  pop();

  updateTimer();
}

// ==================== IA EFFECTS ====================
function applyAIEffects(bass, treble, level) {
  if (modelOut === "B") {
    // NUEVO: Generar partículas desde las líneas existentes
    if (frameCount % 5 === 0) { // Cada 5 frames
      for (let line of lines) {
        // Generar 2-3 partículas por línea
        let numParticles = floor(random(2, 4));
        for (let i = 0; i < numParticles; i++) {
          temporaryParticles.push(
            new TemporaryParticle(
              line.position.x,
              line.position.y,
              currentPalette
            )
          );
        }
      }
    }
    
    flowfield.forceDown = false;
    flickerActive = false;

  } else if (modelOut === "Pinza") {
    // NUEVO: Efecto de parpadeo en toda la pantalla
    flickerActive = true;
    flowfield.forceDown = false;
    flowfield.noiseScale = 0.1;

  } else if (modelOut === "Labial") {
    currentPalette = colorPalettes.warm;
    flowfield.noiseScale = 0.05;
    flowfield.forceDown = false;
    if (allowAISpeed) params.speed = 1.5;

    if (frameCount % 60 === 0) {
      for (let line of lines) line.updateColor(currentPalette);
    }
    if (repellerLines.length > 0 && frameCount % 120 === 0) repellerLines.shift();
    
    flickerActive = false;

  } else {
    flowfield.forceDown = false;
    flowfield.noiseScale = 0.1;
    if (repellerLines.length > 0 && frameCount % 90 === 0) repellerLines.shift();
    flickerActive = false;
  }
}

// ==================== IMAGE GRID FUNCTIONS ====================
function initGridDelays() {
  gridCellDelay = [];
  for (let i = 0; i < gridCols * gridRows; i++) {
    gridCellDelay.push(random(0, 100));
  }
}

function drawImageGrid() {
  if (!gridImage) return;

  gridAlpha = lerp(gridAlpha, gridTargetAlpha, 0.05);
  if (gridAlpha < 1) return;

  push();
  let cellW = width / gridCols;
  let cellH = height / gridRows;

  for (let i = 0; i < gridCols; i++) {
    for (let j = 0; j < gridRows; j++) {
      let index = i + j * gridCols;
      let delay = gridCellDelay[index];

      let phase = (frameCount + delay) % 100;
      let cellAlpha = map(sin(phase * 0.1), -1, 1, 20, 100);

      tint(255, cellAlpha * (gridAlpha / 255));
      image(gridImage, i * cellW, j * cellH, cellW, cellH);
    }
  }
  pop();
}

// ==================== TIMER FUNCTIONS ====================
function updateTimer() {
  if (!timerPaused) {
    timerElapsed = millis() - timerStartTime;
  }

  let minutes = floor(timerElapsed / 60000);
  let seconds = floor((timerElapsed % 60000) / 1000);
  let centiseconds = floor((timerElapsed % 1000) / 10);

  let timeString = nf(minutes, 2) + ':' + nf(seconds, 2) + '.' + nf(centiseconds, 2);
  document.getElementById('timer').textContent = timeString;
}

function togglePlayPause() {
  if (song) {
    if (timerPaused) {
      userStartAudio();
      song.play();
      if (timerElapsed === 0) {
        timerStartTime = millis();
      } else {
        timerStartTime = millis() - timerElapsed;
      }
      timerPaused = false;
    } else {
      song.pause();
      timerPaused = true;
    }
  }
}

// ==================== ML5 CLASSIFICATION ====================
function classifyVideo() {
  classifier.classify(video, gotResult);
}

function gotResult(error, results) {
  if (error) {
    console.error(error);
    return;
  }
  modelOut = results[0].label;
  document.getElementById('aiLabel').textContent = modelOut || "Ninguno";
  classifyVideo();
}

// ==================== CONTROLS SETUP ====================
function setupControls() {
  const $ = (id) => document.getElementById(id);

  const audioFile = $('audioFile');
  if (audioFile) {
    audioFile.addEventListener('change', (e) => {
      if (e.target.files[0]) {
        if (song) song.stop();
        song = loadSound(URL.createObjectURL(e.target.files[0]), () => {
          amplitude.setInput(song);
          fft.setInput(song);
          timerElapsed = 0;
          timerPaused = true;
        });
      }
    });
  }

  const imageFile = $('imageFile');
  if (imageFile) {
    imageFile.addEventListener('change', (e) => {
      if (e.target.files[0]) {
        loadImage(URL.createObjectURL(e.target.files[0]), (img) => {
          gridImage = img;
        });
      }
    });
  }

  $('numLinesSlider')?.addEventListener('input', (e) => {
    params.numLines = parseInt(e.target.value);
    $('numLines').textContent = params.numLines;

    while (lines.length < params.numLines) {
      lines.push(new ContinuousLine(random(width), random(height), currentPalette));
    }
    while (lines.length > params.numLines) lines.pop();
  });

  $('speedSlider')?.addEventListener('input', (e) => {
    params.speed = parseFloat(e.target.value);
    $('speed').textContent = params.speed;
  });

  $('lineLengthSlider')?.addEventListener('input', (e) => {
    params.lineLength = parseFloat(e.target.value);
    $('lineLength').textContent = params.lineLength;
  });

  $('lineWidthSlider')?.addEventListener('input', (e) => {
    params.lineWidth = parseFloat(e.target.value);
    $('lineWidth').textContent = params.lineWidth;
  });

  $('bgOpacitySlider')?.addEventListener('input', (e) => {
    params.bgOpacity = parseFloat(e.target.value);
    $('bgOpacity').textContent = params.bgOpacity;
  });

  $('toggleDebugBtn')?.addEventListener('click', () => (debug = !debug));
  $('regenerateBtn')?.addEventListener('click', () => flowfield.init());
}

// ==================== KEYBOARD CONTROLS ====================
function keyPressed() {
  if (key === ' ') debug = !debug;

  if (key === 'r' || key === 'R') {
    lines = [];
    repellerLines = [];
    for (let i = 0; i < params.numLines; i++) {
      lines.push(new ContinuousLine(random(width), random(height), currentPalette));
    }
  }

  if (key === 'e' || key === 'E') {
    pulseActive = !pulseActive;
  }

  if (key === 'w' || key === 'W') {
    currentPalette = (currentPalette === colorPalettes.warm) ? colorPalettes.cool : colorPalettes.warm;
    for (let line of lines) line.updateColor(currentPalette);
  }

  if (key === 'q' || key === 'Q') {
    showGrid = !showGrid;
    gridTargetAlpha = showGrid ? 255 : 0;
  }

  if (key === 'p' || key === 'P') {
    togglePlayPause();
  }
}

// ==================== MOUSE CONTROLS ====================
function mousePressed() {
  flowfield.init();
}

function windowResized() {
  let container = document.getElementById('canvasContainer');
  canvasWidth = container.offsetWidth;
  canvasHeight = container.offsetHeight;
  resizeCanvas(canvasWidth, canvasHeight);
  flowfield = new FlowField(20);
  initGridDelays();
}
```

### AUTOEVALUCACIÓN

En esta unidad considero que me merezco un sólido 5 :)

¿La razón? Me gustó mucho lo que logré, sé que si hubiese sacado más tiempito pudo haber quedado mejor pero de todas formas traté de meterme con interacciones muy innovadoras y pues el resultado visual me gustó mucho. Además, modificar visuales en tiempo real es muy emocionante, escuchar la música y escribir la partitura sin duda ha sido una muy buena experiencia. Me gusta mucho experimentar con este tipo de cosas. 
