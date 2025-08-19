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

**RESISTENCIA DEL AIRE Y FLUIDOS**

Para modelar esta fuerza quise usar la formula de resistencia de fluido cuadrática: 

<img width="183" height="58" alt="image" src="https://github.com/user-attachments/assets/11a07c41-061a-4341-812b-769bd97abb48" />

En este caso en cada frame a cada "gota de pintura" se le aplican tres fuerzas (viento/remolino, Gravedad, Resistencia/ drag).La implementación clave dentro del código es la siguiente: 

```js
function applyQuadraticDrag(p, c) {
  const speedSq = p.vel.magSq(); //calcular la velocidad al cuadrado
  if (speedSq === 0) return;

  // Vector de arrastre opuesto a la velocidad
  let drag = p.vel.copy().normalize().mult(-1);  // crear copia, convertir en vector unitario  e invertir la dirección. 

  // |Fd| = c * |v|^2
  let dragMag = c * speedSq;  //Magnitud de la fuerza

  drag.setMag(dragMag);  //Aplicar la magnitud al vector de dirección
  p.applyForce(drag); //Sumatoria de fuerzas
}
```

Quise representar esta fuerza en una obra generativa donde al dar clic sea un punto de pintura, pero resulta que la superficie está influenciada de corrientes de viento que simulan remolinos y que se modifican en tiempo real por medio de dos sliders. 

[Link para ver sketch](https://editor.p5js.org/manuuuu15281/sketches/m3ZzpIGas)

```js
let droplets = [];      // varias gotas si haces varios clics, pero cada clic = 1 gota
let field;
let paintLayer;

let cDrag = 0.015;      // coeficiente de resistencia (slider)
let windMag = 0.7;      // fuerza del viento (slider)
let gravity = 0.10;     // gravedad leve

let cSlider, windSlider, clearBtn;

function setup() {
  createCanvas(900, 560);
  colorMode(HSB, 360, 100, 100, 255);

  paintLayer = createGraphics(width, height);
  paintLayer.colorMode(HSB, 360, 100, 100, 255);
  paintLayer.clear();

  field = new FlowField(32);

  // UI
  createSpan('  c (drag): ').style('color', '#ddd');
  cSlider = createSlider(0, 0.1, cDrag, 0.001).style('width', '200px');
  createSpan('    viento: ').style('color', '#ddd');
  windSlider = createSlider(0, 2.0, windMag, 0.05).style('width', '200px');
  clearBtn = createButton('Limpiar pintura');
  clearBtn.mousePressed(() => paintLayer.clear());
}

function draw() {
  background(0);
  image(paintLayer, 0, 0);

  cDrag = cSlider.value();
  windMag = windSlider.value();

  field.update();

  for (let i = droplets.length - 1; i >= 0; i--) {
    const d = droplets[i];

    // (1) viento/remolino (campo espacial)
    let wind = field.lookup(d.pos).setMag(windMag);
    d.applyForce(wind);

    // (2) gravedad
    d.applyForce(createVector(0, gravity * d.mass));

    // (3) drag cuadrático
    applyQuadraticDrag(d, cDrag);

    d.update();
    d.edges();
    d.paint(paintLayer);

    if (d.isExtinguished()) droplets.splice(i, 1);
  }

  // HUD mínimo
  noStroke();
  fill(0, 0, 100, 160);
  rect(8, 8, 250, 48, 8);
  fill(0);
  textSize(12);
  text('c (drag): ' + nf(cDrag, 1, 3), 16, 28);
  text('viento:   ' + nf(windMag, 1, 2), 16, 44);
}

function mousePressed() {
  addDroplet(mouseX, mouseY);
}

// --- una sola gota por clic ---
function addDroplet(x, y) {
  // velocidad inicial suave y aleatoria, la corriente redirige
  const v0 = p5.Vector.fromAngle(random(TWO_PI)).mult(random(0.5, 2.0));
  const m = random(0.8, 1.2);
  const hue = random(0, 300);
  droplets.push(new Droplet(createVector(x, y), v0, m, hue));
}

// --- Drag cuadrático ---
function applyQuadraticDrag(p, c) {
  const speedSq = p.vel.magSq();
  if (speedSq === 0) return;
  let drag = p.vel.copy().normalize().mult(-1);
  drag.setMag(c * speedSq); // |Fd| = c * |v|^2
  p.applyForce(drag);
}

// -------------------- Clases --------------------
class Droplet {
  constructor(pos, vel, mass, hue) {
    this.pos = pos.copy();
    this.vel = vel.copy();
    this.acc = createVector(0, 0);
    this.mass = mass;
    this.hue = hue;

    this.alpha = 150;          // opacidad media para superposición
    this.radius = 10;          // tamaño inicial de la gota
    this.paintLeft = 220;      // “cantidad” de pintura a gastar
  }

  applyForce(f) {
    const a = p5.Vector.div(f, this.mass);
    this.acc.add(a);
  }

  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);

    // Desgaste de la gota: se consume más cuando va más rápido
    const speed = this.vel.mag();
    this.paintLeft -= 0.6 + 0.12 * speed;   // desgaste por tiempo + por velocidad
    this.radius = max(0, this.radius - (0.02 + 0.01 * speed)); // encoge con el desgaste
    this.alpha = max(0, this.alpha - 0.4 - 0.05 * speed);      // se va apagando
  }

  paint(g) {
    if (this.alpha <= 0 || this.radius <= 0) return;
    g.push();
    g.noStroke();
    g.fill(this.hue, 90, 100, this.alpha);
    // “salpicadura” suave variable con la velocidad
    const d = max(1.5, this.radius + map(this.vel.mag(), 0, 8, -2, 3, true));
    g.ellipse(this.pos.x, this.pos.y, d, d);
    g.pop();
  }

  edges() {
    // rebote amortiguado para no salir del lienzo
    if (this.pos.x < 0) { this.pos.x = 0; this.vel.x *= -0.5; }
    if (this.pos.x > width) { this.pos.x = width; this.vel.x *= -0.5; }
    if (this.pos.y < 0) { this.pos.y = 0; this.vel.y *= -0.5; }
    if (this.pos.y > height) { this.pos.y = height; this.vel.y *= -0.5; }
  }

  isExtinguished() {
    return this.paintLeft <= 0 || this.alpha <= 0 || this.radius <= 0;
  }
}

// Campo de flujo (remolinos invisibles) con Perlin noise
class FlowField {
  constructor(scale) {
    this.scale = scale;
    this.cols = floor(width / scale) + 1;
    this.rows = floor(height / scale) + 1;
    this.field = Array.from({ length: this.cols }, () => Array(this.rows).fill(createVector(1, 0)));
    this.zoff = 0;
    this.generate();
  }

  generate() {
    let xoff = 0;
    for (let i = 0; i < this.cols; i++) {
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        const theta = noise(xoff, yoff, this.zoff) * TWO_PI * 2.0;
        const v = p5.Vector.fromAngle(theta).setMag(1);
        this.field[i][j] = v;
        yoff += 0.08;
      }
      xoff += 0.08;
    }
  }

  update() {
    this.zoff += 0.006;
    this.generate();
  }

  lookup(pos) {
    const col = constrain(floor(pos.x / this.scale), 0, this.cols - 1);
    const row = constrain(floor(pos.y / this.scale), 0, this.rows - 1);
    return this.field[col][row].copy();
  }
}
```

<img width="930" height="685" alt="image" src="https://github.com/user-attachments/assets/08e71eae-4b9d-41ad-b089-03da2de6b747" />







