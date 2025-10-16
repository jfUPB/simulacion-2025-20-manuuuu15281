# Evidencias de la unidad 7

### ACTIVIDAD 1

**Ejemplo 1: EXIT**

<img width="366" height="162" alt="image" src="https://github.com/user-attachments/assets/6e284c42-86ca-49ec-94be-ae098bc16631" />

Este ejemplo me parece muy creativo y chistoso porque añade una escena de persecución donde la "i" representa una puerta por la que sale la "X" que tiene pies y corre.Creo para empezar a pensar en mis ideas que me gustaría crear algo que además de chistoso sea muy representativo (como el ejemplo). 

**Ejemplo 2: Tunel**

<img width="459" height="264" alt="image" src="https://github.com/user-attachments/assets/13df6aa2-fc93-4b6f-a551-b5a480271352" />


Este me pareció muy innovador, colocar la letra "n" como una representación visual del significado de la palabra. Además, el detalle de la letra atravesando la "n" me parece que el concepto está muy bien aprovechado. 

**Ejemplo 3: Condom**

<img width="388" height="162" alt="image" src="https://github.com/user-attachments/assets/2e369649-87ef-4259-afb9-929dc4383cee" />

Para este ejemplo, me llama mucho la atención cómo utilizan las formas de las letras en pro de la representación de la palabra, como es el caso de la letra C que la utilizan para que cumpla la doble función de formar la palabra "Condom" y al mismo tiempo representar visualmente un condón. 

**MIS IDEAS CON PROYECCIÓN AL APPLY :)**

Estas ideas se me ocurrieron mirando cosas a mi alrededor, esta parte me pareció la más divertida. 
<img width="853" height="505" alt="image" src="https://github.com/user-attachments/assets/6145038a-b47c-4dd9-81c3-123b2f040903" />


### ACTIVIDAD 2

**Experimimento número 1: Ground inclinado**

Para este experimento quise modificar el ground para que fuera una rampa y los objetos rodaran cuando cayeran en esta. Mi mayor dificultad fue hacer que se rotara porque lo intenté hacer desde el sketch.js y cómo el código tenía una clase especifica para el ground pues olvidé que tenía que trabajar desde allá y llamarlo en el sketch.js (me embalé porque son conceptos de POO en los me considero cruda todavía). 

