# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 1

Cuando hablamos de arte generativo, no buscamos crear una obra específica, sino piezas únicas e impredecibles. Para lograr esa personalización en cada trazo, recurrimos a la aleatoriedad.

### Actividad 2

- Luego de leer y ver el proyecto de Sofi, la aletoriedad cumple un papel fundamental dentro de la experiencia que emite su obra pues al integrar la interactivad, los patrones de movimiento aleatorios y los cambios de color impredecibles, hace que cada vez que alguien decida reproducir su proyecto tendrá una experiencia completamente diferente e irrepetible.
- En los proyectos universitarios en los que he estado trabajando mis últimos semestres y después de hacer sus respectivos análisis, me di cuenta que los que cuentan con mayor aletoriedad son sin duda los que hemos desarrollado en sistemas fisicos interactivos, especificacmente el semestre pasado tuve la oportunidad de desarrollar un proyecto de un evento en vivo y una de las interacciones con las que contaba era que la velocidad modificaba unos visuales y eso garantizaba que cada vez que se ejecutara mi proyecto iba a ser diferente porque la velocidad nunca seria la misma siempre y eso garantizaba que la experiencia fuera irrepetible cada vez que se llevaba a cabo.

### Actividad 3: Investigación 


**Hipótesis 1:** Lo que decidí modificar es el rango del número random, ahora será 7 y  no adicionaré if´s y lo que creo que va a suceder es que el movimiento va a tender a moverse hacia abajo (y--) porque saldrán más números que se vayan al "else" y esto hará que se vayan hacia abajo.

```js
  step() {
    const choice = floor(random(7));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x--;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y--;
    }
  }
```

**Lo que realmente pasó:** El movimiento empezó a tender hacia arriba (no sabía que los y-- son valores positivos) 

**Explicación:** Luego de investigar, entendí que y-- no es la dirección como tal sino un decremento al valor de la variable y.

**Hipótesis 2:** Decidí colocarle los if's con sus respectivas condiciones a otros numéros diferentes (6,5,4) y considerando lo que pasó en la hipótesis anterior, creo que en esta ocasión sucederá que se van a ir hacia abajo. 

```js
 step() {
    const choice = floor(random(7));
    if (choice == 6) {
      this.x++;
    } else if (choice == 5) {
      this.x--;
    } else if (choice == 4) {
      this.y++;
    } else {
      this.y--;
    }
  }
```
**Lo que realmente pasó:** También se fueron hacia arriba en esta ocasión.

**Explicación:** esto sucedió porque las probabilidades de irse a y-- son mayores ya que le corresponden más números que al resto de condicionales. 

### Actividad 4

- Según la explicación del profe y los ejemplos dados en la clase, la distribución uniforme es cuando todos los numeros que salen aleatoriamente tienen la misma probabilidad de salir y por lo tanto es más impredecible. La distribución no uniforme (la del ejemplo RandomGaussian) es cuando los números que tienen más probabilidades de salir son los que están más cerca de la media y genera un comportamiento más suave y natural (se usa para hacer efectos naturales como humo).

- **VERSIÓN INVENTADA POR MANU**: En un primer momento de experimentación  (me inventé ) para implementar una distribución No uniforme en el ejemplo RandomWalker lo  primero que hice fue cambiar Random(4) por RandomGaussian() y eliminé los condicionales que no fuera el que contenía el cero. Por último, modifiqué las direcciones de los condicionales (if choice==0 a x-- y else a x++) y así logré que se moviera a la derecha.

```js
  step() {
    const choice = floor(randomGaussian());
    if (choice == 0) {
      this.x--;
    } else {
      this.x++;
    }
  }
```
<img width="191" height="136" alt="image" src="https://github.com/user-attachments/assets/9bdc45d9-9fa9-49ce-8f3d-0a6f01102b40" />

- **VERSIÓN CONSULTADA A LA IA**: PERO me parecía que no estaba teniendo ese movimiento natural que promete la distribución no uniforme así que con consejos de la IA traté de arreglarlo y llegamos a que se viera más natural.
  
