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
### Actividad 3

Cuando analizamos este código en la clase, pensé que el código iba a imprimir primero el vector con la dirección (6,9) y luego esos valores serian modificados y volveria a imprimir el vector pero con los valores actualizados. Efectivamente así sucedió, y tenía sentido por la explicación del código que el profe dió y los console.log que hay en el código. 

**Paso por valor en programación:**
Este aún no me quedó tan claro, pero investigando llegué a que es cuando pasas cosas como argumentos en funciones, JavaScript crea una copia del valor. Por tanto, cualquier cambio dentro de la función no afecta al valor original fuera de ella.

Un ejemplo puede ser: 

```js
function cambiarValor(x) {
  x = x + 1;
  console.log("Dentro de la función:", x);
}

let numero = 5;
cambiarValor(numero);
console.log("Fuera de la función:", numero);
```

**Paso por referencia en programación:**

Este lo entendí muy bien por la explicación del profe en la clase y es cuando pasamos objetos por referencia lo que se está pasando es una referencia a la ubicación en memoria del objeto original (que es lo que sucede en el código del ejemplo que el profe dió en clase antes de modificarlo). Esto quiere decir que si modificas las propiedades del objeto dentro de la función, esto si afectará al objeto original. Investigando un poco de cómo funcionaba esta manera de programar, encontré que los tipos de referencias que se pueden pasar son **Object, Array y Function**. 

Un ejemplo de esto: 
```js
function modificarObjeto(obj) {
  obj.nombre = "Ana";
}

let persona = { nombre: "Luis" };
modificarObjeto(persona);
console.log("Después de la función:", persona.nombre)
```
En el ejemplo queda en evidencia cómo pasamos como una referencia el nombre "ana" y se modificó el contenido original de la variable persona. 

En el código que el profe nos enseñó en clase se está aplicando paso por referencia y se evidencia tal cual lo que pasa en el ejemplo anterior. El profe luego hizo algunas modificaciones que nos ayudó a comprender cómo podiamos hacer un traspaso por referencia pero sin modificar el arreglo original. 

De esa actividad recordé el concepto de paso por valor y por referencia, también comprendí cómo hacer una copia de un array y trabajar sobre esta sin modificar el arreglo original. 

### Actividad 4

- **¿Para qué sirve el método mag()? Nota que hay otro método llamado magSq(). ¿Cuál es la diferencia entre ambos? ¿Cuál es más eficiente?**

  El método mag() sirve para calcular la magnitud de un vector en dos dimensiones (x , y) y este aplica la raíz cuadrada al calculo de la magnitud. Por su lado, el método magSq() calcula la magnitud del vector pero a diferencia de mag()  este devuelve lo que queda dentro de la raíz cuadrada (no la aplica). Lo que quiere decir que la segunda opción es más rápida porque evita la raíz cuadrada, lo que puede ser útil en visualizaciones en tiempo real, juegos o simulaciones.

- **¿Para qué sirve el método normalize()?**

Este método convierte un vector cualquiera en un vector unitario (un vector unitario es el que tiene magnitud 1) y se usa para indicar direcciones sin importar la distancia o para generar movimientos con velocidad constante. 

- **Te encuentras con un periodista en la calle y te pregunta ¿Para qué sirve el método dot()? ¿Qué le responderías en un frase?**

  Bro sirve para calcular el producto punto entre dos vectores, también te dice que tan alineados están dos vectores entre si.

  Y si me sigue preguntando le digo:  sirve también para ver si dos vectores se mueven en la misma dirección (dot > 0), en direcciones       opuestas (dot < 0), o si son perpendiculares (dot = 0).

- **El método dot() tiene una versión estática y una de instancia. ¿Cuál es la diferencia entre ambas?**

  La diferencia entre la versión estática y la versión de instancia del método dot() en p5.js tiene que ver con cómo se llama al método y dónde se coloca el vector base, pero hacen exactamnete lo mismo pero con sintaxis diferentes:
  
  1. **Version instancia:** se llama desde un vector en específico, y calcula el producto punto con otro vector que se pone como argumento.
     
     ```js
       let v1 = createVector(1, 2);
       let v2 = createVector(3, 4);
       let resultado = v1.dot(v2); // dot entre v1 y v2
     ```
  2. **Version estática:** Esta version llama directamnete desde la clase p5.vector y no depende de ninguna instancia. Necesita dos vectores como argumentos.
     
     ```js
       let v1 = createVector(1, 2);
       let v2 = createVector(3, 4);
       let resultado = p5.Vector.dot(v1, v2);
     ```

- **Ahora el mismo periodista curioso de antes te pregunta si le puedes dar una intuición geométrica acerca del producto cruz. Entonces te pregunta ¿Cuál es la interpretación geométrica del producto cruz de dos vectores? Tu respuesta debe incluir qué pasa con la orientación y la magnitud del vector resultante.**

  El producto cruz entre dos vectores da cómo resultado un vector que sale de manera ortogonal (perpendicular) al plano formado entre los dos vectores originales y además su magnitud es igual al área del pararelogramo que se forma con esos dos vectores base.

- **¿Para que te puede servir el método dist()?**

  Este método nos sirve para calcular la distancia que hay entre dos puntos, lo puede hacer mediante coordenadas o vectores. Puede ser muy útil para detectar si el mouse está cerca de algún objeto y quiero agregar alguna interactividad o dibujar líneas o curvas entre puntos que estén a cierta distancia.

- **¿Para qué sirven los métodos normalize() y limit()?**

  El método normalize() sirve para convertir cualquier vector en un vector unitario (ayuda si necesitamos tener movimientos con velocidades constantes). Por su lado el método limit() permite "cortar" la magnitud de un vector para evitar que pase de una velocidad o fuerza máxima pero siempre mantiene su dirección.


### Actividad 5

**Código de p5.js**

```js
let tiempo = 0;

let mover = 1; 

function setup() {
    createCanvas(400, 400);
}

function draw() {
    background(200);
  
   translate(50, 50); // trasladar el punto de origen a las coordenadas (50,50)
  
    let v0 = createVector(0, 0); // este en el 0,0
    let v1 = createVector(300, 0);
    let v2 = createVector(0, 300);
    let v4 = createVector(-300,300);
    let v3 = p5.Vector.lerp(v1, v2, tiempo);
  
    
    let red1= color(255,0,0);
    let blue1= color(0,0,255);
    let degradado = lerpColor(blue1, red1, mover);
  
  
  
    //lerpColor(c1, c2, amt)
  
  tiempo+= 0.01*mover;
   
  if (tiempo <=0) 
    {
      
      mover =1;
    }
  if ( tiempo >=1)
    {
      mover=-1;
    }
  
    drawArrow(v0, v1, 'red');
    drawArrow(v0, v2, 'blue');
    drawArrow(v1, v4, 'green');
    drawArrow(v0, v3, 'purple');
    drawArrow(v0, v3, degradado);
  
}



function drawArrow(base, vec, myColor) {
    push();
    stroke(myColor);
    strokeWeight(3);
    fill(myColor);
    translate(base.x, base.y);
    line(0, 0, vec.x, vec.y);
    rotate(vec.heading());
    let arrowSize = 7;
    translate(vec.mag() - arrowSize, 0);
    triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
    pop();
}
```

- **¿Cómo funciona lerp() y lerpColor() ?**

  lerp() es una función que se usa para hacer interpolación lineal entre dos coordenadas o valores numéricos. Por su parte, el método lerpColor() se usa para hacer interpolaciones de color (hacer una mezcla gradual).

  En mi caso no puede

  

  



  

  


  




 
  

  









