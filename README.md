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

Instalación rápida:

```bash
python -m venv .venv
source .venv/bin/activate  # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Archivo `requirements.txt` sugerido:

```txt
matplotlib
numpy
```

---

## Uso

### 1) Ejecutar todos los ejemplos desde el script

```bash
python temple_y_colina.py
```

Esto ejecutará, en orden: N‑Reinas (colinas), TSP (colinas), N‑Reinas (temple) y TSP (temple), mostrando las gráficas correspondientes.

### 2) Usar funciones específicas en tu propio código

```python
from temple_y_colina import temple_simulado as sa_nreinas
sol = sa_nreinas(n=8, temp_inicial=1000, temp_final=0.01, alfa=0.99, max_iter=1000)
print(sol)
```

> **Sugerencia:** separa las versiones de `temple_simulado` (N‑Reinas y TSP) en módulos distintos o renómbralas (`temple_nreinas`, `temple_tsp`) para claridad.

---

## Parámetros clave y *tuning*

### Temple Simulado

* `temp_inicial (T₀)`: si es muy alta, aceptará demasiados peores movimientos; si es baja, se comporta parecido a colinas.
* `temp_final (T_f)`: temperatura de corte; bajar demasiado puede alargar el tiempo con poca ganancia.
* `alfa (α)`: razón de enfriamiento geométrico (p. ej., `0.95–0.995`). Valores más cercanos a 1 implican enfriamiento lento y mayor exploración.
* `max_iter`: iteraciones por temperatura; aumentar mejora la cobertura del vecindario pero incrementa el costo computacional.

### Representaciones y vecinos

* **N‑Reinas**: mover una reina en su columna es barato y suficiente para explorar; se puede mejorar con **reinicios aleatorios** o con **min‑conflicts**.
* **TSP**: el **2‑swap** es básico. Para mayor calidad, usar **2‑opt** o **3‑opt** como operador de vecindad.

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

---

## Limitaciones y mejoras propuestas

* Separar el código en **módulos** para evitar redefiniciones y choques de nombres.
* Añadir **reinicios aleatorios** y **rastro de métricas** (costo por iteración) para análisis.
* Incorporar **2‑opt/3‑opt** en TSP y **min‑conflicts** en N‑Reinas.
* Parametrizar la **frecuencia de visualización**; en N‑Reinas (colinas) dibujar cada paso puede ser costoso.
* Devolver también el **costo** además de la solución, para facilitar comparativas.

---

## Ejemplos de salida esperada

* **N‑Reinas (temple)**: un vector como `[0, 4, 7, 5, 2, 6, 1, 3]` con `0 conflictos`.
* **TSP (temple)**: una permutación de ciudades (p. ej., `[3, 7, 1, 0, 2, 6, 5, 4]`) y su longitud total.

> Los valores exactos varían por la semilla aleatoria y los parámetros.

---

## Referencias breves

* Kirkpatrick, Gelatt & Vecchi (1983) – *Optimization by Simulated Annealing*.
* Russell & Norvig – *Artificial Intelligence: A Modern Approach*, capítulos de búsqueda local.

---

## Licencia

Elige una licencia (p. ej., MIT) y añádela como `LICENSE`. Ajusta este README si corresponde.

---

## Agradecimientos

Este proyecto se construyó con fines académicos para comparar heurísticas de búsqueda local con visualizaciones sencillas en Python.
