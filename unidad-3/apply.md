# Unidad 3


## 🛠 Fase: Apply

Para mi obra generativa de esta unidad quise modelar la ley de Hooke: 

<img width="127" height="45" alt="image" src="https://github.com/user-attachments/assets/92373461-9576-4df6-8112-b6709bf0ccbc" />

Y la integre con el modelo de las N particulas, para empezar creé un grid/ malla: 

```js
// Cálculo de dimensiones
cols = floor(width / spacing) + 1;  // Columnas
rows = floor(height / spacing) + 2; // Filas

// Creación de N = cols × rows partículas
for (let j = 0; j < rows; j++) {
    for (let i = 0; i < cols; i++) {
        particles.push(new Particle(i * spacing, j * spacing, i, j));
    }
}
````
Y para cumplir con el problema de las N particulas las conecté a todas entre sí con un sistema de masa-resortes (con la ley de hooke)

```js
// Conexiones horizontales (→)
if (i != 0) {
    let a = particles[i + j * cols];        // Partícula actual
    let b = particles[i - 1 + j * cols];    // Partícula izquierda
    springs.push(new Spring(k, spacing, a, b));
}

// Conexiones verticales (↓)
if (j != 0) {
    let c = particles[i + j * cols];        // Partícula actual
    let d = particles[i + (j - 1) * cols];  // Partícula superior
    springs.push(new Spring(k, spacing, c, d));
}
```
El sistema no solo cuenta con la fuerza elástica de la ley de hooke, sino también con fuerza de Atracción/Repulsión entre las particulas. 

**1. Fuerza elástica**

   ```js
    // En la clase Spring
    let force = p5.Vector.sub(this.a.pos, this.b.pos);  // Vector distancia
    let d = force.mag();                                // Magnitud
    let stretch = d - this.restLength;                  // Deformación
    force.normalize();
    force.mult(-1 * this.k * stretch);                 // F = -k∆x

   ```
**2. Fuerza de Atracción/Repulsión**

```js
    // En applyAttRep()
    for (let nei of neighbors) {
        let target = particles[this.index.x + nei[0] + (this.index.y + nei[1]) * cols];
            if (target.active === true) {
                let force = p5.Vector.sub(this.pos, target.pos);
                let fs = force.copy().mult((target.radius - baseR) / (d * 0.035));
                this.acc.add(fs);
            }
        }
```
Y por último la dinámica del sistema se da por medio de una integración  númerica por el método de Euler:

```js
// En Particle.update()
this.vel.add(this.acc);        // v(t+1) = v(t) + a(t)∆t
this.pos.add(this.vel);        // x(t+1) = x(t) + v(t)∆t
this.acc.mult(0);              // Reset aceleración
this.vel.mult(0.98);           // Amortiguamiento
```

**CONCEPTO**

Decidí hacer una malla de particulas que estén conectadas entre sí por medio de resortes. Esto me pareció funcional teniendo en cuenta la teoría del problema de las N particulas, así que para complementar mi obra le añadí con ayuda de la IA un efecto visual tipo Ola con color rojo, amarillo, azul, blanco y negro (los colores representativos de la obras de Calder) y además el cambio de color se da con ruido perlin. Por último, la malla cada cierto tiempo y de manera aleatoria le rebotan algunas de sus particulas (funciona con levy flight) y esto hace que la malla también se mueva e interactue entre sí por su propia cuenta. 

[Link para ver el Sketch](https://editor.p5js.org/manuuuu15281/sketches/0ViHh0kbj)

```js
let particles = [];
let springs = [];
let k = 0.25;
let spacing, baseR, maxR;

// Variables para Ruido de Perlin
let noiseScale = 0.01;
let timeOffset = 0;
let colorNoiseScale = 0.008;

// Paleta de colores de Alexander Calder (reordenada por predominancia)
let calderColors = [
    [220, 20, 20],   // Rojo vibrante (más predominante)
    [255, 200, 0],   // Amarillo Calder intenso
    [30, 100, 200],  // Azul primario profundo
    [220, 20, 20],   // Rojo duplicado para mayor frecuencia
    [255, 200, 0],   // Amarillo duplicado
    [0, 0, 0],       // Negro
    [255, 255, 255], // Blanco
    [30, 100, 200],  // Azul duplicado
];

