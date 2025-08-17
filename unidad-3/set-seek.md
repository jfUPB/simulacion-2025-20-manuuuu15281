# Unidad 3

## 🔎 Fase: Set + Seek

### FRICCIÓN

Para modelar esta fuerza utilicé la formula de fricción cinética que proponen en el texto guía:

<img width="165" height="50" alt="image" src="https://github.com/user-attachments/assets/1734cb63-a064-4b3f-84d5-8d8cde49faab" />

En este caso, el coeficiente de fricción será controlado por un slider para darle un poco de interacción. Así que en cada frame se toma el valor del mu actual ( mu=muSlider.value();). Luego calculé la fuerza de fricción y para esto apliqué lo que vimos en clase (calculé la fricción a partir de la velocidad): 

```js
function applyFriction(m, mu) {
  if (m.velocity.mag() > 0) {
    let friction = m.velocity.copy(); // copy() para no mutar m.velocity
    friction.normalize();             // convierte la velocidad en un vector unitario
    friction.mult(-1);                // dirección opuesta
    let N = 1;                        
    friction.setMag(mu * N);          // |Ff| = μN
    m.applyForce(friction);           // F = m * a  -> acumula en aceleración y aplica la 2ª ley de Newton
  }
}
```

**CONCEPTO**

Para mi obra generativa decidí crear un pincel en forma de barra rectangular que tiene integrados cuadros de colores que pintan con una opacidad baja, podemos modificar el mu con un slider y en el parámetro de gravedad podemos modificarlo como prefiramos. 

[Link para ver sketch](https://editor.p5js.org/manuuuu15281/sketches/HIAVtpuk4)

```js
let mover;
let mu = 0;        // coeficiente de fricción (slider lo modifica)
let g = 0.3;          // gravedad opcional 
let paintLayer;       // capa donde pintamos con color
let base;             // textura inferior
let muSlider;         // slider 
let isDragging = false;

const BRUSH_W = 400;  // ancho de la barra 
const BRUSH_H = 40;   // alto de la barra 
const BRUSH_ALPHA = 50; // opacidad media (0–255)

function setup() {
  createCanvas(700, 360);
  colorMode(HSB, 360, 100, 100, 255);

  base = createGraphics(width, height);
  drawBaseTexture(base);

  paintLayer = createGraphics(width, height);
  paintLayer.colorMode(HSB, 360, 100, 100, 255);
  paintLayer.clear(); // transparente

  mover = new Mover();

  createP("Coeficiente de fricción (μ)").style("margin:6px 0 0 0");
  muSlider = createSlider(0, 0.5, mu, 0.005);
  muSlider.style("width", "260px");
}

function draw() {
  background(0);
  image(base, 0, 0);
  image(paintLayer, 0, 0);

  // Actualizar mu el slider
  mu = muSlider.value();

  //gravedad
  if (g !== 0) {
    let gravity = createVector(0, g * mover.mass);
    mover.applyForce(gravity);
  }

  // Fricción cinética
  applyFriction(mover, mu);

  // Integración y límites
  mover.update();
  mover.checkEdges();

  // Pintar con la barra (9 franjas arcoíris) según posición actual
  paintBrush(paintLayer, mover.position.x, mover.position.y, BRUSH_W, BRUSH_H);

  // Dibujar la propia barra (marco/guía)
  drawBrushPreview(mover.position.x, mover.position.y, BRUSH_W, BRUSH_H);
}

// ---------------- Interacción de arrastre ----------------
function mousePressed() {
  if (overBrush(mouseX, mouseY)) {
    isDragging = true;
  }
}

function mouseDragged() {
  if (isDragging) {
    // Mantener la barra horizontal: solo movemos su centro
    // Estimar velocidad a partir del movimiento del mouse
    let newPos = createVector(mouseX, mouseY);
    mover.velocity = p5.Vector.sub(newPos, mover.position); // v ≈ Δx por frame
    mover.position.set(newPos);
  }
}

function mouseReleased() {
  isDragging = false; // suelta y que actúe la fricción
}

// ---------------- Utilidades físicas ----------------
function applyFriction(m, mu) {
  if (m.velocity.mag() > 0) {
    let friction = m.velocity.copy();
    friction.normalize();    // v_hat
    friction.mult(-1);       // dirección opuesta
    let N = 1;               // fuerza normal unitaria
    friction.setMag(mu * N); // |Ff| = μN
    m.applyForce(friction);
  }
}

// ---------------- Pincel/Barra ----------------
function overBrush(x, y) {
  // La barra es un rectángulo centrado en mover.position, sin rotación
  return (abs(x - mover.position.x) <= BRUSH_W / 2 &&
          abs(y - mover.position.y) <= BRUSH_H / 2);
}

function paintBrush(gfx, cx, cy, w, h) {
  // 9 franjas verticales a lo ancho, con colores de arcoíris y alpha medio
  let stripeW = w / 9.0;
  let left = cx - w / 2;
  let top = cy - h / 2;

  for (let i = 0; i < 9; i++) {
    // Hues repartidos (rojo->violeta)
    let hue = map(i, 0, 8, 0, 300); // 0..300 (rojo a violeta). Ajusta a gusto.
    gfx.noStroke();
    gfx.fill(hue, 90, 100, BRUSH_ALPHA);
    gfx.rect(left + i * stripeW, top, stripeW + 1, h); // +1 evita gaps
  }
}

function drawBrushPreview(cx, cy, w, h) {
  push();
  noFill();
  stroke(0, 0, 100);
  strokeWeight(2);
  rectMode(CENTER);
  rect(cx, cy, w, h, 6);
  pop();
}

// ---------------- Capas visuales ----------------
function drawBaseTexture(gfx) {
  gfx.colorMode(HSB, 360, 100, 100, 255);
  gfx.background(220, 20, 15); // fondo oscuro suave
  // grano/ruido leve para que la superposición se note mejor
  gfx.noStroke();
  for (let i = 0; i < 12000; i++) {
    let x = random(width), y = random(height);
    let b = random(20, 35);
    gfx.fill(220, random(10, 25), b, 40);
    gfx.rect(x, y, 1, 1);
  }
}

// ---------------- Clase Mover ----------------
class Mover {
  constructor() {
    this.mass = 1;
    this.position = createVector(width / 2, height / 2);
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
  }
  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acceleration.add(f);
  }
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
  }
  checkEdges() {
    // mantener la barra visible sin rebotar (si prefieres rebote, invierte v)
    if (this.position.x > width - BRUSH_W / 2) {
      this.position.x = width - BRUSH_W / 2;
      this.velocity.x = 0;
    } else if (this.position.x < BRUSH_W / 2) {
      this.position.x = BRUSH_W / 2;
      this.velocity.x = 0;
    }
    if (this.position.y > height - BRUSH_H / 2) {
      this.position.y = height - BRUSH_H / 2;
      this.velocity.y = 0;
    } else if (this.position.y < BRUSH_H / 2) {
      this.position.y = BRUSH_H / 2;
      this.velocity.y = 0;
    }
  }
}
```
<img width="888" height="518" alt="image" src="https://github.com/user-attachments/assets/5d3f325e-c3c4-4b78-851b-6503471768d5" />


**Resistencia del aire y fluidos**





