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


**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

Para este ejemplo, las particulas se crean en los emisores que se colocan al dar clic con el mouse (la posición en la que se crea la particula está ligada a la posición de cada emiter que se crea), en este ejemplo como en el anterior se crea una particula por frame en cada emitter y con un alpha inicial de 255 que disminuyendo en 2 cada frame, cuando finalmente el alpha < 0 un bucle elimina esa particula del array (también se recorre de atrás para delante). 

**¿cómo se gestiona la memoria en esta simulación?**

Este ejemplo como el anterior utiliza el Garbage Collector (GC) de Java. Este código se limita a crear objetos (las particulas) y soltar sus referencias a tiempo para que el GC los recicle. En el emitter.run(), al eliminar la referencia del array, si nadie más apunta a esa Particle, el GC puede recolectarla y se libera en la memoria. 


En este código quise modelar la resistencia al agua de las particulas al entrar en contacto con este fluido simulado en la parte de abajo del canva. 

[Link para ver el código modificado](https://editor.p5js.org/manuuuu15281/sketches/pWWebUnG4)

En este sistema, cada vez que haces clic se crea un “emisor” y, en cada fotograma, cada emisor añade una partícula nueva en su punto de origen. Si las partículas entran en la zona azul de “agua”, sienten una resistencia que las frena; además, su “vida” (el Alpha) se disminuye en 2 en cada frame. Cuando esa vida llega a cero, la partícula se considera “muerta” y la quitamos del arreglo de partículas recorriéndolo de atrás hacia adelante para no saltarnos ninguna. Así, el efecto visual se mantiene fluido: nacen, se mueven, se van desvaneciendo y, al final, desaparecen limpiamente.

Apliqué el concepto de resistencia de fluido: en la parte baja del canvas marqué una zona de “agua” y, cuando una partícula entra allí, se le agrega una fuerza de freno en dirección contraria a su movimiento. Esa fuerza crece cuanto más rápido va la partícula, así que en el agua se desacelera y se mueve con pasos más cortos, como en la vida real. Lo implementé detectando si la **Y** de la partícula está por debajo del borde del agua, en caso de que sí, calculamos el vector opuesto a su velocidad, le damos una magnitud según c * v^2 (donde c es el coeficiente del agua) y lo sumamos a sus fuerzas. El resto no cambia: la gravedad sigue actuando, la partícula se actualiza y se dibuja igual, y su vida se va agotando hasta desaparecer del arreglo.

<img width="791" height="292" alt="image" src="https://github.com/user-attachments/assets/f23afb68-2999-47ef-bdbf-6fa188e41815" />

**emitter.js**


```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  // Ahora recibe el entorno para pasarlo a cada partícula
  run(gravity, waterTop, waterC) {
    // Looping backwards to delete in-place
    for (let i = this.particles.length - 1; i >= 0; i--) {
      this.particles[i].run(gravity, waterTop, waterC);
      if (this.particles[i].isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
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

  // Recibe gravedad, borde superior del agua y coeficiente de drag
  run(gravity, waterTop, waterC) {
    // Fuerzas
    this.applyForce(gravity);

    // Si está dentro del agua, aplicar resistencia de fluido (drag cuadrático)
    if (this.position.y >= waterTop) {
      this.applyFluidDrag(waterC);
    }

    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  // Resistencia de fluido: Fd = -c * |v|^2 * v̂
  applyFluidDrag(c) {
    const speed = this.velocity.mag();
    if (speed === 0) return;

    const dragMag = c * speed * speed; // proporcional a v^2
    const drag = this.velocity.copy().mult(-1);
    drag.normalize();
    drag.mult(dragMag);

    this.applyForce(drag);
  }

  // Integración
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0);
  }

  // Dibujo
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  // Vida útil
  isDead() {
    return this.lifespan < 0.0;
  }
}


```

**Sketch.js**


```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Particles are generated each cycle through draw(),
// fall with gravity and fade out over time

let emitters = [];

// Entorno
let GRAVITY;                 // vector de gravedad (reutilizado para no generar basura)
let WATER_TOP;               // y donde empieza el agua (se recalcula por si cambia el canvas)
const WATER_HEIGHT_FRAC = 0.35; // % de alto ocupado por el “agua” al fondo
const WATER_C = 0.15;        // coeficiente de drag del agua (ajusta la resistencia)

function setup() {
  createCanvas(640, 240);
  createP("Click para añadir emisores. Zona inferior = agua con resistencia.");
  GRAVITY = createVector(0, 0.05);
}

function draw() {
  background(255);

  // Calcular y dibujar el “agua”
  WATER_TOP = height * (1 - WATER_HEIGHT_FRAC);
  noStroke();
  fill(100, 150, 255, 60);
  rect(0, WATER_TOP, width, height - WATER_TOP);

  // Actualizar emisores
  for (let emitter of emitters) {
    emitter.run(GRAVITY, WATER_TOP, WATER_C);
    emitter.addParticle();
  }
}

function mousePressed() {
  emitters.push(new Emitter(mouseX, mouseY));
}


```

### Ejemplo 4.5: Particle System with Inheritance and Polymorphism.


**¿Cómo se está gestionando la creación y la desaparción de las partículas?**

Para este ejemplo, si bien funciona de una manera muy similar a los ejemplos anteriores, en este caso cambia un poco porque le añaden una nueva clase que hereda de particle: confetti. Así que al agregar esta clase se añaden unas funcionalidades más. 

Para la creación de las particulas, en cada draw() se llama emitter.addParticle() y este método hace un tipo de coin flip que da como resultado la creación de una instancia: new Particle o new Confetti. La particula o confetti que nace lleva por determinado la posición del emisor, una velocidad aleatoria, una aceleración en 0 y un alpha en 255. Continua creandose una particula por frame y corre a 60 FPS. 

El emitter.run() recorre el array constantemente y lo limpia de las particulas ya "muertas", que está determinado por el valor del lifespan, mientras el p.run() se encarga de aplicar todas las fuerzas, resetear la aceleración y disminuir la esperanza de vida en 2. Por último, el método show() dibuja la particula con el alpha indicado por el lifespan, en este caso si es particle se dibuja un circulo pero si es confetti se sobrescribe el show() y dibuja un cuadrado que  rota (el resto del ciclo de vida del confetti es el mismo que el de particle). 


**¿cómo se gestiona la memoria en esta simulación?**

Se gestiona de la misma manera que los ejemplos anteriores, el ciclo de vida de cada tipo de particula + Garbage collector. Al emitter.run() detectar la particula con su lifespan < 0  la saca del arreglo y elimina su referencia, al quedar esa particula sin referencias el GC libera su espacio en memoria automáticamente.

Para este ejemplo decidí implemnetar un lepcolor() que aprendimos en la unidad 2. Si bien quería implementar un Motion 101 el código ya lo estaba implementando así que no sabía cómo volverlo a integrar. 


Esta modificación es muy breve y no interviene en nada de la gestión de creación y desaparición de los diferentes tipos de particulas, solo interviene el color en la creación de particulas utilizando lepcolor. 

[Link para ver el código modificado](https://editor.p5js.org/manuuuu15281/sketches/ZQ5Ulq6mg)

Apliqué una interpolación de color usando lepColor() y  se aplica al momento de nacimiento de cada partícula. Donde lerpColor(a, b, t) mezcla dos colores a y b usando un factor t entre 0 y 1 (0 = todo a, 1 = todo b). Practicamente es como mover un “slider” entre dos colores.

El emisor calcula un número que sube y baja suavemente (con una onda “sinusoidal”) y, con ese número, mezcla los dos colores para obtener un tono en ese instante. Cada partícula nace con ese color ya mezclado y lo conserva toda su vida; lo único que cambia después es su transparencia, que se va volviendo más baja hasta desaparecer. Así, el chorro completo va cambiando de color con el tiempo, pero cada partícula mantiene su tono mientras se va apagando.

<img width="796" height="304" alt="image" src="https://github.com/user-attachments/assets/f45ea5dc-5aa2-47af-978e-5a7a84d90a05" />

**emitter.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

class Emitter {
  constructor(x, y, cA, cB) {
    this.origin = createVector(x, y);
    this.particles = [];
    this.cA = cA; // color inicio
    this.cB = cB; // color fin
    this.t = 0;   // tiempo para variar el lerp
  }

  addParticle() {
    // Factor suave y oscilante para el gradiente
    const amt = (sin(this.t) + 1) / 2; // 0..1
    const col = lerpColor(this.cA, this.cB, amt);
    this.t += 0.03;

    let r = random(1);
    if (r < 0.5) {
      this.particles.push(new Particle(this.origin.x, this.origin.y, col));
    } else {
      this.particles.push(new Confetti(this.origin.x, this.origin.y, col));
    }
  }

  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      let p = this.particles[i];
      p.run();
      if (p.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}

```


**particle.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Simple Particle System

class Particle {
  constructor(x, y, col) {
    this.position = createVector(x, y);
    this.acceleration = createVector(0, 0);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.lifespan = 255.0;
    this.col = col || color(127); // color asignado al nacer (desde lerpColor)
  }

  run() {
    let gravity = createVector(0, 0.05);
    this.applyForce(gravity);
    this.update();
    this.show();
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0);
  }

  show() {
    // Usar el color asignado con alpha según lifespan
    const c = color(this.col);
    c.setAlpha(this.lifespan);

    stroke(c);
    strokeWeight(2);
    fill(c);
    circle(this.position.x, this.position.y, 8);
  }

  isDead() {
    return this.lifespan < 0.0;
  }
}

```

**confetti.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Child class constructor
class Confetti extends Particle {
  // Override the show method
  show() {
    let angle = map(this.position.x, 0, width, 0, TWO_PI * 2);

    rectMode(CENTER);

    // Usar el mismo color heredado con alpha por lifespan
    const c = color(this.col);
    c.setAlpha(this.lifespan);

    stroke(c);
    strokeWeight(2);
    fill(c);

    push();
    translate(this.position.x, this.position.y);
    rotate(angle);
    square(0, 0, 12);
    pop();
  }
}

```

**sketch.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

// Particles are generated each cycle through draw(),
// fall with gravity and fade out over time
// A ParticleSystem object manages a variable size
// list of particles.

let emitter;
let cStart, cEnd;

function setup() {
  createCanvas(640, 240);

  // Colores base para el gradiente (puedes cambiarlos)
  cStart = color(66, 135, 245);  // azul
  cEnd   = color(255, 80, 120);  // rosado

  emitter = new Emitter(width / 2, 20, cStart, cEnd);
}

function draw() {
  background(255);
  emitter.addParticle();
  emitter.run();
}

```

### Ejemplo 4.6: Particle System with Forces.

Para este ejemplo las particulas se generan de la misma manera que los ejemplos anteriores: El emisor genera una particula por frame y corre a 60 FPS, cada particula se genera con la posición del emitter, una velocidad aleatoria y en este caso se les aplica una fuerza ( F= m * a) pero en el código se está dividiendo la fuerza por la masa (en este caso tiene por determinado el valor de 1 ) y luego se le suma a la aceleración la fuerza. Todavía conserva el lifespan que arranca en 255 y se le resta 2 en cada frame hasta que se desvanece del todo. Cuando el lifespan < 0 el emiiter.run() lo elimina del array eliminando su referencia, luego el Garbage collector de JavaScript libera la memoria de esa particula porque ya no está siendo referenciado. 


En este caso quise modelar una fuerza adicional para el código: la atracción gravitacional. Implementé  un atractor que se coloca con el mouse y trae hacia el las particulas que el emitter genera. 

[Link para ver el código modificado](https://editor.p5js.org/manuuuu15281/sketches/lIyI2oZvR)

Para implementarlo, al hacer clic en el código se crea el attractor (attractor = createVector(mouseX, mouseY) ) y cuanto más cerca está la partícula, más fuerte la jala; cuanto más lejos, más suave. Para que no se vuelva incontrolable cuando está muy cerca la particula del attractor, limitamos la distancia mínima y máxima al calcular el jalón.
En cada frame, tomamos la dirección desde la partícula hasta el attractor, calculamos una fuerza en esa dirección (con ayuda de “fuerza” general y la “masa” del attractor), y se la sumamos a la gravedad. Con esas fuerzas, la partícula actualiza su velocidad y posición; visualmente sigue desvaneciéndose igual que antes y, cuando su vida se agota, desaparece.

<img width="844" height="575" alt="image" src="https://github.com/user-attachments/assets/ecae7145-14d1-492f-9eba-2100205598e6" />

**emitter.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  // Fuerza uniforme (ej. gravedad global)
  applyForce(force) {
    for (let p of this.particles) {
      p.applyForce(force);
    }
  }

  // Aplica atracción gravitacional hacia un punto "target"
  // G: constante gravitacional efectiva
  // M: "masa" del atractor
  // minD/maxD: límites para evitar singularidades / fuerzas ridículas
  applyAttractor(target, G = 1.0, M = 80, minD = 10, maxD = 50) {
    if (!target) return;
    for (let p of this.particles) {
      // Vector desde la partícula al atractor
      const dir = p5.Vector.sub(target, p.position);
      let d = dir.mag();
      // Limitar distancia para estabilidad numérica
      d = constrain(d, minD, maxD);
      dir.normalize();

      // F = G * m * M / d^2 (dirección hacia el atractor)
      const strength = (G * p.mass * M) / (d * d);
      const force = p5.Vector.mult(dir, strength);

      p.applyForce(force);
    }
  }

  run() {
    // Recorre hacia atrás para eliminar seguras
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const p = this.particles[i];
      p.run();
      if (p.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
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
    this.acceleration = createVector(0, 0.0);
    this.velocity = createVector(random(-1, 1), random(-2, 0));
    this.lifespan = 255.0;
    this.mass = 1; // Puedes variar (p.ej. random(0.5, 2)) si quieres diversidad
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(force) {
    // a = F / m
    const f = force.copy().div(this.mass);
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
    this.lifespan -= 2.0;
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


**sketch.js**

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

let emitter;
let attractor = null;          // Se define al hacer clic
const G = 1.2;                 // Constante gravitacional efectiva (ajústala)
const ATTRACTOR_MASS = 500;     // "Masa" del atractor (ajústala)

function setup() {
  createCanvas(1280, 480);
  emitter = new Emitter(width / 2, 50);
}

function draw() {
  background(255, 30);

  // Gravedad vertical uniforme
  const gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);

  // Aplica atracción si ya hay atractor
  if (attractor) {
    emitter.applyAttractor(attractor, G, ATTRACTOR_MASS);

    // Dibujo del atractor (marcador visual)
    noStroke();
    fill(0, 120);
    circle(attractor.x, attractor.y, 16);

    noFill();
    stroke(0, 60);
    circle(attractor.x, attractor.y, 80); // aro de referencia opcional
  }

  emitter.addParticle();
  emitter.run();
}

// Cada clic fija/actualiza la posición del atractor
function mousePressed() {
  attractor = createVector(mouseX, mouseY);
}

```


### Ejemplo 4.7: Particle System with a Repeller.

La novedad en este ejemplo es la implementación del repeller. De resto las particulas se generan y desaparecen de la misma manera que todos los ejemplos anteriores. 

Para modificar este código decidí que quería hacer que el repeller se mueva con velocidad angular usando coordenadas polares. 

[Link para acceder al código modificado](https://editor.p5js.org/manuuuu15281/sketches/vPs-YqSFU)

Apliqué dos ideas sencillas de movimiento: traslación constante y rotación con coordenadas polares. Primero, al repeller le di una velocidad fija para su “centro” y lo hago rebotar en los bordes, así recorre todo el canvas sin quedarse en un solo lugar. Segundo, sobre ese centro del repeller, calculo su posición real con una órbita polar: x = cx + r·cos(θ) y y = cy + r·sin(θ), aumentando θ a ritmo constante (ω) para que gire suavemente. Para poder ver la rotación, dibujo una raya interna desde el centro de la bolita hasta el borde y así muestra hacia dónde se está orientado en cada momento.

<img width="783" height="299" alt="image" src="https://github.com/user-attachments/assets/c0cd5152-89a0-49bd-bdc0-541ae5ad5bfe" />

**repeller.js**

```js
class Repeller {
  /**
   * @param {number} cx Centro X de la órbita local
   * @param {number} cy Centro Y de la órbita local
   * @param {number} radius Radio de la órbita local (en px)
   * @param {number} thetaStart Ángulo inicial (radianes)
   * @param {object} options { centerSpeed, omega, power, visualR }
   */
  constructor(cx, cy, radius = 60, thetaStart = 0, options = {}) {
    this.center = createVector(cx, cy); // centro que se moverá por el canvas
    const speed = options.centerSpeed ?? 2.0;

    // Velocidad constante del centro (dirección aleatoria inicial)
    this.centerVel = p5.Vector.fromAngle(random(TWO_PI)).setMag(speed);

    this.radius = radius;              // radio de la órbita local
    this.theta = thetaStart;           // ángulo polar para el giro local
    this.omega = options.omega ?? 0.05;// velocidad angular constante
    this.power = options.power ?? 150; // fuerza de repulsión
    this.visualR = options.visualR ?? 16; // radio visual de la bolita

    // Posición inicial del repeller (centro + órbita polar)
    const x0 = this.center.x + this.radius * cos(this.theta);
    const y0 = this.center.y + this.radius * sin(this.theta);
    this.position = createVector(x0, y0);
  }

  update() {
    // 1) Mover el centro con velocidad constante y rebotar en bordes
    this.center.add(this.centerVel);

    // Margen para que NI el giro (radius) NI la bolita visual salgan del canvas
    const margin = this.radius + this.visualR;

    if (this.center.x < margin || this.center.x > width - margin) {
      this.centerVel.x *= -1;
      // Clamp suave para evitar quedar pegado al borde
      this.center.x = constrain(this.center.x, margin, width - margin);
    }
    if (this.center.y < margin || this.center.y > height - margin) {
      this.centerVel.y *= -1;
      this.center.y = constrain(this.center.y, margin, height - margin);
    }

    // 2) Rotación local con coordenadas polares
    this.theta += this.omega;

    // Posición final del repeller = centro móvil + órbita polar
    const x = this.center.x + this.radius * cos(this.theta);
    const y = this.center.y + this.radius * sin(this.theta);
    this.position.set(x, y);
  }

  show() {
    // Círculo del repeller
    stroke(0);
    strokeWeight(2);
    fill(127);
    circle(this.position.x, this.position.y, this.visualR * 2);

    // Raya interna que marca el radio (usa el mismo ángulo theta para visualizar la rotación)
    const endX = this.position.x + this.visualR * cos(this.theta);
    const endY = this.position.y + this.visualR * sin(this.theta);
    line(this.position.x, this.position.y, endX, endY);
  }

  repel(particle) {
    // Fuerza de repulsión tipo inversa al cuadrado de la distancia
    let force = p5.Vector.sub(this.position, particle.position);
    let distance = force.mag();
    distance = constrain(distance, 5, 50);
    const strength = (-1 * this.power) / (distance * distance);
    force.setMag(strength);
    return force;
  }
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
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.acceleration = createVector(0, 0);
    this.lifespan = 255.0;
  }

  run() {
    this.update();
    this.show();
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  // Method to update position
  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0);
  }

  // Method to display
  show() {
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(127, this.lifespan);
    circle(this.position.x, this.position.y, 8);
  }

  // Is the particle still useful?
  isDead() {
    return this.lifespan < 0.0;
  }
}

```

**sketch.js**

```js
// One ParticleSystem
let emitter;
// Repeller que ahora se desplaza por todo el canvas y gira
let repeller;

function setup() {
  createCanvas(640, 240);
  emitter = new Emitter(width / 2, 60);

  // Centro inicial cerca a la parte inferior, pero ahora se moverá por todo el canvas
  const cx = width * 0.25;
  const cy = height * 0.75;

  // Radio de la órbita local del repeller (giro alrededor de su propio centro móvil)
  const r = min(width, height) * 0.12;

  repeller = new Repeller(
    cx,
    cy,
    r,
    random(TWO_PI),
    {
      centerSpeed: 2.2, // px por frame (movimiento constante por el canvas)
      omega: 0.07,      // radianes por frame (velocidad angular)
      power: 150,       // fuerza de repulsión (igual que antes)
      visualR: 16       // radio visual de la bolita (diámetro 32)
    }
  );
}

function draw() {
  background(255);

  // Nace una partícula por frame
  emitter.addParticle();

  // Gravedad global
  const gravity = createVector(0, 0.1);

  // Actualizar repeller (mueve su centro por el canvas + rota con coordenadas polares)
  repeller.update();

  // Aplicar fuerzas y correr sistema
  emitter.applyForce(gravity);
  emitter.applyRepeller(repeller);
  emitter.run();

  // Dibujar repeller (con la raya interna que marca su radio/orientación)
  repeller.show();
}

```

**emitter.js**

```js
// The Emitter manages all the particles.
class Emitter {

  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
  }

  addParticle() {
    this.particles.push(new Particle(this.origin.x, this.origin.y));
  }

  applyForce(force) {
    // Applying a force as a p5.Vector
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  applyRepeller(repeller) {
    // Calculating a force for each Particle based on a Repeller
    for (let particle of this.particles) {
      const force = repeller.repel(particle);
      particle.applyForce(force);
    }
  }

  run() {
    // Recorremos al revés para poder eliminar sin problemas
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];
      particle.run();
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }
}

```

## ACTIVIDAD  3

Lo que me imaginé para mi apply mientras hacía la actividad 2 fue algo así: 

<img width="1582" height="533" alt="image" src="https://github.com/user-attachments/assets/f68c4159-124e-4f44-89c3-aa27aaf10e10" />

Para mi concepto me imaginé un lienzo con un pincel y un borrador poco convencional autónomos ambos, que se mueven por el canvas con un tipo de movimiento en específico. En resumen, me imaginé un lienzo atipico donde el pincel y el borrador se mueven libremente. 

En palabras más tecnicas, me imaginé un emitter que genera particulas de diferentes tipos que a la vez son unos borradores, estas particulas cambian de color y forma a medida que van saliendo del emitter. El emitter se mueve con velocidad angular usando coordenadas polares generaando que las particulas salgan de manera divertida.  El color de las particulas está controlado por un lepColor() con el que se podrá interactuar eligiendo los dos colores que usará la función lepColor() para modificar los colores de las particulas.

Al mismo tiempo, implementaré un atractor que también se movera con velocidad angular y además tendrá integrado un levy flight que hará que cada cierto tiempo de saltos de posición que hará más impredecible mi obra. El atractor funciona con atracción gravitacional que traerá a si las particulas que genera el emitter. 

[Clic para acceder al link de mi obra](https://editor.p5js.org/manuuuu15281/sketches/vEqBCZkMT)

**emitter.js**

```js
// The Emitter manages all the particles.
class Emitter {
  /**
   * @param {number} cx Centro X de la órbita local del emitter
   * @param {number} cy Centro Y de la órbita local del emitter
   * @param {number} radius Radio de la órbita local (px)
   * @param {number} thetaStart Ángulo inicial (rad)
   * @param {object} options { centerSpeed, omega }
   */
  constructor(cx, cy, radius = 50, thetaStart = 0, options = {}) {
    // Centro móvil + velocidad constante (dirección aleatoria)
    this.center = createVector(cx, cy);
    const speed = options.centerSpeed ?? 2.0;
    this.centerVel = p5.Vector.fromAngle(random(TWO_PI)).setMag(speed);

    // Órbita polar local
    this.radius = radius;
    this.theta = thetaStart;
    this.omega = options.omega ?? 0.05;

    // La "origin" es donde nacen las partículas: centro + órbita polar
    const x0 = this.center.x + this.radius * cos(this.theta);
    const y0 = this.center.y + this.radius * sin(this.theta);
    this.origin = createVector(x0, y0);

    // Radio de colisión para el origin (pequeño halo)
    this.collisionR = 12;

    // Gradiente de color (extremos) y fase de interpolación
    this.col1 = color('#00aaff');
    this.col2 = color('#ff00aa');
    this.lerpPhase = 0;          // avanza en el tiempo
    this.lerpSpeed = 0.02;       // qué tan rápido recorre el gradiente

    // Tipo actual de partícula (fábrica)
    this.shapeType = 'circle';   // 'circle' | 'square' | 'triangle'

    this.particles = [];
  }

  setColors(c1, c2) {
    this.col1 = c1;
    this.col2 = c2;
  }

  randomizeShape() {
    const choices = ['circle', 'square', 'triangle'];
    this.shapeType = random(choices);
  }

  update() {
    // 1) Traslación del centro con rebote en bordes
    this.center.add(this.centerVel);

    const margin = this.radius;

    if (this.center.x < margin || this.center.x > width - margin) {
      this.centerVel.x *= -1;
      this.center.x = constrain(this.center.x, margin, width - margin);
    }
    if (this.center.y < margin || this.center.y > height - margin) {
      this.centerVel.y *= -1;
      this.center.y = constrain(this.center.y, margin, height - margin);
    }

    // 2) Giro local (θ) y actualización de la origin (coordenadas polares)
    this.theta += this.omega;

    const x = this.center.x + this.radius * cos(this.theta);
    const y = this.center.y + this.radius * sin(this.theta);
    this.origin.set(x, y);

    // 3) Avance de la fase del degradé
    this.lerpPhase += this.lerpSpeed;
  }

  addParticle() {
    // Interpolación entre col1 y col2
    const amt = (sin(this.lerpPhase) + 1) * 0.5; // mapea sin() a [0,1]
    const col = lerpColor(this.col1, this.col2, amt);

    // Fábrica por tipo (polimorfismo)
    let p;
    if (this.shapeType === 'square') {
      p = new SquareParticle(this.origin.x, this.origin.y, col);
    } else if (this.shapeType === 'triangle') {
      p = new TriangleParticle(this.origin.x, this.origin.y, col);
    } else {
      p = new CircleParticle(this.origin.x, this.origin.y, col);
    }

    this.particles.push(p);
  }

  applyForce(force) {
    for (let particle of this.particles) {
      particle.applyForce(force);
    }
  }

  // Aplica fuerzas de los repellers en modo "attractor" (gravedad)
  applyRepellers(repArray) {
    for (let particle of this.particles) {
      for (const rep of repArray) {
        if (rep.isAttractor()) {
          const f = rep.repel(particle); // ← ahora es atracción
          particle.applyForce(f);
        }
      }
    }
  }

  // PASO 1: simular + borrar + colisiones con "painters"
  step(g /* p5.Graphics */, colliders /* array de repellers painter */) {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      const particle = this.particles[i];

      // Integración
      particle.simulate();

      // Colisiones con repellers "painter"
      if (colliders && colliders.length) {
        for (const rep of colliders) {
          if (rep && rep.mode === "painter") {
            rep.collide(particle);
          }
        }
      }

      // Borrado de la tinta
      if (g) particle.eraseOn(g);

      // Vida
      if (particle.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }

  // PASO 2: render en el canvas principal
  render() {
    for (let i = 0; i < this.particles.length; i++) {
      this.particles[i].show();
    }
  }
}

```


**repeller.js**

```js
class Repeller {
  /**
   * @param {number} cx Centro X de la órbita local
   * @param {number} cy Centro Y de la órbita local
   * @param {number} radius Radio de la órbita local (en px)
   * @param {number} thetaStart Ángulo inicial (radianes)
   * @param {object} options {
   *   centerSpeed, omega,
   *   power,              // G*M efectivo del atractor
   *   visualR,
   *   brushWeight, brushAlpha,
   *   levyProb, levyAlpha, levyMin, levyMax,
   *   softenMin, softenMax,
   *   mode                // "attractor" | "painter"
   * }
   */
  constructor(cx, cy, radius = 60, thetaStart = 0, options = {}) {
    this.center = createVector(cx, cy);
    const speed = options.centerSpeed ?? 2.0;
    this.centerVel = p5.Vector.fromAngle(random(TWO_PI)).setMag(speed);

    this.radius = radius;
    this.theta  = thetaStart;
    this.omega  = options.omega ?? 0.05;

    // Gravedad (solo usada si mode === "attractor")
    this.power  = options.power ?? 150;

    this.visualR = options.visualR ?? 16;

    // Pincel (usado normalmente por los "painter")
    this.brushWeight = options.brushWeight ?? 8;
    this.brushAlpha  = options.brushAlpha  ?? 255;

    // Lévy flight (mov. errático — aplica a ambos modos, puedes bajar la prob. si no lo quieres)
    this.levyProb  = options.levyProb  ?? 0.012;
    this.levyAlpha = options.levyAlpha ?? 1.5;
    this.levyMin   = options.levyMin   ?? 12;
    this.levyMax   = options.levyMax   ?? 180;

    // Suavizado de distancia para la fuerza gravitacional
    this.softenMin = options.softenMin ?? 5;
    this.softenMax = options.softenMax ?? 50;

    // Modo de trabajo
    this.mode = options.mode ?? "attractor"; // "attractor" o "painter"

    const x0 = this.center.x + this.radius * cos(this.theta);
    const y0 = this.center.y + this.radius * sin(this.theta);
    this.position = createVector(x0, y0);

    this.lastPosition   = this.position.copy();
    this._suppressPaint = false; // “levantar pincel” en salto Lévy
  }

  isAttractor() {
    return this.mode === "attractor"; 
  }

  _levyStepLength() {
    const u = random(1);
    let L = this.levyMin / pow(1 - u, 1 / this.levyAlpha);
    return constrain(L, this.levyMin, this.levyMax);
  }

  _maybeLevyJump() {
    if (random(1) < this.levyProb) {
      const ang = random(TWO_PI);
      const L   = this._levyStepLength();
      const dx  = L * cos(ang);
      const dy  = L * sin(ang);

      const margin = this.radius + this.visualR;
      const nx = constrain(this.position.x + dx, margin, width  - margin);
      const ny = constrain(this.position.y + dy, margin, height - margin);

      this.position.set(nx, ny);
      // Mantener pos = center + R*(cosθ, sinθ)
      this.center.set(
        this.position.x - this.radius * cos(this.theta),
        this.position.y - this.radius * sin(this.theta)
      );

      this._suppressPaint = false; // no trazar el segmento del salto
    }
  }

  update() {
    // Traslación del centro + rebote
    this.center.add(this.centerVel);
    const margin = this.radius + this.visualR;

    if (this.center.x < margin || this.center.x > width - margin) {
      this.centerVel.x *= -1;
      this.center.x = constrain(this.center.x, margin, width - margin);
    }
    if (this.center.y < margin || this.center.y > height - margin) {
      this.centerVel.y *= -1;
      this.center.y = constrain(this.center.y, margin, height - margin);
    }

    // Recordar posición previa
    this.lastPosition.set(this.position);

    // Giro local y nueva posición
    this.theta += this.omega;
    const x = this.center.x + this.radius * cos(this.theta);
    const y = this.center.y + this.radius * sin(this.theta);
    this.position.set(x, y);

    // Salto Lévy opcional
    this._maybeLevyJump();
  }

  // Dibuja el trazo negro en la capa persistente (lo usarán típicamente los "painter")
  paint(g) {
    if (!g) return;
    g.push();
    g.stroke(255, this.brushAlpha);
    g.strokeWeight(this.brushWeight);
    g.strokeCap(ROUND);
    g.noFill();
    if (!this._suppressPaint && this.lastPosition && this.position) {
      g.line(this.lastPosition.x, this.lastPosition.y, this.position.x, this.position.y);
    }
    g.pop();
    this._suppressPaint = false;
  }

  show() {
    
    
    stroke(0);
    strokeWeight(2);
    fill(127);
    circle(this.position.x, this.position.y, this.visualR * 2);
    
    

    const endX = this.position.x + this.visualR * cos(this.theta);
    const endY = this.position.y + this.visualR * sin(this.theta);
    line(this.position.x, this.position.y, endX, endY);
  }

  // Fuerza gravitacional (solo si es atractor)
  repel(particle) {
    if (!this.isAttractor()) {
      return createVector(0, 0); // sin fuerza (para painters)
    }
    let force = p5.Vector.sub(this.position, particle.position); // hacia el atractor
    let distance = force.mag();
    distance = constrain(distance, this.softenMin, this.softenMax);
    const strength = (this.power) / (distance * distance); // + => atracción
    force.setMag(strength);
    return force;
  }

  // Colisión/choque con una partícula (usado por "painter" como obstáculo móvil)
  collide(particle) {
    const pr = particle.size * 0.5;     // radio aprox. de la partícula
    const rr = this.visualR;            // radio del repeller visual
    let d = p5.Vector.sub(particle.position, this.position);
    let dist = d.mag();
    const rSum = pr + rr;

    if (dist === 0) {
      // Evitar división por cero: crear una normal aleatoria
      d = p5.Vector.fromAngle(random(TWO_PI));
      dist = 0.0001;
    }
    if (dist < rSum) {
      const n = d.copy().div(dist);           // normal de choque (unitaria)
      const penetration = rSum - dist + 0.1;  // empujoncito extra para separar

      // Reposicionar la partícula fuera del repeller
      particle.position.add(p5.Vector.mult(n, penetration));

      // Reflejar velocidad si se movía hacia el repeller
      const vn = particle.velocity.dot(n);
      if (vn < 0) {
        const e = 0.8; // restitución (0 = inelástico, 1 = elástico)
        // v' = v - (1+e)(v·n) n
        const delta = (1 + e) * vn;
        particle.velocity.sub(p5.Vector.mult(n, delta));
        // Un poco de amortiguamiento global
        particle.velocity.mult(0.98);
      }
    }
  }
}

```


**particle.js**

```js
// The Nature of Code
// Sistema simple de partículas con herencia y polimorfismo

class Particle {
  constructor(x, y, col) {
    this.position = createVector(x, y);
    this.velocity = createVector(random(-1, 1), random(-1, 0));
    this.acceleration = createVector(0, 0);
    this.lifespan = 255.0;

    // Color asignado al nacer (resultado de lerpColor)
    this.baseColor = col ?? color(127);

    // Tamaño base
    this.size = 8;

    // Borrador
    this.eraseSpeedThreshSq = 0.04;   // ~0.2^2
    this.eraseSize = this.size * 1.6; // grosor del borrador
  }

  // Simulación (integración + vida)
  simulate() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.lifespan -= 2;
    this.acceleration.mult(0);
  }

  // Borrar en la capa 'g' cuando hay movimiento apreciable
  eraseOn(g) {
    if (!g) return;
    if (this.velocity.magSq() < this.eraseSpeedThreshSq) return; // casi quieta → no borra
    g.push();
    g.erase();         // recorta en la capa (transparente)
    g.noStroke();
    g.circle(this.position.x, this.position.y, this.eraseSize);
    g.noErase();
    g.pop();
  }

  // Método polimórfico (se sobreescribe en subclases)
  show() {
    // Por defecto no dibuja (subclases implementan)
  }

  isDead() {
    return this.lifespan < 0.0;
  }

  applyForce(f) {
    this.acceleration.add(f);
  }

  // Utilidad para aplicar fill/stroke con el alfa del lifespan
  applyStyle() {
    const r = red(this.baseColor);
    const g = green(this.baseColor);
    const b = blue(this.baseColor);
    stroke(0, this.lifespan);
    strokeWeight(2);
    fill(r, g, b, this.lifespan);
  }
}

// ---- Subclases ----

// Círculo
class CircleParticle extends Particle {
  show() {
    this.applyStyle();
    circle(this.position.x, this.position.y, this.size);
  }
}

// Cuadrado
class SquareParticle extends Particle {
  show() {
    this.applyStyle();
    push();
    rectMode(CENTER);
    translate(this.position.x, this.position.y);
    rotate(this.velocity.heading() * 0.25);
    rect(0, 0, this.size, this.size);
    pop();
  }
}

// Triángulo
class TriangleParticle extends Particle {
  show() {
    this.applyStyle();
    const s = this.size * 1.1;

    push();
    translate(this.position.x, this.position.y);
    rotate(this.velocity.heading());
    beginShape();
    vertex(+s * 0.6, 0);          // punta
    vertex(-s * 0.5, +s * 0.4);   // base abajo
    vertex(-s * 0.5, -s * 0.4);   // base arriba
    endShape(CLOSE);
    pop();
  }
}

```



**sketch.js**

```js
// One ParticleSystem
let emitter;

// Tres repellers: 1 atractor + 2 painters
let attractor;   // modo "attractor" (gravedad)
let painterA;    // modo "painter" (pinta + colisiona con partículas)
let painterB;    // modo "painter" (pinta + colisiona con partículas)

// UI: color pickers para el gradiente
let pickerA, pickerB;

// Capa persistente para la "pintura" de los painters
let paintLayer;

function setup() {
  createCanvas(640, 240);

  // Capa donde se pinta el trazo
  paintLayer = createGraphics(width, height);
  paintLayer.clear(); // transparente

  // --- Emitter con centro móvil + órbita polar ---
  const ex = width * 0.75;
  const ey = height * 0.30;
  const eR = min(width, height) * 0.10;
  emitter = new Emitter(
    ex,
    ey,
    eR,
    random(TWO_PI),
    {
      centerSpeed: 2.0, // px/frame
      omega: 0.05       // rad/frame
    }
  );

  // --- Repellers ---
  // Atractor gravitacional (no pintará)
  const ax = width * 0.25;
  const ay = height * 0.75;
  const aR = min(width, height) * 0.12;
  attractor = new Repeller(
    ax, ay, aR, random(TWO_PI),
    {
      mode: "attractor",
      centerSpeed: 5,
      omega: 0.07,
      power: 300,      // “masa” efectiva
      visualR: 16,
      brushWeight: 8,  // irrelevante si no pintamos con él
      brushAlpha: 0,   // por si llamáramos paint accidentalmente, que no marque
      levyProb: 0.012,
      levyAlpha: 1.5,
      levyMin: 12,
      levyMax: 10
    }
  );

  // Painter A
  const px1 = width * 0.35;
  const py1 = height * 0.35;
  const pR1 = min(width, height) * 0.10;
  painterA = new Repeller(
    px1, py1, pR1, random(TWO_PI),
    {
      mode: "painter",
      centerSpeed: 5,
      omega: 0.06,
      visualR: 14,
      brushWeight: 15,
      brushAlpha: 255,
      // Lévy un poco más suave
      levyProb: 0.008,
      levyAlpha: 1.6,
      levyMin: 10,
      levyMax: 45
    }
  );

  // Painter B
  const px2 = width * 0.60;
  const py2 = height * 0.65;
  const pR2 = min(width, height) * 0.09;
  painterB = new Repeller(
    px2, py2, pR2, random(TWO_PI),
    {
      mode: "painter",
      centerSpeed: 2.0,
      omega: 0.05,
      visualR: 12,
      brushWeight: 15,
      brushAlpha: 255,
      levyProb: 0.010,
      levyAlpha: 1.5,
      levyMin: 12,
      levyMax: 40
    }
  );

  // --- UI de colores ---
  pickerA = createColorPicker('#00aaff');
  pickerA.position(10, 10);
  pickerA.size(40, 20);

  pickerB = createColorPicker('#ff00aa');
  pickerB.position(60, 10);
  pickerB.size(40, 20);
}

function draw() {
  background(0);

  // Actualizar trayectorias
  emitter.update();
  attractor.update();
  painterA.update();
  painterB.update();

  // 1) Pintar los PAINTERS en la capa persistente
  painterA.paint(paintLayer);
  painterB.paint(paintLayer);

  // Colores del emitter
  emitter.setColors(pickerA.color(), pickerB.color());

  // Colisión del origin del emitter con el atractor (puedes ampliar a painters si quieres)
  if (collides(emitter.origin, emitter.collisionR, attractor.position, attractor.visualR)) {
    emitter.randomizeShape();
  }

  // Nace una partícula por frame
  emitter.addParticle();

  // Fuerzas globales
  const gravity = createVector(0, 0.1);
  emitter.applyForce(gravity);

  // Atracción gravitacional SOLO del "attractor"
  emitter.applyRepellers([attractor]);

  // 2) Simular partículas + BORRAR tinta + Colisiones con painters
  emitter.step(paintLayer, [painterA, painterB]);

  // 3) Dibujar capa y luego partículas, para que se vean encima
  image(paintLayer, 0, 0);
  emitter.render();

  // UI de los 3 repellers
  attractor.show();
  /*painterA.show();
  painterB.show();*/
}

function collides(p1, r1, p2, r2) {
  return p5.Vector.dist(p1, p2) <= (r1 + r2);
}

```

<img width="639" height="240" alt="image" src="https://github.com/user-attachments/assets/db69c9a8-2739-4488-b01d-dd8dde56183f" />