// Variables para Lévy Flight
let levyActive = false;
let levyParticles = []; // Índices de partículas que realizan Lévy flights
let levyTimer = 0;
let levyInterval = 300; // frames entre Lévy flights

let neighbors = [
    [1, 0],
    [1, 1],
    [0, 1],
    [-1, 0],
    [-1, -1],
    [0, -1],
    [1, -1],
    [-1, 1]
];

function setup() {
    createCanvas(windowWidth, windowHeight);

    // Botón para activar/desactivar Lévy Flight
    let levyBtn = createButton('Toggle Lévy Flight');
    levyBtn.position(80, 50);
    levyBtn.mousePressed(() => { levyActive = !levyActive; });

    if (width < height) {
        spacing = width * 0.08;
        cols = floor(width / spacing) + 1;
        rows = floor(height / spacing) + 2;
    } else {
        spacing = height * 0.08;
        cols = floor(width / spacing) + 2;
        rows = floor(height / spacing) + 1;
    }
    baseR = spacing * 0.2;
    maxR = spacing * 0.8;

    for (let j = 0; j < rows; j++) {
        for (let i = 0; i < cols; i++) {
            particles.push(new Particle(i * spacing, j * spacing, i, j));

            if (i === 0 || j === 0 || i === cols - 1 || j === rows - 1) {
                let index = i + j * cols;
                particles[index].locked = true;
            }

            if (i != 0) {
                let a = particles[i + j * cols];
                let b = particles[i - 1 + j * cols];
                springs.push(new Spring(k, spacing, a, b));
                if (j != 0) {
                    let c = particles[i + j * cols];
                    let d = particles[i + (j - 1) * cols];
                    springs.push(new Spring(k, spacing, c, d));
                }
            }
        }
    }
}

function draw() {
    background(0);

    // Aplicar Lévy Flight periódicamente
    if (levyActive) {
        levyTimer++;
        if (levyTimer >= levyInterval) {
            applyLevyFlight();
            levyTimer = 0;
        }
    }

    // Actualizar tiempo para el ruido de Perlin (ola más intensa)
    timeOffset += 0.035;

    for (let sp of springs) {
        sp.update();
    }

    for (let p of particles) {
        if (!p.locked) {
            p.checkMouseHover();
            p.applyPerlinNoise(); // Aplicar ruido de Perlin
            p.show();
            p.update();
            p.applyAttRep();
        }
    }

    if (mouseIsPressed) {
        let active;
        for (let p of particles) {
            let distance = dist(p.pos.x, p.pos.y, mouseX, mouseY);
            if (distance < p.radius) {
                active = p;
                p.active = true;
            }
        }
        if (active) {
            active.vel.set(0, 0);
            active.targetR = maxR;
        }
    } else {
        for (let p of particles) {
            p.active = false;
            p.targetR = baseR;
        }
    }
}

// Función para aplicar Lévy Flight a partículas aleatorias
function applyLevyFlight() {
    // Seleccionar 3-5 partículas aleatorias no bloqueadas
    let numParticles = floor(random(3, 6));
    levyParticles = [];
    
    for (let i = 0; i < numParticles; i++) {
        let attempts = 0;
        let randomParticle;
        do {
            let randomIndex = floor(random(particles.length));
            randomParticle = particles[randomIndex];
            attempts++;
        } while (randomParticle.locked && attempts < 50);
        
        if (!randomParticle.locked) {
            levyParticles.push(randomParticle);
            randomParticle.applyLevyJump();
        }
    }
}

// Generar número aleatorio con distribución de Lévy
function levyRandom(alpha = 1.5) {
    // Aproximación simple de distribución de Lévy usando Box-Muller
    let u = random(0.01, 1);
    let v = random(-PI, PI);
    return pow(u, -1/alpha) * cos(v);
}

class Particle {
    constructor(x, y, ix, iy) {
        this.index = {
            x: ix,
            y: iy
        };
        this.pos = createVector(x, y);
        this.originalPos = createVector(x, y); // Posición original para referencia
        this.vel = createVector();
        this.acc = createVector();
        this.mass = 1;

        this.locked = false;
        this.active = false;
        this.radius = baseR;
        this.targetR = baseR;

        // Inicializar con un color de Calder aleatorio
        let randomColorIndex = floor(random(calderColors.length));
        this.col = color(calderColors[randomColorIndex][0], 
                        calderColors[randomColorIndex][1], 
                        calderColors[randomColorIndex][2]);
        this.hoverCol = color(0, 255, 0);
        
        // Variables para ruido de Perlin
        this.noiseOffsetX = random(1000);
        this.noiseOffsetY = random(1000);
        this.colorNoiseOffset = random(1000);
        
        // Variables para Lévy Flight
        this.isLevyActive = false;
        this.levyDecay = 0;
    }

