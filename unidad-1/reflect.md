# Unidad 1

## 🤔 Fase: Reflect

### Actividad 9: Parte 1

**1. Describe la diferencia fundamental entre la aleatoriedad generada por random() y la apariencia de aleatoriedad del Ruido Perlin (noise()). ¿En qué tipo de situación usarías cada una**

El random genera valores aleatorios y esto hace que cuando se usa para generar visuales ocurran cambios repentinos y muy bruscos a diferencia del Perlin noise que genera valores muy cercamos entre si y hace que visualmente se generen cambios muy suaves y naturales. El random lo usamos cuando buscamos una aletoriedad sin control alguno (generar truenos o efectos así de impredecibles), en cambio, el noise lo usamos para generar efectos como humo o cosas que deben ser muy orgánicas.

**2. Explica con tus palabras qué es una distribución de probabilidad. ¿Qué diferencia visual produce una caminata aleatoria con una distribución uniforme versus una con una distribución normal?**

Una distribución de probabilidad es las diferentes formas en que se pueden generar datos de manera aleatoria. Visualmente la caminata aleatoria con DU se ve como los walkers salen aleatoriamente dentro de un rango de 0-4 y todos los números tienen la misma probabilidad de salir. Por su lado, en una distribución normal los numeros que se generan tienden al valor de la media y a la desviación estandar que se establezcan, visualmente se ve como se acumulan en el valor de la media y unos que otros están más alejados (por la DE).

**3. ¿Cuál es el papel de la aleatoriedad en el arte generativo? Menciona al menos dos funciones distintas que cumple**

La aletoriedad permite que la obra que se está ejecutando sea irrepetible e impredecible. Asegura que cada vez que se ejecute la experiencia sea completamente diferente a la anterior :) 

**4. Piensa en tu obra final (Actividad 08). Describe uno de los conceptos de aleatoriedad que usaste y explica por qué fue una elección adecuada para lograr el efecto que buscabas.**

Uno de los efectos que más me gustó implementar fue que el grosor de la línea variara contanstemente con el ruido de perlin, queria tener efectos desaliñados y nada perfectos. Aplicar esta técnica me ayudó a lograrlo.

**5. ¿Qué es un “paseo” o “caminata” (walk) en el contexto de la simulación? ¿Qué característica particular tiene una caminata de tipo “Lévy flight”?**

Una caminata se refiere a valores aleatorios que se generan dentro de un rango y eso hace que aparezcan los walkers simulando un caminatica de puntitos. La caminata tipo Levy Flight se refiere a que casi siempre (el 99% de las veces) los valores están muy cerca entre si y de la nada (el 1% que resta) ocurre un salto inesperado que modifica la ruta de la caminata. 

### Parte 2

**1. ¿Cuál fue el concepto más abstracto o difícil de “visualizar” para ti en esta unidad? ¿Qué hiciste para finalmente comprenderlo?**

Creo que lo que más dificultad me dió fue recordar la diferencia entre la distribución uniforme y la no uniforme (Me costó interiorizar el concepto). El ejercicio del randomwalker me ayudó a entender muy bien otra ve el concepto, también le dije a chat gpt que me explicara la diferencia entre la una y la otra y eso me acalaró todo. También un ejercicio que me ayudó mucho fue el que debiamos modificar el random walker para que funcionara con distribución no uniforme en ves de DU. 

**2. Describe un momento durante el desarrollo de tu obra final (Actividad 08) en el que un “error” o un resultado inesperado te llevó a una idea creativa interesante.**

Aunque al final no lo dejé implementado, cuando intenté generar el color del fondo con ayuda de una distribución normal y que el color variara según el valor de la media (que se modificara por un slider) y al pedirle a la IA que me ayudara a implementarlo, me creó una cuadricula muy interesante que cambiaba sus colores según la media. Se veía muy genial pero no me funcionaba para la obra generativa ya que parecia más un jueguito. 

