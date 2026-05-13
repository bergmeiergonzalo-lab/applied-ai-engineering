# Comentarios Play Store Argentinas (CPS)

Este directorio contiene comentarios de usuarios de aplicaciones argentinas extraídos de la Google Play Store, junto con sus calificaciones por estrellas. Los datos fueron recopilados mediante scraping directo de la plataforma y posteriormente anonimizados para eliminar toda información identificable (nombres de usuario, IDs de review, nombres de aplicación).

[📥 Descargar `reviews_limpias_2022.csv` (ZIP)](https://raw.githubusercontent.com/AxelSkrauba/applied-ai-engineering/main/datasets/cps/reviews_limpias_2022.zip)
[📥 Descargar `reviews_2026.csv` (ZIP)](https://raw.githubusercontent.com/AxelSkrauba/applied-ai-engineering/main/datasets/cps/reviews_2026.zip)

## Origen y Cita Recomendada

Los comentarios fueron recopilados de la Google Play Store de aplicaciones con presencia en Argentina. La recolección original fue realizada en 2022; en 2026 se actualizó el proceso de extracción, abarcando una cantidad mayor de aplicaciones y recopilando un número significativamente mayor de comentarios. Los datos publicados corresponden a reseñas públicas de usuarios, anonimizadas para eliminar identificadores personales y de aplicación.

**Cita del dataset (curador):**
> *A. A. Skrauba. (2022, 2026). "Scraping de comentarios en PlayStore — Apps Argentinas." [Dataset].*

**Origen de los datos:**
> *Reseñas públicas de usuarios de aplicaciones Android en Google Play Store, recopiladas entre 2022 y 2026.*


## Descripción de los Datos

Cada muestra representa un comentario real de un usuario junto con su calificación en estrellas (1–5). Los comentarios están en español (con posibles errores ortográficos, abreviaturas y lenguaje informal característico de la región argentina). Es posible que algunos comentarios contengan información sensible respecto a la aplicación o el usuario involucrado.

### Archivos disponibles

| Archivo | Muestras | Descripción |
|---|---|---|
| `reviews_limpias_2022.csv` | ~4.500 | Dataset original (2022). Comentarios preprocesados con columna de texto "limpio" mediante un preprocesamiento simple. |
| `reviews_2026.csv` | ~77.800 | Dataset actualizado y anonimizado. Comentarios con score 1–5 y categoría temática según app de origen. |


### Estructura de `reviews_limpias_2022.csv`

*   **`reviews`**: (Texto) Comentario original tal como aparece en Play Store.
*   **`stars`**: (Texto) Descripción textual de la calificación (ej. "Calificación: 5 de cinco estrellas").
*   **`rating`**: (Numérica/Entera) Calificación numérica de 1 a 5 estrellas.
*   **`sentiment`**: (Categórica) Etiqueta derivada del rating: `negativo` (1–2), `neutral` (3), `positivo` (4–5).
*   **`class`**: (Numérica/Entera) Codificación numérica del sentimiento: `0` = negativo, `1` = neutral, `2` = positivo.
*   **`clean_reviews`**: (Texto) Versión preprocesada del comentario: minúsculas, sin emojis, espacios normalizados.

### Estructura de `reviews_2026.csv`

*   **`content`**: (Texto) Comentario original tal como aparece en Play Store.
*   **`score`**: (Numérica/Entera) Calificación de 1 a 5 estrellas.
*   **`sentiment`**: (Categórica) Etiqueta derivada: `negativo`, `neutral`, `positivo`.
*   **`category`**: (Categórica) Categoría temática de la app de origen (`bancaria`, `gubernamental`, `telefonia`, `medios`, etc.).
*   **`content_length`**: (Numérica/Entera) Longitud del comentario en caracteres.


## Consideraciones para Ingeniería y Casos de Uso

- **Clasificación de Sentimiento Multiclase:** La clase `neutral` (3 estrellas) suele ser la más escasa. La *accuracy* no es informativa; se recomienda evaluar con **F1-score macro/ponderado, Precision-Recall por clase y matrices de confusión normalizadas**.
- **Replantee como Binario:** Dado el desbalance extremo de la clase neutral, el problema puede replantearse como clasificación binaria (negativo vs. no-negativo) o como detección de anomalías en el espacio de embeddings.
- **Lenguaje Informal y Regional:** Los comentarios contienen errores ortográficos, abreviaturas, modismos argentinos (*"podes"*, *"che"*, *"bolilla"*) y mezclas de español con inglés. Un Embedding entrenado desde cero sobre este corpus capturará estas particularidades, pero será menos robusto que uno preentrenado en texto formal. La decisión entre embedding propio vs. preentrenado (Word2Vec, GloVe, FastText) depende del volumen de datos disponible.
- **Embeddings y Secuencias:** El texto es una secuencia donde el orden importa. Los enfoques que ignoran el orden (Bag of Words + capa densa) servirán de *baseline*, pero los que lo capturan (Embedding + LSTM/GRU, 1D-CNN, Transformers) tenderán a superarlos cuando el contexto de las palabras sea relevante para el sentimiento.
- **Transfer Learning:** Estos comentarios pueden procesarse con modelos preentrenados de Hugging Face (ej. BETO, RoBERTa en español) para obtener embeddings densos y alimentarlos a modelos clásicos de ML o realizar *fine-tuning* directo.


## Nota sobre Privacidad

Los comentarios de Google Play Store son públicos por diseño de la plataforma. Los datasets publicados en este directorio han sido anonimizados: no contienen nombres de usuario, IDs de review, nombres de aplicaciones, fechas ni ningún otro dato identificable (salvo las expresiones propias de cada usuario). El objetivo es el análisis lingüístico y de sentimiento sobre un contexto real, no la identificación de personas.
