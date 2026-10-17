# Detección de ciberataques con redes neuronales profundas: CIC-IDS2017

Avance del Proyecto ML · Tema **PR-36** · Maestría MMIA/MSDS, USFQ
Autor: Ronnie Almeida

## Descripción
Este proyecto hace una clasificación binaria de flujos de red (**benigno vs. ataque**) sobre el dataset CIC-IDS2017. Compara una **red neuronal profunda (MLP)** con un **Random Forest**, y ambos modelos usan el mismo pipeline de scikit-learn:

`inf→NaN → imputación (mediana) → VarianceThreshold → StandardScaler → SelectKBest (ANOVA-F, filter) → clasificador`

## Contenido del notebook
| Sección | Ítem de la rúbrica |
|---|---|
| 1 | Introducción y motivación |
| 2 | EDA: clases, NaN/inf, constantes, duplicados, distribución, correlación |
| 3 | Feature Extraction (no aplica, el dataset es tabular) |
| 4 | Pipelines y Feature Selection tipo filter |
| 5 | Hiperparámetros: `GridSearchCV` + `RepeatedStratifiedKFold` (5×2), gráficas y curvas de aprendizaje (Optuna opcional) |
| 6 | Comparación estadística MLP vs. RF: 10×10 CV, Wilcoxon y t-test corregido de Nadeau-Bengio, evaluación en test |
| 7 | t-SNE del espacio de características y de la última capa oculta del MLP |
| 8 | Conclusiones |
| 9 | Referencias en formato IEEE |

## Cómo ejecutarlo (Google Colab)
1. Abrir `PR36_deteccion_ciberataques_CICIDS2017.ipynb` en Colab.
2. `Runtime → Run all`. El dataset se descarga automáticamente con `kagglehub` (`chethuhn/network-intrusion-dataset`).
   - Si la descarga falla, hay que bajar los CSV *MachineLearningCVE* de CIC-IDS2017 y subirlos a una carpeta `data/`.
3. Tiempo aproximado en CPU: unas 2 horas (la comparación 10×10 tarda unos 36 min y las búsquedas en grilla unos 40 min). Para acelerar, se puede cambiar `CMP_SPLITS, CMP_REPEATS = 5, 2` en la celda de configuración.

## Requisitos
`numpy`, `pandas`, `scikit-learn>=1.3`, `scipy`, `matplotlib`, `seaborn`, `kagglehub` y, opcionalmente, `optuna`. Todos vienen preinstalados en Colab salvo `optuna`.

## Dataset
I. Sharafaldin, A. H. Lashkari y A. A. Ghorbani, "Toward generating a new intrusion detection dataset and intrusion traffic characterization," *ICISSP*, 2018. https://www.unb.ca/cic/datasets/ids-2017.html

## Resultados principales (CIC-IDS2017, muestra de 38 129 flujos)
| Modelo | F1 (10×10 CV) | F1 prueba | ROC-AUC prueba | Falsos positivos (prueba) |
|---|---|---|---|---|
| MLP (128-64-32, k=40) | 0.9684 ± 0.0047 | 0.9681 | 0.9950 | 162 |
| Random Forest (200 árboles, depth 20, k=40) | **0.9954 ± 0.0011** | **0.9952** | **0.9993** | **13** |

Wilcoxon: W = 0, p = 3.9e-18. El Random Forest es significativamente mejor (α = 0.05).