    applyForce(force) {
        let f = force.copy();
        f.div(this.mass);
        this.acc.add(f);
    }

    // Aplicar ruido de Perlin como fuerza sutil
    applyPerlinNoise() {
        if (!this.locked) {
            // Fuerza basada en ruido de Perlin
            let noiseX = noise(this.pos.x * noiseScale + this.noiseOffsetX, 
                              this.pos.y * noiseScale, timeOffset);
            let noiseY = noise(this.pos.x * noiseScale, 
                              this.pos.y * noiseScale + this.noiseOffsetY, timeOffset);
            
            // Convertir ruido a fuerza direccional
            let forceX = map(noiseX, 0, 1, -0.5, 0.5);
            let forceY = map(noiseY, 0, 1, -0.5, 0.5);
            
            let perlinForce = createVector(forceX, forceY);
            this.applyForce(perlinForce);
            
            // Modificar color basado en ruido de Perlin con paleta Calder
            this.updateCalderWaveColor();
        }
    }
    
    // Actualizar color usando ruido de Perlin con efecto ola intenso
    updateCalderWaveColor() {
        // Crear ondas de color más pronunciadas y rápidas
        let waveX = this.pos.x * 0.004 + timeOffset * 0.8;
        let waveY = this.pos.y * 0.004 + timeOffset * 0.6;
        
        // Onda principal más dramática
        let mainWave = noise(waveX, waveY, timeOffset * 0.3);
        
        // Onda secundaria para contraste fuerte
        let secondWave = noise(waveX * 1.8, waveY * 2.2, timeOffset * 0.5);
        
        // Onda terciaria para patrones complejos
        let detailWave = noise(waveX * 3.5, waveY * 4, timeOffset * 0.7);
        
        // Combinar ondas con más contraste
        let combinedNoise = (mainWave * 0.7 + secondWave * 0.2 + detailWave * 0.1);
        
        // Aumentar el contraste del ruido
        combinedNoise = pow(combinedNoise, 1.5); // Curva exponencial para más contraste
        
        // Mapear a índices de colores con transiciones más abruptas
        let colorIndex = floor(map(combinedNoise, 0, 1, 0, calderColors.length));
        colorIndex = constrain(colorIndex, 0, calderColors.length - 1);
        
        // Reducir la interpolación para colores más puros
        let nextColorIndex = (colorIndex + 1) % calderColors.length;
        let lerpAmount = map(combinedNoise % 0.25, 0, 0.25, 0, 1); // Transiciones más rápidas
        
        // Crear colores más saturados
        let currentCalderColor = color(calderColors[colorIndex][0], 
                                     calderColors[colorIndex][1], 
                                     calderColors[colorIndex][2]);
        let nextCalderColor = color(calderColors[nextColorIndex][0], 
                                  calderColors[nextColorIndex][1], 
                                  calderColors[nextColorIndex][2]);
        
        // Interpolación más dramática entre colores
        let finalColor = lerpColor(currentCalderColor, nextCalderColor, lerpAmount);
        
        // Modulación de intensidad más fuerte
        let intensityWave = noise(this.pos.x * 0.006, this.pos.y * 0.006, timeOffset * 0.2);
        let intensity = map(intensityWave, 0, 1, 0.8, 1.0); // Rango más alto
        
        // Ajustar saturación y brillo
        let r = red(finalColor) * intensity;
        let g = green(finalColor) * intensity;
        let b = blue(finalColor) * intensity;
        
        // Si no está en modo hover, active, o Levy, aplicar color Calder con onda
        if (!this.active && !this.isLevyActive && dist(this.pos.x, this.pos.y, mouseX, mouseY) > this.radius) {
            this.col = lerpColor(this.col, color(r, g, b), 0.15); // Transición más rápida
        }
    }
    
