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




