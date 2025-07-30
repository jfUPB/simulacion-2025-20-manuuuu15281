# Unidad 2

## 🔎 Fase: Set + Seek

### Actividad 1

**¿Cómo funciona la suma dos vectores en p5.js?**

La suma de vectores está dada por el método .add, la cual sirve para sumar vectores en este lenguaje y para utilizarlo se puede hacer de dos maneras: 
- La primera es sumar componente a componente de dos vectores: Aquí lo que hacemos es que modificamos v1 sumandole las componentes del v2.
  
  ```js
  let v1 = createVector(3, 4);
  let v2 = createVector(1, 2);

  v1.add(v2);
  ```
- Otra manera de sumar vectores es sumarle componentes individuales. 

  ```js
  let v1 = createVector(3, 4);
  
  v1.add(1,2)
  ```
y lo que hace es que le suma los nuevos valores a las componentes del vector respectivamente; para el componente X sería 3+1=4 y para Y sería 4+2= 6. Las nuevas componentes serian v1= (4,6).

**¿Por qué esta línea position = position + velocity; no funciona?**

En JavaScript, el operador + no sabe sumar objetos p5.Vector y .add() está definido explícitamente para hacer suma vectorial.

[Link para ver el ejemplo comentado de la clase](https://editor.p5js.org/manuuuu15281/sketches/e307PnlQP)

### Actividad 2
Para desarrolar esta actividad utilicé el ejemplo del random Walker. Para hacer que el código funcionara con vectores en la clase walker creé una variable donde almacené la dirección del vector en X y Y. También  en el show() modificamos los parametos de point() y le añadimos el nuevo nombre (position) para que los reconociera con el nuevo cambio (la variable que creé en Walker se llama this.position) y por último le agregué a los condicionales el nombre position. 

```js
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

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
    
    this.position=createVector(width/2, height /2);//No se me puede olvidar el this e indica que es un miembro del objeto
  }

  show() {
    stroke(0);
    point(this.position.x, this.position.y);
  }

  step() {
  
    const choice = floor(random(4));
    if (choice == 0) {
      this.position.x++;
    } else if (choice == 1) {
      this.position.x--;
    } else if (choice == 2) {
      this.position.y++;
    } else {
      this.position.y--;
    }
  }
}

```