```js
  step() {
   let stepx = randomGaussian() * 2 + 1;  // media = +1 → tiende a moverse a la derecha
    let stepy = randomGaussian();          // media = 0 → sin sesgo vertical

    this.x += stepx;
    this.y += stepy;
  }
```
<img width="418" height="121" alt="image" src="https://github.com/user-attachments/assets/d5f23208-172b-44de-b7ff-6ce2058b0512" />

### Actividad 5

La forma en que decidí representar la distribución normal en este nuevo sketch fue creando en vez de elipses, unas estrellas en un fondo negro con una media de 320 y DS de 60 para ambas posiciones ( X y Y ), esto ocasionó que la mayoría de las estrellas se concentraran en el centro del canvas (cerca a la media elegida). Por último, decidí que esta vez quería parametrizar la DS y la media para poder ver como varia la aparición de las estrellas según voy modificando los parámetros.

```js
let mean = 320;
let sd = 60;

let meany = 320;
let sdy = 60;

function setup() {
  createCanvas(640, 640);
  background(10, 10, 30); // Fondo nocturno
}

function draw() {
  let x = randomGaussian() * sd + mean;
  let y = randomGaussian() * sdy + meany;

  let innerRadius = 3;   // Tamaño del centro
  let outerRadius = 8;  // Tamaño de las puntas
  let npoints = 5;

  drawStar(x, y, innerRadius, outerRadius, npoints);
}

function drawStar(x, y, radius1, radius2, npoints) {
  let angle = TWO_PI / npoints;
  let halfAngle = angle / 2.0;

  push();
  translate(x, y);
  rotate(random(TWO_PI)); // orientación aleatoria
  fill(random(200, 255), random(200, 255), 0, random(100, 200)); // amarillo suave
  noStroke();
  beginShape();
  for (let a = 0; a < TWO_PI; a += angle) {
    let sx = cos(a) * radius2;
    let sy = sin(a) * radius2;
    vertex(sx, sy);
    sx = cos(a + halfAngle) * radius1;
    sy = sin(a + halfAngle) * radius1;
    vertex(sx, sy);
  }
   endShape(CLOSE); 
  pop();
}
```


[Link para ver el sketch](https://editor.p5js.org/manuuuu15281/sketches/YOBkOvfVc)

<img width="594" height="591" alt="image" src="https://github.com/user-attachments/assets/f7bd739d-95d8-44c6-94a0-8ddaeadc23d3" />

### Actividad 6 
Para hacer esta actividad, utilicé uno de los ejemplos que ya habiamos trabajado en actividades anterioes (Random walker con distribución No uniforme con tendencia a moverse a la derecha) y le añadí  la distribución Levy flight. 
1. Lo primero que hice fue utilizar el pedazo de código que sugiere el texto guía:
   
```js
   let r = random(1);
//{!3} A 1% chance of taking a large step
if (r < 0.01) {
  xstep = random(-100, 100);
  ystep = random(-100, 100);
} else {
  xstep = random(-1, 1);
  ystep = random(-1, 1);
}
```
2. En la función step() modifiqué el código original, añadí el pedazo de código sugerido y modifiqué las respectivas condiciones.

```js
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(0);
    point(this.x, this.y);
  }

  step() {
    let stepx, stepy;

    let r = random(1);
    if (r < 0.01) {
      // 1% de probabilidad de salto grande (Levy flight)
      stepx = random(-100, 100);
      stepy = random(-100, 100);
    } else {
      // 99% de probabilidad de paso pequeño con tendencia hacia la derecha
      stepx = randomGaussian() * 2 + 1;
      stepy = randomGaussian();
    }

    this.x += stepx;
    this.y += stepy;

    //limitar al canvas con ayuda de la IA jeje
    this.x = constrain(this.x, 0, width);
    this.y = constrain(this.y, 0, height);
  }
}

```
3. Por último, añadí una funcionalidad de que el random walk no se pueda salir del canvas. 

[Link para ver el sketch](https://editor.p5js.org/manuuuu15281/sketches/0nKaQAP1P)

<img width="407" height="239" alt="image" src="https://github.com/user-attachments/assets/c7a4a7dd-e075-43ff-9912-5f74706a36e5" />


