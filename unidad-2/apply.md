# Unidad 2


## 🛠 Fase: Apply

El concepto para mi apply es un corral de gallinas, a esto llegué luego de decidir que quería implementar una aceleración que funcionara de la siguiente manera: particulas se alejan del mouse. Luego de definir eso, pensé en algo que pudiera implementar y que hiciera match con la aceleración que decidí implementar.

- **¿Cómo piensas aplicar el marco MOTION 101 y por qué?**

  Pienso aplicar el motion 101 para que la acelelración cambie la veolocidad apenas el perro se acerque a las gallinas (las gallinas reaccionan en cierto tiempo y eso hace que su velocidad cambie), también tendré que limitar la velocidad de las gallinas (Maxspeed) para que no se descontrolen despues de varias interacciones y por último la velocidad cambiará la posición de las gallinas.

- **¿Qué algoritmo de aceleración vas a utilizar? ¿Por qué?**

  Usaré un tipo de aceleración repulsión hacia el mouse y en lugar de que el objeto se acerque al mouse, este le huye si está muy cerca. El código base que usé es el siguiente (la IA me ayudó a crearlo porque aunque tenía la idea no tenía muy claro cómo implementarla en el código).

  ```js
  let dir = createVector(mouseX, mouseY).sub(position);
  let distancia = dir.mag();
  if (distancia < 100) {
    dir.mult(-1);         // Invertimos la dirección: alejamiento
    dir.setMag(0.5);      // Magnitud controlada
    acceleration = dir;
  } else {
    acceleration = createVector(0, 0.1); // gravedad normal si está lejos
  }
  
  ```

- **Código del apply**

  ```js
  let gallinas = [];
  
  function setup() {
    createCanvas(700, 500);
    textAlign(CENTER, CENTER);
    textSize(24); // Tamaño del emoji
  
    // Crear varias gallinas
    for (let i = 0; i < 30; i++) {
      gallinas.push(new Gallina(random(width), random(height)));
    }
  }
  
  function draw() {
    background(0,128,0);
  
    // Dibujar al "perro" 🐶
    textSize(30);
    text('🐶', mouseX, mouseY);
  
    // Actualizar y mostrar cada gallina 🐔
    for (let g of gallinas) {
      g.update();
      g.show();
    }
  }
  
  // Clase Gallina
  class Gallina {
    constructor(x, y) {
      this.position = createVector(x, y);
      this.velocity = p5.Vector.random2D().mult(1.5);
      this.acceleration = createVector(0, 0);
      this.maxSpeed = 3;
      this.maxForce = 0.2;
    }
  
    update() {
      // Vector desde el mouse (perro) hacia la gallina
      let mouse = createVector(mouseX, mouseY);
      let dir = p5.Vector.sub(this.position, mouse); // dirección contraria al perro
      let d = dir.mag();
  
      if (d < 200) {
        dir.setMag(this.maxForce); // fuerza de repulsión
        this.acceleration = dir;
      } else {
        this.acceleration.mult(0); // no hay repulsión si está lejos
      }
  
      // Motion 101
      this.velocity.add(this.acceleration);
      this.velocity.limit(this.maxSpeed);
      this.position.add(this.velocity);
  
      // Rebote en bordes (el corral)
      if (this.position.x < 0 || this.position.x > width) {
        this.velocity.x *= -1;
        this.position.x = constrain(this.position.x, 0, width);
      }
      if (this.position.y < 0 || this.position.y > height) {
        this.velocity.y *= -1;
        this.position.y = constrain(this.position.y, 0, height);
      }
    }
  
    show() {
      // Dibujar la gallina como un emoji 🐔
      textSize(24);
      text('🐔', this.position.x, this.position.y);
    }
  }
  
  ```

[Link para acceder al código del corral de gallinas](https://editor.p5js.org/manuuuu15281/sketches/06aGrnRFO)

<img width="889" height="641" alt="image" src="https://github.com/user-attachments/assets/74db02b6-9f81-4943-959d-d7c96057481d" />