![exp1_U7](https://github.com/user-attachments/assets/b432b9a5-f38b-4968-9d31-a45b2f6a100a)

Y pensé que solo inclinando el ground iban a rodar las figuras pero no fue así, tuve que modificar también el código de rect.js: 

```js
class Rect {
  constructor(x, y, w, h) {
    this.w = w;
    this.h = h;

    const options = {
      friction: 0.4,       // fricción contra la rampa (ajusta para más/menos deslizamiento)
      frictionStatic: 0.6, // “agarre” inicial antes de deslizar
      frictionAir: 0.01,   // resistencia del aire
      restitution: 0.05    // rebote bajo para que se deslicen
    };

    this.body = Bodies.rectangle(x, y, this.w, this.h, options);

    // ligera rotación inicial para que tiendan a voltear/rodar
    Body.setAngularVelocity(this.body, random(-0.2, 0.2));

    Composite.add(engine.world, this.body);
  }

  display() {
    push();
    rectMode(CENTER);
    noStroke();
    fill(30);

    const x = this.body.position.x;
    const y = this.body.position.y;
    const angle = this.body.angle;

    translate(x, y);
    rotate(angle);
    rect(0, 0, this.w, this.h);
    pop();
  }
}

```
**Engine**: Es el motor de físicas donde se calcula las fuerzas, las colisiones, gravedad y el movimiento de todos los cuerpos dentro del world. 

**World**: Es el contenedor de todos los cuerpos y objetos, es como el escenario donde ocurre toda la simulación. 

**Bodies**: Son los objetos y cuerpos con los que interactua el motor de físicas y pueden tener diferentes formas: rectangle, circle, polygon, etc. Y es importante recordar que cada cuerpo tiene sus propiedades físicas como masa, fricción o rebote, estatico si es el caso, angulo y posición. 

**Constrain**: Permite simular conexiones físicas entre los cuerpos, por ejemplo cuerdas, resortes, etc. 

**MouseConstraint**: Permite la interacción con los cuerpos usando el mouse, sirve para hacer simulaciones interactivas. 


[Link para ver el código completo](https://editor.p5js.org/manuuuu15281/sketches/mGYg0qDzg)


**Experimimento número 2:  Trampolín**
Quise hacer que el ground se comportara como un trampolín. Este fue sencillo después de haber implementado ya el experimento anterior pues no cometí los mismos errores. Lo que hice fue implementar las fisicas en los códigos de rect.js y ground.js y luego llamarlos desde el sketch.js para poderlo evidenciar en mi canvas. 

**ground.js**

```js
class Ground {
  constructor(x, y, w, h) {
    this.w = w;
    this.h = h;
    
    this.body = Bodies.rectangle(x, y, this.w, this.h, {
      isStatic: true,
      restitution: 0.95,  // muy elástico para efecto trampolín
      friction: 0.2
    });
    this.body.label = 'trampoline';

    Composite.add(engine.world, this.body);
  }
  
  display() {
    push();
    rectMode(CENTER);
    let x = this.body.position.x;
    let y = this.body.position.y; 
    translate(x, y);
    rect(0, 0, this.w, this.h);
    pop();
  }
}

````

**rect.js**

```js
class Rect {
  constructor(x, y, w, h) {
    this.w = w;
    this.h = h;

    const options = {
      friction: 0.3,
      frictionAir: 0.01,
      restitution: 0.2 // rebote base (el trampolín da el extra)
    };

    this.body = Bodies.rectangle(x, y, this.w, this.h, options);
    this.body.label = 'box';

    // Un poco de spin para hacerlo más dinámico
    Body.setAngularVelocity(this.body, 0.2);

    Composite.add(engine.world, this.body);
  }
  
  display() {
    push();
    rectMode(CENTER);
    let x = this.body.position.x;
    let y = this.body.position.y; 
    let angle = this.body.angle;
    translate(x, y);
    rotate(angle);
    rect(0, 0, this.w, this.h);
    pop();
  }
}

```

[Link para ver el código completo](https://editor.p5js.org/manuuuu15281/sketches/7QCKlzuDS)

![Exp2_U7](https://github.com/user-attachments/assets/5de32cf0-b118-40c1-bd8a-c2e75f1ac437)

### APPLY

Para mi apply pensé en muchas ideas (las que hice para la actividad 1) pero el fin de semana que salí a caminar, vi un letrero que decía la palabra "COLTEJER" cómo el edificio, y se me ocurrió la idea de hcaer una animación generativa que incluyera esta palabra y que con algunas de sus letras se creara la forma del edificio coltejer de Medellín, quería que el edificio cayera del cielo y se colocara en el lugar correspondiente para crear la palabra, además de que cuando cae salen particulas de polvo. 

Hacerlo fue muy sencillo después de haber entendido cómo funciona la librería de matters.js entonces lo que hice fue hacer los assets con IA (Gemini me ayudó) le di mi concepto y un bocetico y de una me sacó mi asset: 

<img width="816" height="255" alt="image" src="https://github.com/user-attachments/assets/e729d683-d752-4a70-85a2-39b1a848418f" />

luego lo que hice fue que para poderla ubicar de la manera que quería opté por sobreponer un asset sobre otro y que no tuvieran trigger integrado para que no colisionaran sino que se ubicaran una sobre la otra. Cómo se ve en el GIF: 


```js

if (window.p5) p5.disableFriendlyErrors = true;

const { Engine, Body, Bodies, Composite, Events } = Matter;

let engine, world;
const TEX = { base: "coejer.png", fall: "lt2.png" };
let imgBase, imgFall;

// cuerpos
let ground, baseRect, fallRect;

// medidas y helpers de dibujo
let targetW, targetH, cx, cy, topOfGround;

// partículas de polvo
const dust = []; // { body, born, ttl, r }
const TTL_MS = 900;

function preload() {
  imgBase = loadImage(TEX.base);
  imgFall = loadImage(TEX.fall);
}

function setup() {
  createCanvas(800, 400);
  noSmooth();
  imageMode(CENTER);
  angleMode(RADIANS);

  engine = Engine.create();
  world = engine.world;

  const W = width;
  const H = height;

  // --- Categorías de colisión ---
  const CAT_BASE   = 0x0001;
  const CAT_FALL   = 0x0002;
  const CAT_GROUND = 0x0004;

  // --- Suelo ---
  const GROUND_H = 24;
  const groundY = H - GROUND_H / 2;           // 388
  topOfGround = groundY - GROUND_H / 2;       // ≈376
  ground = Bodies.rectangle(W / 2, groundY, W - 40, GROUND_H, {
    isStatic: true,
    collisionFilter: { category: CAT_GROUND, mask: CAT_FALL }
  });

  // ===== Tamaño objetivo común (auto-ajustado a canvas) =====
  let tH = 300;                   // altura deseada
  const maxH = topOfGround - 10;  // margen superior
  tH = Math.min(tH, maxH);

  let tW = imgBase.width * (tH / imgBase.height);
  const maxW = W - 40;
  if (tW > maxW) {
    const f = maxW / tW;
    tW *= f;
    tH *= f;
  }
  targetW = tW;
  targetH = tH;

  // Centro común donde se superponen
  cx = W / 2;
  cy = topOfGround - targetH / 2;

  // --- Rect estático (coejer.png) ---
  baseRect = Bodies.rectangle(cx, cy, targetW, targetH, {
    isStatic: true,
    collisionFilter: { category: CAT_BASE, mask: 0 } // no colisiona
  });
  setSpriteFit(baseRect, imgBase, targetW, targetH);

  // --- Rect que cae (lt2.png) ---
  fallRect = Bodies.rectangle(cx, -targetH, targetW, targetH, {
    restitution: 0.0,
    friction: 0.6,
    frictionAir: 0.02,
    collisionFilter: { category: CAT_FALL, mask: CAT_GROUND } // solo suelo
  });
  Body.setAngle(fallRect, 0);
  Body.setAngularVelocity(fallRect, 0);
  Body.setInertia(fallRect, Infinity);
  setSpriteFit(fallRect, imgFall, targetW, targetH);

  // Añadir al mundo (el que cae después para dibujar encima)
  Composite.add(world, [baseRect, fallRect, ground]);

  // Detección de PRIMER impacto para soltar polvo y “snap”
  let puffed = false;
  Events.on(engine, "collisionStart", (evt) => {
    if (puffed) return;
    for (const pair of evt.pairs) {
      const a = pair.bodyA, b = pair.bodyB;
      const hitFallAndGround =
        (a === fallRect && b === ground) || (a === ground && b === fallRect);
      if (hitFallAndGround) {
        puffed = true;
        spawnDust(fallRect.position.x, topOfGround, targetW);
        // anclar exactamente encima tras un instante
        setTimeout(() => {
          Body.setPosition(fallRect, { x: cx, y: cy });
          Body.setVelocity(fallRect, { x: 0, y: 0 });
          Body.setStatic(fallRect, true);
        }, 60);
        break;
      }
    }
  });
}

function draw() {
  background(255);

  // Avanzar físicas usando el tiempo real entre frames (ms)
  Engine.update(engine, deltaTime);

  // --- Dibujar base (coejer.png) ---
  drawBodyImage(baseRect);

  // --- Dibujar fall (lt2.png) ---
  drawBodyImage(fallRect);

  // --- Polvo: dibujar + fade + limpieza ---
  const now = performance.now();
  for (let i = dust.length - 1; i >= 0; i--) {
    const d = dust[i];
    const t = (now - d.born) / d.ttl;
    if (t >= 1) {
      Composite.remove(world, d.body);
      dust.splice(i, 1);
      continue;
    }
    const p = d.body.position;
    const alpha = 255 * 0.22 * (1 - t); // igual que antes, pero en p5
    noStroke();
    fill(0, alpha);
    circle(p.x, p.y, d.r * 2);
  }

  // (Opcional) visual del suelo para depurar:
  // stroke(230); line(0, topOfGround, width, topOfGround);
}

// ---- Helpers ----

// Guardamos “sprite” (imagen + escala final) en el body para dibujar con p5
function setSpriteFit(body, p5img, targetW, targetH) {
  const iw = p5img?.width || 1;
  const ih = p5img?.height || 1;
  const s = Math.min(targetW / iw, targetH / ih);
  body._sprite = { img: p5img, w: iw * s, h: ih * s };
}

function drawBodyImage(body) {
  if (!body || !body._sprite) return;
  const { x, y } = body.position;
  push();
  translate(x, y);
  rotate(body.angle || 0);
  image(body._sprite.img, 0, 0, body._sprite.w, body._sprite.h);
  pop();
}

// Generar partículas de polvo físicas (círculos en Matter) que no colisionan
function spawnDust(x, y, widthSpan) {
  const n = Math.max(16, Math.floor(widthSpan / 18));
  for (let i = 0; i < n; i++) {
    const r = 3 + Math.random() * 6;
    const px = x + (Math.random() - 0.5) * widthSpan * 0.9;
    const py = y - 2;

    const p = Bodies.circle(px, py, r, {
      isStatic: false,
      density: 0.0001,
      frictionAir: 0.06,
      collisionFilter: { category: 0x8000, mask: 0 } // no colisiona
    });

    // Velocidad inicial hacia arriba en abanico
    const angle = (-Math.PI / 2) + (Math.random() - 0.5) * (Math.PI / 3);
    const speed = 3 + Math.random() * 6;
    Body.setVelocity(p, { x: Math.cos(angle) * speed, y: Math.sin(angle) * speed });

    Composite.add(world, p);
    dust.push({ body: p, born: performance.now(), ttl: TTL_MS, r });
  }
}

````
[Link para ver el código completo](https://editor.p5js.org/manuuuu15281/sketches/oaZ5bVjQY)

![Apply_U7](https://github.com/user-attachments/assets/fe8d2aca-e3fd-4203-984d-abc82c1a0a2d)

mi nota propuesta es 5.0
Porque apliqué los conceptos que aprendimos en la unidad ( la librería matters.js) y además traté de pensar muy bien cuál palabra hacer que fuera innovadora, también completé todas las actividades y en la actividad 2 experimenté con diferentes físicas y aplicarlas a los cuerpos que tengo dn mi canvas. Me gustó mucho esta unidad, fue muy divertido
