# ECG5000 Dataset (ECG)

Este directorio contiene una versión procesada del PTB Diagnostic ECG Database, distribuida comúnmente como **ECG5000** a través del UCR Time Series Archive. Las señales electrocardiográficas originales han sido submuestreadas y segmentadas a 140 puntos temporales por latido, facilitando su uso en tareas de clasificación de series temporales y detección de anomalías.

[📥 Descargar `ECG5000_train.csv` (ZIP)](https://raw.githubusercontent.com/AxelSkrauba/applied-ai-engineering/main/datasets/ecg/ECG5000_train.zip)
[📥 Descargar `ECG5000_test.csv` (ZIP)](https://raw.githubusercontent.com/AxelSkrauba/applied-ai-engineering/main/datasets/ecg/ECG5000_test.zip)

## Origen y Cita Recomendada

Los datos originales fueron recopilados por el **Physikalisch-Technische Bundesanstalt (PTB)**, el Instituto Nacional de Metrología de Alemania, en colaboración con el Departamento de Cardiología de la Clínica Universitaria Benjamin Franklin en Berlín. Las señales fueron digitalizadas a 1000 muestras por segundo con 16 canales (12 derivaciones estándar + 3 derivaciones de Frank).

**Cita del dataset original (PTBDB):**
> *Bousseljot, R.; Kreiseler, D.; Schnabel, A. "Nutzung der EKG-Signaldatenbank CARDIODAT der PTB über das Internet." Biomedizinische Technik, Band 40, Ergänzungsband 1 (1995), S. 317.*

**Cita de PhysioNet:**
> *Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). "PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals." Circulation [Online], 101(23), pp. e215–e220. DOI: 10.1161/01.CIR.101.23.e215*

**Fuente del formato procesado (ECG5000):**
> *Dau, H. A.; Bagnall, A.; Kamgar, K.; Yeh, C. C. M.; Zhu, Y.; Gharghabi, S.; Ratanamahatana, C. A.; Keogh, E. "The UCR Time Series Archive." IEEE/CAA Journal of Automatica Sinica, 6(6), pp. 1293–1305, 2019.*
> Disponible en: https://www.cs.ucr.edu/~eamonn/time_series_data_2018/

## Descripción de los Datos

El conjunto contiene **señales de electrocardiograma (ECG)** segmentadas por latido. Cada muestra representa un único latido cardíaco submuestreado a **140 puntos temporales**, capturando la morfología de la onda desde el complejo P hasta el complejo T. Las señales provienen de sujetos sanos y pacientes con diversas patologías cardíacas.

### Archivos disponibles

| Archivo | Muestras | Descripción |
|---|---|---|
| `ECG5000_train.csv` | 7.600 | Conjunto de entrenamiento con latidos etiquetados. |
| `ECG5000_test.csv` | 1.900 | Conjunto de prueba para evaluación de modelos. |

### Distribución de clases (Entrenamiento)

| Categoría | Diagnóstico | Muestras |
|---|---|---|
| `1` | **Normal** | 4.427 |
| `2` | Anomalía: contracción ventricular prematura | 2.683 |
| `4` | Anomalía: contracción supra-ventricular prematura | 306 |
| `3` | Anomalía: latido ectópico | 149 |
| `5` | Anomalía: patología desconocida | 35 |


## Diccionario de Datos

Cada archivo contiene **141 columnas**:

*   **`Categoria` (Variable Objetivo / Target)**: (Categórica/Entera) Clase del latido cardíaco:
    *   `1`: Latido **normal**.
    *   `2`, `3`, `4`, `5`: Diferentes tipos de **anomalías morfológicas** del latido.
*   **`1` – `140`**: (Numéricas, Continuas) Amplitud de la señal ECG en cada uno de los 140 puntos temporales del latido. Los valores representan la tensión eléctrica relativa (normalizados en la extracción del UCR Archive).

## Consideraciones para Ingeniería y Casos de Uso

- **Clasificación Multiclase con Desbalance Severo:** La clase `5` representa apenas el `0,46%` del conjunto de entrenamiento. La accuracy no es una métrica informativa; se recomienda evaluar con **F1-score macro/ponderado, Precision-Recall por clase y matrices de confusión normalizadas**.
- **Detección de Anomalías (Binaria):** Alternativamente, el dataset puede replantearse como un problema de detección de anomalías binario (`1` = Normal, `2–5` = Anomalía), lo cual refleja mejor el escenario clínico real donde el objetivo es detectar cualquier latido patológico frente a la clase dominante normal.
- **Series Temporales 1D:** Cada fila es una serie temporal univariada de longitud fija (140). Es el formato nativo para redes convolucionales 1D (`Conv1D`), redes recurrentes (LSTM/GRU) o Transformers para series temporales. No requiere *feature engineering* tradicional — la morfología temporal completa es la entrada.
- **Criterio de Ingeniería en Salud:** En un entorno clínico real, un falso negativo (no detectar una anomalía) puede tener consecuencias graves. El umbral de decisión del modelo debe calibrarse priorizando la sensibilidad (recall) de las clases anómalas, aunque a costa de más falsos positivos.
- **Contexto Temporal Ausente:** Cada muestra es un latido aislado. El dataset no preserva la secuencia temporal entre latidos consecutivos de un mismo paciente. Si se requiere análisis de arritmias basado en la variabilidad de la frecuencia cardíaca (HRV), este dataset no es apropiado y se debe recurrir al PTBDB original con sus señales continuas.
