# Unidad 3


## 🛠 Fase: Apply

Para mi obra generativa de esta unidad quise modelar la ley de Hooke: 

<img width="127" height="45" alt="image" src="https://github.com/user-attachments/assets/92373461-9576-4df6-8112-b6709bf0ccbc" />

Y la integre con el modelo de las N particulas, para empezar creé un grid/ malla: 

```js
// Cálculo de dimensiones
cols = floor(width / spacing) + 1;  // Columnas
rows = floor(height / spacing) + 2; // Filas

// Creación de N = cols × rows partículas
for (let j = 0; j < rows; j++) {
    for (let i = 0; i < cols; i++) {
        particles.push(new Particle(i * spacing, j * spacing, i, j));
    }
}
````
Y para cumplir con el problema de las N particulas las conecté a todas entre sí con un sistema de masa-resortes (con la ley de hooke)

```js
// Conexiones horizontales (→)
if (i != 0) {
    let a = particles[i + j * cols];        // Partícula actual
    let b = particles[i - 1 + j * cols];    // Partícula izquierda
    springs.push(new Spring(k, spacing, a, b));
}

// Conexiones verticales (↓)
if (j != 0) {
    let c = particles[i + j * cols];        // Partícula actual
    let d = particles[i + (j - 1) * cols];  // Partícula superior
    springs.push(new Spring(k, spacing, c, d));
}
```
El sistema no solo cuneta con la la fuerza elastica de la ley de hooke, sino también con fuerza de Atracción/Repulsión entre las particulas. 


