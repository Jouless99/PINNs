# TP2 — Influencia de las estrategias de muestreo (RAR-D vs LHS)

PINN para la reconstrucción del campo de flujo en una cavidad cuadrada (Navier-Stokes 2D
incompresible, Re=100), comparando el muestreo baseline (LHS) contra la estrategia adaptativa
RAR-D en 3 configuraciones (A, B, C).

## Contenido

* `TP2\\\_PINN\\\_muestreo.ipynb` — notebook principal, ya ejecutado (incluye código, logs, tablas y gráficos).
* `fig\\\_tp2\\\_convergencia.png` — curvas de pérdida (baseline vs A/B/C).
* `fig\\\_tp2\\\_muestreo\\\_adaptativo.png` — evolución del muestreo RAR-D ronda a ronda.
* `fig\\\_tp2\\\_error\\\_comparacion.png` — campos de error absoluto de u (baseline vs mejor config).
* `tp2\\\_resultados\\\_resumen.png` — tabla resumen (errores relativos y tiempos de entrenamiento).

## Cómo correrlo

Pensado para **Google Colab con GPU (T4)**:

1. Subir `TP2\\\_PINN\\\_muestreo.ipynb` a Colab.
2. Entorno de ejecución → Cambiar tipo de entorno de ejecución → GPU (T4).
3. Subir `velocity.mat` y `pressure.mat` (no incluidos en este repo, provistos por la cátedra) al
almacenamiento temporal de la sesión (panel de Archivos → subir).
4. Entorno de ejecución → Ejecutar todas.

Tiempo estimado: \~90-100 min con GPU T4 (4 corridas de 50.000 épocas cada una: baseline + 3
configuraciones RAR-D).

Para correrlo localmente en vez de Colab, instalar las dependencias de `requirements.txt` y
asegurarse de tener una GPU CUDA disponible (en CPU el tiempo se multiplica por \~10-15x).

## Supuestos y decisiones documentadas

Ver la celda de introducción del notebook para el detalle completo. En resumen:

1. **Condición de referencia de presión**: se agrega durante el entrenamiento (no solo post-hoc)
un término de pérdida que fija p en un punto interior fijo (0.5, 0.5) a un valor arbitrario
p\_ref=0, ponderado con λp=100, para resolver la indeterminación de *gauge*. La corrección de
nivel contra el ground-truth se sigue haciendo solo para evaluación, nunca en el entrenamiento.
2. **Discrepancia numérica del enunciado**: el enunciado indica agregar 1.000 puntos por ronda (4
rondas) partiendo de 5.000, pero también dice que la ronda final llega a 10.000 puntos
(matemáticamente da 9.000). Se optó por seguir literalmente "1.000 puntos por ronda" mientras se
confirma con la cátedra, por lo que el esquema RAR-D en este notebook **termina con 9.000
puntos de colocación** (no 10.000). Para cambiarlo, ajustar `N\\\_ADD\\\_PER\\\_ROUND` de 1000 a 1250 en
la Sección 2 del notebook.
3. Arquitectura, optimizador y ponderaciones de pérdida reutilizados del TP1, idénticos en las 4
corridas (mismos pesos iniciales de red) para que la comparación sea "justa".

