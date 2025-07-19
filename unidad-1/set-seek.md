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

- **VERSIÓN CONSULTADA A LA IA**: PERO me parecía que no estaba teniendo ese movimiento natural que promete la distribución no uniforme así que con consejos de la IA traté de arreglarlo y llegamos a que se viera más natural.
  
```js
  step() {
   let stepx = randomGaussian() * 2 + 1;  // media = +1 → tiende a moverse a la derecha
    let stepy = randomGaussian();          // media = 0 → sin sesgo vertical

    this.x += stepx;
    this.y += stepy;
  }
```
  



