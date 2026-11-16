<p align="center">
  <img src="assets/banner.png" alt="¿Llegará a tiempo? Predicción temprana de vuelos retrasados, cancelados o desviados" width="100%">
</p>

# ¿Llegará a tiempo? Predicción temprana de retrasos de vuelos

Proyecto del curso **Inteligencia Artificial 1**. Un sistema de alerta temprana que estima, **antes del despegue**, el riesgo de que un vuelo doméstico de EE. UU. llegue con 15 minutos o más de retraso, se cancele o se desvíe. Con esa alerta, una aerolínea podría preparar tripulaciones de reserva, avisar a los pasajeros con conexiones o reforzar el personal en tierra.

El problema se plantea como una **clasificación binaria con clases desbalanceadas**: solo 1 de cada 4 vuelos tiene problemas. Por eso los modelos no se evalúan con accuracy, sino con **F2** (que da más peso al recall) bajo una **capacidad de revisión del 35%**: todos los modelos alertan la misma cantidad de vuelos y se compara qué tan bien eligen cuáles.

## Autor

| Nombre | Código |
|---|---|
| Andrés Felipe Gómez Pradilla | 2240076 |

## Objetivos

### Objetivo general

Desarrollar un modelo de aprendizaje automático que identifique, antes del despegue, los vuelos con mayor riesgo de llegar retrasados, ser cancelados o desviados, usando solo información disponible antes del vuelo.

### Objetivos específicos

1. Explorar el dataset para identificar los patrones de los retrasos según la hora, el día, la temporada, la aerolínea y el aeropuerto.
2. Construir atributos *ex-ante* (sin fuga de datos), como las tasas históricas de retraso por aerolínea, aeropuerto y ruta, y la congestión programada del día.
3. Comparar modelos clásicos de clasificación y redes neuronales con una métrica adecuada para clases desbalanceadas y una misma capacidad de revisión.
4. Seleccionar los atributos más relevantes según su importancia en el modelo.
5. Aplicar aprendizaje no supervisado para descubrir estructura en los datos (tipos de aeropuerto y días atípicos) y usar esos hallazgos para mejorar el modelo.
6. Evaluar el modelo final una sola vez con vuelos futuros (partición temporal) para estimar su desempeño real.

## Dataset

[**Flight Delay and Cancellation Dataset (2019-2023)**](https://www.kaggle.com/datasets/patrickzel/flight-delay-and-cancellation-dataset-2019-2023), publicado en Kaggle con datos oficiales del *Bureau of Transportation Statistics* de EE. UU.

| Característica | Valor |
|---|---|
| Vuelos | ~3 millones (muestra aleatoria, archivo `flights_sample_3m.csv`) |
| Periodo | Enero de 2019 a agosto de 2023 |
| Columnas | 32 |
| Variable objetivo | `RETRASO` = 1 si el vuelo llega con ≥ 15 min de retraso, se cancela o se desvía |
| Proporción de la clase positiva | ~24% |

Las columnas incluyen la fecha, la aerolínea, los aeropuertos de origen y destino, las horas programadas y reales, la duración y la distancia, los minutos de retraso y sus causas (aerolínea, clima, sistema aéreo, seguridad y avión que llegó tarde), y si el vuelo fue cancelado o desviado.

Solo se usan como entrada las columnas conocidas **antes del vuelo**. Las que se generan durante o después del vuelo (retraso de salida, tiempos de rodaje, causas del retraso) se usan únicamente para construir la etiqueta y para la exploración.

## Modelos utilizados

**Aprendizaje supervisado**

| Modelo | Detalle |
|---|---|
| Naive Bayes Gaussiano | Modelo base |
| Regresión Logística | Con pesos balanceados por clase |
| Árbol de Decisión | Análisis de overfitting según la profundidad |
| **Random Forest** | **Modelo final** (150 árboles) |
| SVM | Kernel RBF, entrenada con una submuestra |
| Redes neuronales densas (Keras) | Una red simple (128 neuronas) y una profunda (256-128-64) |

**Aprendizaje no supervisado**

| Técnica | Uso |
|---|---|
| K-means y método del codo | Agrupar 170 aeropuertos según cómo operan |
| Clustering aglomerativo | Dendrograma y comparación con K-means |
| DBSCAN | Detectar días atípicos (pandemia, tormentas) como ruido |
| PCA y t-SNE | Visualizar aeropuertos y vuelos en 2D, y probar la reducción de dimensión antes de clasificar |

**Resultado en test (vuelos futuros, alertando el 35% de los vuelos)**

| Modelo | F2 | Recall | Precision |
|---|---|---|---|
| Alertar al azar | 0.324 | 0.352 | 0.246 |
| Random Forest con 12 atributos seleccionados | 0.474 | 0.520 | 0.351 |
| **Random Forest + atributos del día anterior** | **0.506** | **0.553** | **0.377** |

La mejora final nace del aprendizaje no supervisado: DBSCAN mostró que los días malos vienen en racha, así que se agregó la tasa de retraso del día anterior (del país, de la aerolínea y del aeropuerto). En ese caso la alerta se emite la noche anterior al vuelo.

## Estructura del repositorio

```
├── assets/
│   └── banner.png
├── notebooks/
│   └── proyecto_retrasos_vuelos.ipynb
├── requirements.txt
└── README.md
```

El notebook descarga el dataset directamente desde Kaggle con `kagglehub`, así que no hace falta descargarlo a mano. Está pensado para ejecutarse en Google Colab.

## Enlaces

| Recurso | Enlace |
|---|---|
| Dataset en Kaggle | [Flight Delay and Cancellation Dataset (2019-2023)](https://www.kaggle.com/datasets/patrickzel/flight-delay-and-cancellation-dataset-2019-2023) |
| Presentación del proyecto | [Ver presentación](ENLACE_DE_LA_PRESENTACION) |
| Video del proyecto en YouTube | [Ver video](ENLACE_DE_YOUTUBE) |