    // Aplicar salto de Lévy
    applyLevyJump() {
        if (!this.locked) {
            // Generar magnitud del salto usando distribución de Lévy
            let jumpMagnitude = abs(levyRandom(1.5)) * spacing * 0.3;
            jumpMagnitude = constrain(jumpMagnitude, spacing * 0.1, spacing * 2);
            
            // Dirección aleatoria
            let angle = random(TWO_PI);
            let levyForce = createVector(cos(angle), sin(angle));
            levyForce.mult(jumpMagnitude);
            
            // Aplicar la fuerza de Lévy
            this.applyForce(levyForce);
            
            // Marcadores visuales
            this.isLevyActive = true;
            this.levyDecay = 60; // Frames para mostrar efecto visual
            this.targetR = maxR * 1.2; // Hacer la partícula más grande temporalmente
        }
    }

    update() {
        this.vel.add(this.acc);
        this.pos.add(this.vel);
        this.acc.mult(0);
        this.vel.mult(0.98); // damping

        this.radius += 0.1 * (this.targetR - this.radius);
        
        // Actualizar efectos de Lévy
        if (this.isLevyActive) {
            this.levyDecay--;
            if (this.levyDecay <= 0) {
                this.isLevyActive = false;
                this.targetR = baseR;
            }
        }
        
        // Color base con interpolación (removido particlesColor)
        if (!this.isLevyActive && !this.active) {
            // Aplicar color Calder con ondas siempre
            this.updateCalderWaveColor();
        }
    }

    applyAttRep() {
        for (let nei of neighbors) {
            let targetIndex = this.index.x + nei[0] + (this.index.y + nei[1]) * cols;
            if (targetIndex >= 0 && targetIndex < particles.length) {
                let target = particles[targetIndex];
                if (target && target.active === true) {
                    let force = p5.Vector.sub(this.pos, target.pos);
                    let d = force.mag();
                    force.normalize();
                    let fs = force.copy().mult((target.radius - baseR) / (d * 0.035));
                    this.acc.add(fs);

                    this.targetR = baseR * map(d, 0, spacing, 0, 0.5);
                    this.col = lerpColor(color(255, 30, 0), color(0), this.radius / baseR);
                }
            }
        }
    }

    checkMouseHover() {
        let distance = dist(this.pos.x, this.pos.y, mouseX, mouseY);
        if (distance < this.radius) {
            fill(this.hoverCol);
        } else {
            fill(this.col);
        }
    }

    show() {
        noStroke();
        
        // Efecto visual especial para partículas con Lévy Flight activo
        if (this.isLevyActive) {
            // Aura pulsante
            let auraAlpha = map(this.levyDecay, 0, 60, 0, 100);
            fill(255, 100, 0, auraAlpha);
            ellipse(this.pos.x, this.pos.y, this.radius * 4);
            
            // Partícula principal con color especial
            fill(255, 150, 0);
        } else {
            fill(this.col);
        }
        
        ellipse(this.pos.x, this.pos.y, this.radius * 2);
        
        // Mostrar trail sutil para partículas con Lévy
        if (this.isLevyActive && this.levyDecay > 30) {
            stroke(255, 100, 0, 50);
            strokeWeight(2);
            line(this.originalPos.x, this.originalPos.y, this.pos.x, this.pos.y);
            noStroke();
        }
    }
}

class Spring {
    constructor(k, restLength, a, b) {
        this.k = k; // Constante de elasticidad
        this.restLength = restLength; // Longitud en reposo del resorte
        this.a = a; // Partícula A
        this.b = b; // Partícula B
    }

    update() {
        // Vector que apunta de la partícula B a la partícula A
        let force = p5.Vector.sub(this.a.pos, this.b.pos);

        // Calcular la distancia entre A y B
        let d = force.mag();

        // Calcular la extensión o compresión del resorte
        let stretch = d - this.restLength;

        // Calcular la fuerza del resorte de acuerdo a la Ley de Hooke
        force.normalize();
        force.mult(-1 * this.k * stretch);

        // Aplicar la fuerza a ambas partículas
        this.a.applyForce(force);
        this.b.applyForce(force.mult(-1)); // Fuerza en la dirección opuesta para B
    }
}
```

<img width="915" height="722" alt="image" src="https://github.com/user-attachments/assets/ea15c8c7-9746-43ab-b695-4ecdda71ef02" />






