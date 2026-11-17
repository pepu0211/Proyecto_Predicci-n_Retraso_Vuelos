# ¿Llegará a tiempo? Predicción temprana de retrasos de vuelos

<p align="center">
  <img src="¿Llegará a tiempo Predicción de retrasos de vuelos - slide 1.png" alt="¿Llegará a tiempo? Predicción temprana de vuelos retrasados, cancelados o desviados" width="100%">
</p>

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

## Enlaces

| Recurso | Enlace |
|---|---|
| Dataset en Kaggle | [Flight Delay and Cancellation Dataset (2019-2023)](https://www.kaggle.com/datasets/patrickzel/flight-delay-and-cancellation-dataset-2019-2023) |
| Presentación del proyecto | [Ver presentación](https://github.com/pepu0211/Proyecto_Predicci-n_Retraso_Vuelos/blob/2672309f097d0e11dce0f2b4feadb5e705b29a94/%C2%BFLlegar%C3%A1%20a%20tiempo%20Predicci%C3%B3n%20de%20retrasos%20de%20vuelos.pdf) |
| Video del proyecto en YouTube | [Ver video](https://youtu.be/TkXHFfq9WPM) |
| Colab del Proyecto | [Ver Colab](https://drive.google.com/file/d/17gJ65X9YAWOupuYh6FglF5rNjdwM9oAd/view?usp=sharing) |

