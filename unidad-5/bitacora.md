# Evidencias de la unidad 5

## ACTIVIDAD  2
### Ejemplo 4.2: Array of particles


**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

Luego de ver este ejemplo y analizar el código me di cuenta que en cada frame se crea una particula nueva y cada una tiene un contador de vida que va disminuyendo hasta que se elimina. 

Para su creación el método draw() se ejecuta en cada frame y se emite una particula nueva con una velocidad inicial aleatoria. Luego de creada la particula, en cada frame se aplica una gravedad suave con this.applyForce y se le hace un update (se aplica el concepto de motion 101). Seguido a esto con el método show() dibuja la particula modificando su alpha (empieza en 255 y resta 2 por frame) hasta que dejan de verse.

Para desaparecer la particula, en el método isDead() devuelve true cuando su alpha < 0 y se elimina del array. 

El bucle para borrar las particula es al revés por lo siguiente (explicaré como lo entendí): 

- Empieza desde la última particula generada hasta la más antigua para verificar su alpha < 0 y en caso de que cumpla con la condición la particula en cuestión se eliminirá y las particulas que ya se revisaron se corren un posición a la izquierda pero no pasa nada porque el código sigue su recorrido hacia la izquierda y no hay riesgo de saltar ninguna particula (cosa que si pasaría si recorrieramos el array de izquierda a derecha).

**¿cómo se gestiona la memoria en esta simulación?**

En el código se crean objetos (partículas + vectores), mientra las particulas estén "vivas" se mantienen referencias en particles, cuando la particula finalmente muere, se cortan las referencias con splice y al GC no detectar referencias libera automáticamente lo que queda.


Este código lo modifiqué implementando un **Levy flight** en el punto emisor de particulas, ahora este cambia aleatoriamente. 

[Link al código modificado](https://editor.p5js.org/manuuuu15281/sketches/V00gjUd5D)

En este caso el código sigue gestionando la creación y desaparición de las particulas de la misma manera: 

- Crea una particula en cada frame. Cada una de estas tiene un tiempo de vida que se gestiona primero con un alpha que empieza en 255 y cada frame resta 2, al llegar a 0 o a un número menor el bucle lo identifica y lo elimina del array y finalmente queda eliminado.

Como dije anteriormente, el concepto que elegí fue **levy flight** que hace que el punto emisor origin se mueve cada frame con muchos desplazamientos cortos y, de vez en cuando, hace saltos largos. Para que el código no se descontrole visualmente, recorto el paso largo con un límite máximo y, si el emisor de particulas sale del lienzo, aplico wrap-around para que reaparezca por el lado opuesto. Las partículas se siguen emitiendo en la posición actual del emisor y conservan su física original (gravedad constante, vida que disminuye y eliminación al morir).


<img width="799" height="302" alt="image" src="https://github.com/user-attachments/assets/e810422a-cf13-46ea-923a-8ba91148b3ef" />


**Sketch.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

let particles = [];

// --- Emisor dinámico:Lévy flight ---
let origin;                      // punto desde donde nacen las partículas

const EMIT_PER_FRAME = 1;        // cuántas partículas emites por frame

// Gravedad reusable (evita crear vectores por frame)
let GRAVITY;

// Parámetros Lévy flight
const LEVY_ALPHA = 1.5;          // 1 < alpha <= 2 (1.3–1.7 da saltos interesantes)
const LEVY_SCALE = 12;           // escala base del paso en píxeles
const MAX_STEP = 80;             // recorte de la cola para evitar saltos “infinitos”

function setup() {
  createCanvas(640, 240);
  origin = createVector(width / 2, 20); // arranca arriba al centro
  GRAVITY = createVector(0, 0.05);
}

function draw() {
  background(255);

  updateEmitterLevy(); // mueve el emisor con Lévy flight

  // Emitir partículas en el punto actual
  for (let n = 0; n < EMIT_PER_FRAME; n++) {
    particles.push(new Particle(origin.x, origin.y));
  }

  // Recorrer de atrás hacia adelante para poder eliminar con seguridad
  for (let i = particles.length - 1; i >= 0; i--) {
    const p = particles[i];
    p.run();
    if (p.isDead()) {
      particles.splice(i, 1);
    }
  }
}

// --- Utilidades para el emisor ---


function levyStep(alpha) {
  
  const u = random(1e-6, 1);            // evita 0
  return pow(u, -1 / alpha) - 1;        // longitudes grandes con baja probabilidad
}

function updateEmitterLevy() {
  // Paso de Lévy en dirección aleatoria
  const theta = random(TWO_PI);
  const stepRaw = levyStep(LEVY_ALPHA) * LEVY_SCALE;
  const step = min(stepRaw, MAX_STEP);  // recorta cola para estabilidad visual

  origin.x += cos(theta) * step;
  origin.y += sin(theta) * step;

  // Envolver dentro del canvas (wrap-around)
  if (origin.x < 0) origin.x += width;
  if (origin.x > width) origin.x -= width;
  if (origin.y < 0) origin.y += height;
  if (origin.y > height) origin.y -= height;
}

```


**particle.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Simple Particle System

class Particle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
  }

  run() {
    // Usa la constante GRAVITY definida en sketch.js
    this.applyForce(GRAVITY);
    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  // Integración del movimiento + “consumo” de vida
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0); // no acumular fuerzas de un frame a otro
  }

  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}

```

### Ejemplo 4.4: System of systems
