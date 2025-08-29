# Algoritmos de Ascenso de Colinas y Temple Simulado — N‑Reinas y TSP

Este repositorio contiene implementaciones educativas de **Ascenso de Colinas** (*Hill Climbing*) y **Temple Simulado** (*Simulated Annealing*) aplicadas a dos problemas clásicos: **N‑Reinas** y el **Problema del Viajero (TSP)**. Incluye funciones de **visualización con Matplotlib** y ejemplos listos para ejecutar.

> **Objetivo:** servir como base didáctica para entender cómo modelar estados, definir vecindarios, evaluar costos y comparar un buscador **greedy** (colinas) con una metaheurística **estocástica** (temple).

---

## Contenidos

1. **N‑Reinas – Ascenso de Colinas**: búsqueda local con mejora inmediata (*first‑improvement*), mostrando el tablero en cada paso.
2. **TSP – Ascenso de Colinas**: vecinos por intercambio de dos ciudades; ejecuta 1000 intentos de mejora.
3. **N‑Reinas – Temple Simulado**: aceptación de peores movimientos con probabilidad `exp(Δ/T)`, enfriamiento geométrico.
4. **TSP – Temple Simulado**: ciudades aleatorias en un plano; temple con cronograma geométrico y trazado de la ruta.

---

## Problemas y modelado

### N‑Reinas

* **Estado**: vector de longitud *n* donde `estado[c] = fila` de la reina en la columna `c`.
* **Costo (conflictos)**: parejas de reinas que se atacan (misma fila o diagonal).
* **Vecindario**:

  * *Colinas*: mover una reina a otra fila en su misma columna.
  * *Temple*: idéntico, pero con aceptación probabilística `exp(Δ/T)`.
* **Visualización**: tablero de ajedrez con la pieza ♛ en la posición de cada reina.

### TSP (Traveling Salesperson Problem)

* **Estado**: permutación de índices de ciudades.
* **Costo**: longitud total de la ruta cerrada (ciclo).
* **Vecindario**:

  * *Colinas*: intercambio de dos ciudades (operador 2‑swap simple).
  * *Temple*: mismo operador de vecino con aceptación probabilística.
* **Visualización**: dispersión de ciudades y polilínea de la ruta (temple). En la versión de colinas se ilustra la secuencia de índices.

---

## Algoritmos

### Ascenso de Colinas (Hill Climbing)

* **Idea**: desde un estado actual, se exploran vecinos y se **acepta la primera mejora** respecto al costo. Si no hay mejora, **se detiene** (óptimo local o meseta).
* **Ventajas**: simple, rápido, sin hiperparámetros.
* **Desventajas**: se atasca en óptimos locales, mesetas o crestas; depende fuertemente del estado inicial.

### Temple Simulado (Simulated Annealing)

* **Idea**: explorar vecinos como en colinas, pero permitir **empeorar** con probabilidad `exp(Δ/T)` (Δ < 0 si el vecino es peor), donde **T** desciende según un cronograma de enfriamiento.
* **Cronograma geométrico**: `T <- α·T`, con 0 < α < 1.
* **Ventajas**: escapa de óptimos locales; balancea exploración/explotación.
* **Desventajas**: requiere ajustar hiperparámetros (`T0`, `Tf`, `α`, iteraciones por temperatura).

---

## Estructura del código

> Archivo principal: `temple_y_colina.py`

* `ocho_reinas_ascenso_colinas()`

  * Evalúa conflictos por pares y aplica **first‑improvement** hasta estancarse.
  * Dibuja el tablero en cada iteración y al final.
* `agente_viajero_ascenso_colinas(distancias)`

  * Solución inicial aleatoria; vecino por **intercambio de dos ciudades**.
  * Mantiene la mejor solución observada durante 1000 iteraciones.
* `temple_simulado(...)` (N‑Reinas)

  * Estados y vecinos como arriba; aceptación **Metropolis** y **enfriamiento geométrico**.
  * Finaliza antes si encuentra **0 conflictos**.
* `temple_simulado(ciudades, ...)` (TSP)

  * Genera ciudades aleatorias `(x, y)`; vecino por intercambio; cronograma geométrico.
  * Traza la ruta resultante sobre el plano.

> **Nota técnica:** el archivo redefine algunas funciones (p. ej., `dibujar_tablero`, `generar_estado`, `generar_vecino`) en secciones posteriores. Si vas a **importar** funciones desde otros módulos, considera **separarlas por archivo** (por ejemplo, `n_reinas_colinas.py`, `tsp_colinas.py`, `n_reinas_temple.py`, `tsp_temple.py`) para evitar colisiones de nombres.

---

## Requisitos

* Python 3.8+
* `matplotlib`
* `numpy` (solo se usa en una visualización de tablero)

---

## Uso

### 1) Ejecutar todos los ejemplos desde el script

```bash
python temple_y_colina.py
```

Esto ejecutará, en orden: N‑Reinas (colinas), TSP (colinas), N‑Reinas (temple) y TSP (temple), mostrando las gráficas correspondientes.

---

## Reproducibilidad

Los ejemplos son **aleatorios**. Para fijar resultados:

```python
import random, numpy as np
random.seed(42)
np.random.seed(42)
```

---

## Complejidad (nociones)

* **Evaluación de N‑Reinas**: O(n^2) en la implementación básica (pares de columnas). Puede reducirse a O(n) con conteos por diagonales.
* **Evaluación de TSP**: costo de recomputar la ruta es O(n); conviene actualizar incrementalmente para vecinos locales.
* **Colinas** vs **Temple**: el temple añade factor por “temperaturas × iteraciones por temperatura”.

---

## Ejemplos de salida esperada

* **N‑Reinas (colinas)**: un vector como `[0, 4, 7, 5, 2, 6, 1, 3]` con algunos conflictos residuales (p. ej., `2 conflictos`). El algoritmo puede detenerse en un óptimo local sin resolver totalmente el problema.
  <img width="460" height="658" alt="image" src="https://github.com/user-attachments/assets/12788f96-72c1-40b8-ba77-c36482aae998" />

* **TSP (colinas)**: una permutación de ciudades (p. ej., `[3, 7, 1, 0, 2, 6, 5, 4]`) y su longitud total, que puede ser subóptima por estancarse en un mínimo local.
  <img width="626" height="536" alt="image" src="https://github.com/user-attachments/assets/7cb3a69d-efbe-46a2-93b5-d16c3622ade5" />
  
* **N‑Reinas (temple)**: un vector como `[0, 4, 7, 5, 2, 6, 1, 3]` con `0 conflictos`.
  <img width="362" height="392" alt="image" src="https://github.com/user-attachments/assets/6516c9bf-3cc9-43d3-80f9-52f15cacefe3" />

* **TSP (temple)**: una permutación de ciudades (p. ej., `[3, 7, 1, 0, 2, 6, 5, 4]`) y su longitud total.
  <img width="482" height="515" alt="image" src="https://github.com/user-attachments/assets/26fca973-a02c-4a9c-9e3f-c9541c6e035d" />

> Los valores exactos varían por la semilla aleatoria y los parámetros.