**3. Al combinar diferentes técnicas de aleatoriedad, ¿Cuál fue el mayor desafío? ¿La interacción entre los sistemas, el control de los parámetros, el rendimiento?**

Creo que esto sin duda fue de las cosas que más se me dificultó implementar pues no fue fácil integrarlos entre si, tan dificil fue que no pude añadir una tercera técnica porque no me funcionó lo que planeaba y me rendí de hablar con la IA (Fallé en la elaboración de pronts claros). 

**4. Si tuvieras que empezar la Actividad 08 de nuevo, ¿Qué harías de manera diferente basándote en lo que sabes ahora?**

Creo que me hubiese gustado ver referentes y consumir más contenido generativo para ser más original y que saliera una idea más cool. También sentí que no fui más allá en la creación del concepto, por eso mi mayor compromiso será ver muchos referentes para poder nutrir mi cerebro de ideas nuevas. 

### Actividad 10

Para esta actividad, analicé la obra de mi compañero Jerónimo Montoya. Al principio me costó un poco entender cuál era su intención, pero tras revisar el código con más detenimiento y releer el texto en el que explica el concepto, logré comprender mucho mejor lo que buscaba transmitir.
En una próxima ocasión, le sugeriría a Jerónimo que explore formas de incorporar una interactividad más llamativa y entretenida. También recomendaría incluir más de los conceptos trabajados durante la unidad, lo cual podría enriquecer la propuesta y hacerla aún más atractiva como obra generativa.

### Actividad 11

**1. ¿Qué actividad, ejemplo o explicación de esta unidad te resultó más reveladora o útil para tu aprendizaje? ¿Qué deberíamos mantener sí o sí?**

Me gustó  mucho la actividad de modificar el random walker para que funcionara con la distribución no uniforme, eso me ayudó a comprender la diferencia entre los tipos de distribuciones uniforme y no uniforme, además que pudimos cacharrear el código un rato. Creo que esas actividades de modificar y adaptar código pueden ser muy utiles para comprender este tipo de conceptos. 

**2. ¿Hubo alguna actividad o concepto que te pareció redundante, confuso o menos útil? ¿Hay algo que eliminarías o cambiarías por completo?**

NOO, todas las actividades me parecieron muy oportunas para explicar los conceptos por aprender en esta unidad. Considero que trabajar con el texto guía paralelo a las actividades que el profe propone ayuda a que si surgen dudas durante el tiempo de trabajo autónomo sea muy fácil solucionar dudas que probablemente van a surgir (eso fue lo que yo hice). 

**3. ¿Qué te faltó en esta unidad? ¿Quizás más ejemplos de artistas, más desafíos técnicos, más teoría? ¿Qué idea tienes para hacerla mejor?**

Profe, me habría gustado ver más referentes y ejemplos que mostraran la aplicación de los conceptos aprendidos. Los desafíos técnicos propuestos me parecieron muy acertados, pero creo que incorporar más podría facilitar una mejor interiorización de los contenidos.
Por último, considero que la teoría fue presentada de manera clara y concisa, lo cual ayudó a comprender bien los temas abordados. 

**4. ¿Cómo sentiste el equilibrio entre analizar los ejemplos del texto guía y ponerte a programar tus propios sketches? Explica tu respuesta.**

Profe considero que tuvo el equilibrio perfecto, como te conté en la pregunta #2 trabajar con el texto guía hizo que al momento de hacer nuestros propios sketches las dudas que iban saliendo se fueran solucionando de manera sencilla. Metodología 10/10

**5. ¿Hay algo más que quieras compartir sobre tu experiencia en esta unidad?**

De esta unidad me gustó la metodología del profe que de cierta manera nos hizo trabajar si o si en clase y en el espacio de trabajo autónomo, eso me está ayudando a mejorar mi hábito de no procrastinar. También, me gustó usar un texto guía para orientar mi aprendizaje. 

   
