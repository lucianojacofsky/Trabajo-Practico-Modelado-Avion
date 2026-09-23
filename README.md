# TP1 – Aplicaciones Computacionales en Negocios

Simulación Monte Carlo del embarque de un avión para comparar políticas de boarding y analizar su impacto en tiempos y costos.

## Descripción

Modelamos un avión de **25 filas con 4 asientos por fila (2+2)**, o sea 100 pasajeros. La simulación avanza segundo a segundo: cada pasajero entra al pasillo, avanza fila por fila, guarda su carry-on (si tiene), deja pasar a quien haga falta y se sienta.

Políticas comparadas:

| Política | Cómo funciona |
|---|---|
| **Back-to-Front** | Suben por grupos de filas, de atrás hacia adelante (orden aleatorio dentro de cada grupo) |
| **Random** | Orden completamente aleatorio |
| **Wilma** | Primero ventanillas, después pasillos (aleatorio dentro de cada grupo) |
| **Steffen** | Ventanillas antes que pasillos, filas alternadas (pares y después impares), de atrás hacia adelante y un lado a la vez |

## Supuestos

| Parámetro | Valor | Fuente |
|---|---|---|
| Probabilidad de tener carry-on | p = 0.75 | Yazdani et al. (2023) |
| Avanzar una fila sin carry-on | 3 s | Enunciado |
| Avanzar una fila con carry-on | 6 s | Enunciado |
| Sentarse | 2.4 s | Media de Triangular(1.8, 2.4, 3.0) |
| Guardar el carry-on | 14 s | Media de Weibull(α=16, β=1.7) |
| Dejar pasar a otro pasajero | 11 s | Media de Triangular(6, 9, 20) |
| Espacio libre antes de avanzar | 3 s | Enunciado |
| Costo operativo por minuto de demora | USD 98.41 | Airlines for America (2025) |
| Tarifa doméstica promedio | USD 387 | Bureau of Transportation Statistics (2025) |

## Resultados principales

**3000 simulaciones por política (p = 0.75):**

| Política | Media (min) | Desvío (min) | Error estándar (min) |
|---|---|---|---|
| Back-to-Front | 33.54 | 1.18 | 0.022 |
| Random | 23.95 | 0.98 | 0.018 |
| Wilma | 22.49 | 0.82 | 0.015 |
| **Steffen** | **17.50** | **0.23** | **0.004** |

Steffen es la política más rápida y la más estable, y Back-to-Front la más lenta. El orden coincide con lo que encontré en el paper de Yazdani et al. (2023).

**Sensibilidad al carry-on:** a medida que sube la probabilidad de tener carry-on, el tiempo crece en todas las políticas. Back-to-Front pasa de ~16 min (p=0) a ~39 min (p=1), mientras que Steffen va de ~10.5 a ~17.9 min.

**Dejar asientos vacíos:** con ocupaciones de 90%, 80% y 70%, el ahorro operativo por embarcar más rápido (máx. ~USD 1.200) nunca compensa el ingreso perdido por los asientos vacíos (USD 3.870 a USD 11.610). **No conviene dejar asientos libres.**

**Bonus – costo de armar la fila en Steffen:** si se suma el tiempo de llamar a cada pasajero por nombre y que camine a su lugar en la fila (distancias de 5 a 20 m), Steffen termina tardando entre 30.8 y 64.2 min, contra 24 min de Random. En la práctica, **no conviene** salvo que la fila se arme en paralelo o antes del embarque.

## Estructura del notebook

1. Fuentes y supuestos
2. Parámetros del modelo
3. Clases `Pasajero` y `Avion`
4. Funciones de orden: `Backtofront`, `orden_random`, `orden_wilma`, `orden_steffen`
5. Lógica de sentado (`sentarse`) y simulación (`simulacion`)
6. Monte Carlo: media, desvío y error estándar
7. Sensibilidad a la probabilidad de carry-on
8. Análisis de costos por ocupación
9. Bonus: trade-off de armar la fila en Steffen

## Cómo correrlo

Requisitos: Python 3 con `numpy`, `pandas` y `matplotlib`.

```bash
pip install numpy pandas matplotlib
jupyter notebook TP1ACN.ipynb
```

También se puede abrir directamente en Google Colab. Correr las celdas en orden: las 3000 simulaciones por política pueden tardar unos minutos.

## Fuentes

1. Yazdani, Bidgoli, et al. (2023). *MDPI Sustainability*. https://www.mdpi.com/2071-1050/15/23/16476
2. de Oliveira et al. (2021). *International Journal of Aerospace Engineering*. https://onlinelibrary.wiley.com/doi/10.1155/2021/8872992
3. Airlines for America (2025). *U.S. Passenger Carrier Delay Costs*. https://www.airlines.org/dataset/u-s-passenger-carrier-delay-costs/
4. Bureau of Transportation Statistics (2025). https://www.bts.gov/newsroom/2025-annual-average-domestic-air-fare-decreases-2024
