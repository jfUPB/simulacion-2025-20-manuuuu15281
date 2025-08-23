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

Analicé la obra de Sebas Torres. 

**1. ¿La obra visual refleja la inspiración en las esculturas cinéticas de Calder y el problema de los n-cuerpos?**

La pieza de sebas si refleja el problema de los N-cuerpos (los plates están vinculador por el concepto de acción-reacción lo que hace que lo que empuje a una particula empueje a la otra), pero lo que noté es que visualmente está mezclado con resortes, vientos y drag. 

**2. ¿Se aplican correctamente las leyes de Newton? ¿Las fuerzas se acumulan apropiadamente?**


En el código de sebas vi que si está aplicada la 2da ley de newton y se acumulan las fuerzas (sebas suma todo en acc y resetea en cada frame). 

**3. ¿El modelado de fuerzas es interesante y genera comportamientos únicos?**

Las fuerzas que modela sebas si generan comportamiento muy interesantes, precisamente porque combina varios mundos de la física lo que lo hace un diseño híbrido porque la gravedad N-cuerpos asegura cierta complejidad, los resortes + viento + drag le dan estabilidad y el rendering con estelas y glow traduce esas dinámicas en un lenguaje  mucho más artístico.

**4. ¿La interacción permite explorar diferentes aspectos del sistema de fuerzas?**

Si, sebas permite que las personas puedan aplicar viento, mover las particulas con el mouse, cambiar la paleta de colores y limpiar el lienzo. De todas maneras propongo que permita modificar más aspectos del sistema para hacerlo más interactivo. 

**5. ¿Qué fue lo que más te llamó la atención del trabajo de tu compañero?**

Lo más interesante de la obra de sebas es ver cómo pudo mezclar varios conceptos de fuerzas en una misma obra. Me parece que fue acertado en su propuesta y aunque sé que intentó referenciar a alexander calder no lo pude apreciar tanto.

**6. ¿Qué aprendiste de su enfoque para modelar fuerzas?**

Aprendí ese sistema híbrido que se puede plantear para tener multiples fuerzas modeladas funcionando al mismo tiempo, vi que se pueden generar obras muy interesantes.

**7. ¿Qué técnica o idea de su implementación te gustaría aplicar en tus futuros proyectos?**

Me gustaría aplicar esa experimentación que sebas propone en su trabajo, el modela las fuerzas hasta donde conviene y seguidamente le da autonomía a su obra. 

### ACTIVIDAD 13

**1. ¿Qué actividad o concepto de esta unidad te resultó más “revelador” para entender las fuerzas en el arte generativo?**

Creo que el recurso que más me ayudó a entender el modelado de fuerzas fue el texto guía, ya que tiene muy bien explicado cada ejemplo y cada fórmula. También las líneas de código aplicando las fórmulas físicas ayudan a la comprensión del tema. 

**2. ¿Hubo alguna actividad que te pareció redundante o menos efectiva para comprender el modelado de fuerzas?**

Profe sinceramente considero que en esta unidad cada actividad cumplía con un objetivo muy específico y no sobra ninguna actividad por hacer. De la actividad 1-8 es un proceso de aprendizaje que nos preparó para hacer las actividades prácticas, donde terminé de interiorizar el concepto del modelado de fuerzas. 

**3. ¿El paso de manipular aceleración directamente (unidad 2) a modelar fuerzas (unidad 3) te pareció una progresión natural y efectiva? ¿Por qué?**

Profe me pareció que aumentamos el nivel de diseño generativo, porque antes no podía entender en qué se podía relacionar la matemática con el diseño y me ha sorprendido los patrones que podemos llegar  lograr con diferentes ecuaciones y formulas. Entonces, sin duda ver especificamente la física en el diseño hace que nos abramos a más caminos de diseño, que es precisamente lo que más me gusta del diseño generativo. 

**4. ¿Cómo te ha ayudado esta unidad a ver la conexión entre conceptos físicos y expresión artística? ¿Te sientes más cómodo “jugando” con las leyes de la física en tus creaciones?**

Considero ahora que la mátematica en general y la expresión artística por medio de obras generativas son muy atractivas tanto para quienes las hacen como para quienes solo las ven e interactuan con ellas. Al mismo tiempo pienso que si antes me gustaban las matemáticas ahora las considero factores elementales para lograr cosas más innovadoras o efectos más únicos. Esta unidad me hizo apreciar las diferentes manersa que podemos usar esas lógicas matemáticas en nuestras obras. 
