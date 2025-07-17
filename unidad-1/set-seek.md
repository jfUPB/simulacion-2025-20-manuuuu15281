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




