# Evidencias de la unidad 6

### ACTIVIDAD 1

**Obra 1: Fragments of thought**

<img width="743" height="588" alt="image" src="https://github.com/user-attachments/assets/e14d455d-2ff4-44d4-9336-2b86d56480db" />

Esta obra de Tyler Hobbs me llamó mucho la atención porque luego de ver su explicación de los campos de flujo primero mostrando la matriz de fuerzas, luego mostró en blancos y negros cómo se ve y finalmente cuando agrega el color quedé muy fascinada con el resultado. También me gustó mucho que se puede experimentar de muchas manera con el movimiento del campo de flujo cómo jugar con el mismo o hacer que genere ek movimiento a algo. Me gustó también la manera en que se veía un ejemplo para mostrar el GRID (imagen a continuación):

<img width="513" height="541" alt="image" src="https://github.com/user-attachments/assets/8f77e2a3-e5f6-43ec-b81b-416e2041f19b" />

Me gusta mucho cómo el mismo campo de flujo forman ciertas figuras que se perciben muy bien visualmente, el efecto visual que causa es espectacular. 

**Obra 2: Mirror Removal**

<img width="1874" height="505" alt="image" src="https://github.com/user-attachments/assets/1a424e27-e2fc-4e20-ba28-3a646f67f4aa" />

Esta definitivamente es mi favorita, la manera en que cambia según los colores, las formas generadas y ese reflejo tipo espejo me sorprendió bastante. 


De ambas obras me inspira mucha vida, creo que eso lo logra por el uso de los colores. Además me gusta las formas que logra con los campos de flujo, más que todo en la segunda obra, definitivamente son muy disruptivos. 

### ACTIVIDAD 2


**¿Qué es una fuerza de dirección (steering force)?**

Después de investigar, una fuerza de dirección es la aceleración que aplicas al "agente" para corregir su velocidad actual hacia una velocidad deseada. Este método es muy útil porque evita giros bruscos y por el contrario ocurre suavemente. 


**¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?**

La diferencia más grande que veo es que las otras fuerzas que hemos modelado provienen de leyes del entorno y del estado; no buscan llegar a una meta (cómo si lo hace steering force), solo actúan según ecuaciones físicas. 

**¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?**

Después de comprender cómo funciona el programa de Craig Reynolds, pude llegar a que él mostró que el “vuelo en bandada” se puede simular si cada agente aplica tres steering behaviors: **separación, alineación y cohesión** cada uno produciendo una fuerza de dirección. Esas fuerzas se ponderan, se suman y se limitan y el resultado se aplica como aceleración del agente; de ahí sale el movimiento grupal creíble y orgánico. 

### ACTIVIDAD 3

El campo de flujo se guarda dentro de un arreglo 2D. El tamaño de cada cuadrito lo define la resolución r, por eso el número de columnas es width / r y el de filas height / r. En cada celda se guarda un p5.Vector que marca la dirección del “viento” en ese punto.

Para crear estas direcciones, en init() se recorre toda la rejilla y, para cada celda (i, j), se toma un valor de ruido Perlin(que cambia suavemente), se transforma ese número de [0, 1] a un ángulo entre 0 y 2π, y con ese ángulo se construye el vector (p5.Vector.fromAngle).Además, noiseSeed(random(10000)) “rebaraja” el ruido perlin cada vez que llamas a init(), generando un campo nuevo. 

La manera en que un agente utiliza el campo de flujo para calcular su fuerza de direeción es siguiendo los siguientes pasos:

1. El agente mira en qué cuadrito del campo está.

2. Toma la flechita (vector) de ese cuadrito y la convierte en su dirección/velocidad deseada (ajustada a su velocidad máxima).

3. Calcula la fuerza de dirección como: velocidad deseada menos velocidad actual (esto le dice cuánto girar y acelerar).

4. Limita esa fuerza a su máxima fuerza permitida.

5. Aplica esa fuerza: con eso cambia su aceleración y va corrigiendo la trayectoria para seguir el flujo.

Los parámetros clave que percibí: 

- Cantidad de agentes
- La escala del ruido perlin
- Posición inicial de los agentes (por ahora es aleatoria pero podría ser modificado para que funcione de otra manera)
- Resolución
- MaxSpeed
- MaxForce

Decidí modificar la resoulción, el maxspeed, el maxforce y la cantidad de particulas que se generan. 

Primero lo hice sin modificar el maxforce, solo los demás parámetros mencionados y obtuve unos vehicles mucho más grandes por colocar la resolución = 9, también aumenté la cantidad de agentes de 120 a 320 y el maxSpeed lo cambié de ms a 10. Y se ve de la siguiente manera 

<img width="814" height="306" alt="image" src="https://github.com/user-attachments/assets/add187e6-5a80-4974-a4c2-f25294dee382" />

