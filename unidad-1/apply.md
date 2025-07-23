# Unidad 1

## 🛠 Fase: Apply
Una obra generativa es una creación que se basa en la aletoriedad junto con términos matemáticos que generan patrones impredecibles y hace que cada reproducción de la obra sea una experiencia completamente diferente.

Lo que decidí hacer finalmente fue generar una línea que se mueve, cambia de color y modifica su grosor por el método del ruido de Perlin (se puede controlar con un slider el color y la manera en que se mueve), además de que cuenta con Levy flight integrado y de manera repentina la línea cambia de posición. La interactividad que decidí incluir es que con el mouse puedes colocar elipses que contienen un colisionador con el que la línea podrá chocar. También cuenta con un botón para limpiar el lienzo si así se desea. 

Intenté hacer que el color del fondo se pudiera modificar con los valores de una distribución normal y que la media se controlara por medio de otro slider pero no salió como quería. Así que me quede solo con el ruido de perlin y el Levy flight. Dejo del link del intento fallido, porque si bien la funcionabilidad del fondo si está ejecutandose bien, el dibujo de la linea no aparece más y pues parece es más  un juego que una obra generativa :( 

[Link para ver el intento fallido](https://editor.p5js.org/manuuuu15281/sketches/-atXmvZCO)

Con el que me quedé para la actividad fue este: 

```js
let x, y;
let t = 0;
let ct = 1000;
let timeSlider;
let obstacles = []; // Array para almacenar los obstáculos
let obstacleRadius = 20; // Radio de los obstáculos

function setup() {
    createCanvas(800, 400);
    background(0);
    x = width / 2;
    y = height / 2;
    
    // Crear controles
    createP("Velocidad del tiempo (t):");
    timeSlider = createSlider(0.001, 0.1, 0.01, 0.001);
    timeSlider.style('width', '200px');
    
    createP("Haz clic en el canvas para colocar obstáculos");
    createP("La línea hará saltos aleatorios (Levy Flight) automáticamente");
    createP("El grosor, color y posición varían con ruido Perlin");
    
    // Botón para limpiar obstáculos
    let clearButton = createButton('Limpiar obstáculos');
    clearButton.mousePressed(clearObstacles);
}

function draw() {
    // Leer valor del slider
    let timeStep = timeSlider.value();
    
    // Implementar Levy Flight para reposicionamiento aleatorio
    let r = random(1);
    let isLevyJump = false;
    
    // 1% de probabilidad de hacer un salto largo (Levy Flight)
    if (r < 0.01) {
        // Salto largo - reposicionamiento aleatorio
        let levyX = random(50, width - 50);  // Evitar bordes
        let levyY = random(50, height - 50);
        
        // Verificar que el nuevo punto no esté en un obstáculo
        if (!checkCollisionWithObstacles(levyX, levyY)) {
            x = levyX;
            y = levyY;
            isLevyJump = true;
            
            // Cambiar también el tiempo para nueva dirección
            t += random(1, 3);
        }
    }
    
    let dx, dy, newX, newY;
    
    if (!isLevyJump) {
        // Movimiento normal controlado por ruido Perlin
        let angle = noise(t) * TWO_PI * 2;
        let stepSize = 2;
        dx = cos(angle) * stepSize;
        dy = sin(angle) * stepSize;
        
        // Calcular nueva posición
        newX = x + dx;
        newY = y + dy;
    } else {
        // Si fue un salto Levy, no mover más en este frame
        newX = x;
        newY = y;
    }
    
    // Verificar colisión con obstáculos (solo si no fue un salto Levy)
    let collision = false;
    if (!isLevyJump) {
        collision = checkCollisionWithObstacles(newX, newY);
    }
    
    if (collision) {
        // Si hay colisión, cambiar dirección dramáticamente
        t += random(0.5, 1.5);
        
        // Recalcular con nueva dirección
        let angle = noise(t) * TWO_PI * 2;
        dx = cos(angle) * 2;
        dy = sin(angle) * 2;
        newX = x + dx;
        newY = y + dy;
        
        // Verificar que la nueva dirección no cause otra colisión inmediata
        let attempts = 0;
        while (checkCollisionWithObstacles(newX, newY) && attempts < 10) {
            t += random(0.3, 0.8);
            angle = noise(t) * TWO_PI * 2;
            dx = cos(angle) * 2;
            dy = sin(angle) * 2;
            newX = x + dx;
            newY = y + dy;
            attempts++;
        }
    }
    
    // Mantener dentro del canvas (solo verificar si no fue salto Levy)
    if (!isLevyJump && (newX < 0 || newX > width || newY < 0 || newY > height)) {
        t += 0.1;
        return;
    }
    
    // Color controlado con Perlin noise
    let r_color = noise(ct) * 255;
    let g = noise(ct + 100) * 255;
    let b = noise(ct + 200) * 255;
    
    // Grosor controlado con Perlin noise (usando offset diferente)
    let thickness = map(noise(ct + 300), 0, 1, 3, 25); // Grosor entre 3 y 25 píxeles
    
    // Cambiar color y grosor si fue un salto Levy
    if (isLevyJump) {
        stroke(255, 255, 0, 150); // Amarillo semitransparente para saltos Levy
        strokeWeight(8); // Grosor fijo para saltos Levy para mantener consistencia visual
        // Dibujar una línea punteada hacia la nueva posición
        drawingContext.setLineDash([5, 5]);
        line(x, y, newX, newY);
        drawingContext.setLineDash([]); // Resetear línea
    } else {
        stroke(r_color, g, b);
        strokeWeight(thickness); // Usar grosor variable
        // Dibujar línea solo si hay movimiento real
        if (dist(x, y, newX, newY) > 0) {
            line(x, y, newX, newY);
        }
    }
    
    // Actualizar posición y tiempo
    if (!isLevyJump) {
        x = newX;
        y = newY;
    }
    t += timeStep;
    ct += 0.005;
    
    // Dibujar obstáculos
    drawObstacles();
}

function mousePressed() {
    // Verificar si el clic está dentro del canvas
    if (mouseX >= 0 && mouseX <= width && mouseY >= 0 && mouseY <= height) {
        // Agregar nuevo obstáculo en la posición del mouse
        obstacles.push({
            x: mouseX,
            y: mouseY,
            radius: obstacleRadius
        });
    }
}

function checkCollisionWithObstacles(newX, newY) {
    for (let obstacle of obstacles) {
        let distance = dist(newX, newY, obstacle.x, obstacle.y);
        if (distance < obstacle.radius + 3) { // +3 para dar un poco de margen
            return true;
        }
    }
    return false;
}

function drawObstacles() {
    // Dibujar obstáculos como elipses blancas con borde
    fill(0);
    stroke(255);
    strokeWeight(2);
    
    for (let obstacle of obstacles) {
        ellipse(obstacle.x, obstacle.y, obstacle.radius * 2, obstacle.radius * 2);
    }
    
    // No necesitamos restaurar strokeWeight aquí ya que se establece dinámicamente en draw()
    noFill();
}

function clearObstacles() {
    obstacles = [];
    // Limpiar el canvas
    background(0);
}
```

[Link para ver mi obra generativa](https://editor.p5js.org/manuuuu15281/sketches/84HaK1Oob)

<img width="794" height="390" alt="image" src="https://github.com/user-attachments/assets/dba98f82-2996-44cf-9be9-f093b5715f36" />
