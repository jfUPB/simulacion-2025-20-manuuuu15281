# Unidad 3


## 🤔 Fase: Reflect

### ACTIVIDAD 11

**1. Escribe la ecuación vectorial de la segunda ley de Newton y explica cada uno de sus componentes.**

La ecuación vectorial de la 2da ley de newton es Ftotal = m * a, donde:

- Ftotal es la suma de todas las fuerzas que están actuando y como es un vector tiene magntiud, dirección y sentido.
- m (Masa) es un número constante y representa la cantidad de materia que hay y entre más masa haya, más dificil es para la fuerza aplicada acelerar el cuerpo.
- a (Aceleración) es un vector que indica cómo cambia la velocidad del cuerpo con el paso del tiempo.

**2. ¿Por qué es necesario multiplicar la aceleración por cero en cada frame del método update()?**

Hacer esto implica que la aceleración no se acumule frame a frame, y en caso de no multiplicarla por 0 en cada frame lo que ocurriría es que el cuerpo se aceleraría cada vez más y sin sentido, incluso si la fuerza deja de aplicarse. 

**3. Explica la diferencia entre paso por valor y paso por referencia cuando aplicamos fuerzas a un objeto.**

Cuando hacemos un paso por valor estamos creando una copia independiente del obejeto o vector y trabajamos directamente sobre la copia sin intervenir el objeto original.Por su parte, el paso por referencia se pasa la misma dirección en memoria y si haces cambios el objeto original también se modifica. 

**4. ¿Cuál es la diferencia conceptual entre modelar fuerzas (como fricción, gravedad) y simplemente definir algoritmos de aceleración?**

Cuando modelamos fuerzas estamos usando directamente la 2da Ley de Newton y además estamos teniendo en cuenta la masa del cuerpo. Por su parte, un algoritmo de aceleración es algo que define directamente cómo cambia la aceleración oo la velocidad sin tener en cuenta las fuerzas. Un ejemplo podría ser:

**Física real:** una pelota cae porque la gravedad ejerce una fuerza proporcional a la masa.

**Algoritmo directo:** una pelota cae porque yo escribí vel.y += 0.5;.


**5. ¿Qué fue lo más desafiante en la Actividad 10 (problema de los n-cuerpos)? ¿El concepto creativo, la implementación de las fuerzas o la integración de la interactividad?**

Lo que más me costó fue crear el concepto creativo, más porque era representar el problema de los N cuerpo con referencia a Alexander Calder. Mi luz en este problema fue encontrar otra fuerza atractiva para modelar (decidí modelar la Ley de Hooke) y se me abrió la mente de lo que podía llegar a hacer.

**2. ¿Las fuerzas que modelaste produjeron el comportamiento que esperabas? Describe un momento “sorpresa” (esperado o inesperado) durante el desarrollo.**

Si, logré crear el efecto resorte que tanto quería. Mi momento sorpresa fue cuando al modelar la fuerza se generó un efecto como si las particulas estuvieran en un "Colchón de agua", no entendí muy bien por qué pasó porque fue cero intensional pero se veía muy cool.

**3. ¿Cómo ha cambiado tu forma de pensar sobre la “física” en el arte generativo después de esta unidad?**

Creo que en su momento cuando vi ciencia básica y modelamineto matemático me estresaba un poco porque no lograba comprender muy bien el concepto de cada fuerza y eso no me dejaba entender los diferentes temas que me daban en las clases, pero con esta actividad verlas representadas visualmente, jugar con sus parámetros me han hehco pensar lo divertido que puede ser modelar la física y las cosas tan impredecibles e innovadoras que podemos sacar de ella. 

**4. Si tuvieras una semana más, ¿Qué otras fuerzas te gustaría modelar o cómo mejorarías tu simulación del problema de los n-cuerpos?**

Me hubiese gustado poder implementar una funcionalidad de manipular los resortes con más libertad. Me refiero a poder decidir que particula quiero que estén conectadas y cuales otras no, para hacer más interactivo mi simulación.


### ACTIVIDAD 12


