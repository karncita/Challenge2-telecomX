# Challenge2-telecomX

# Proposito del proyecto:
Presentar el analisis del abandono de clientes (church) en la empresa TelecomX, comprednder que pátrones de riesgo hay y dar estartegias que logren reducir la taza de abandono de clientes.

## Descripción de los datos 📊

La base de datos utilizada proviene de la etapa anterior del presente proyecto, donde se realizó exploración y limpieza de los datos, los cuales pueden obtenerse en el siguiente enlace:
<a href="https://github.com/ignaciomajo/proyecto_TelecomX/tree/main/src">Base de datos</a>

Se tomaron los archivos📄: 

* <a href="https://raw.githubusercontent.com/ignaciomajo/proyecto_TelecomX/refs/heads/main/src/preprocessed_TelecomX_data.json">preprocessed_TelecomX_data.json</a>
* <a href="https://raw.githubusercontent.com/ignaciomajo/proyecto_TelecomX/refs/heads/main/src/clientes_altovalor_abandonan.json">clientes_altovalor_abandonan.json</a>

## Resultados y conclusiones ✍️

### Modelos

Se evaluaron los modelos de cada familia para seleccionar un modelo campeón (**Champion Model**), el cual será implementado en un entorno simulado de producción.

|Model	             |Accuracy	|Precision	|Recall	|F1-score|
|---------------------|----------|-----------|--------|--------|
|RandomForest 1	    |0.7131	   |0.7056	   |0.7292	|0.7172  |
|LogisticRegression 4 |0.6715	   |0.6565	   |0.7167	|0.6853  |
|KNN 1	             |0.6653	   |0.6416	   |0.7458	|0.6898  |
|XGB Classifier 3	    |0.7048	   |0.7059	   |0.7000	|0.7029  |
|SVM 3       	       |0.6778	   |0.6481	   |0.7750	|0.7059  |

Estos, fueron guardados y cargados como `Best {familia algoritmo}`, y que luego fueron evaluados en los datos de prueba *(datos no vistos por los modelos durante el entrenamiento)*:

|Model	                    |Accuracy	|Precision |Recall	|F1-score |
|----------------------------|---------|----------|---------|---------|
|Best Random Forest	        |0.6566	|0.6761	  |0.6010	|0.6364   |
|Best Logistic Regression	  |0.6692	|0.6618	  |0.6919	|0.6765   |
|Best K-Nearest Neighbors	  |0.6490	|0.6335	  |0.7071	|0.6683   |
|Best XGBoost Classifier	  |0.6389	|0.6536	  |0.5909	|0.6207   |
|Best Support Vector Machine |0.6439	|0.6234    |0.7273	|0.6713   |

Al evaluar las métricas obtenidas, se seleccionó el modelo `Best Logistic Regression` como **Champion Model** para implementación en entorno productivo, debido a su capacidad de generalización e interpretabilidad de coeficientes y, especialmente, por su alto Recall con baja variación, priorizando la detección de clientes que abandonan (objetivo de negocio), sin penalizar fuertemente la Precisión.

Como puede verse en las tablas a continuación:

|Model	                     |Recall Validation| Recall Testing |Variación|
|----------------------------|-----------------|----------------|---------|
|Best Random Forest	        |0.7292           |0.6010          |-12.82%  |
|Best Logistic Regression    |0.7167	        |0.6919          |-2.48%   |
|Best K-Nearest Neighbors    |0.7458           |0.7071	       |-3.87%   |
|Best XGBoost Classifier     |0.7000           |0.5909          |-10.91%  |
|Best Support Vector Machine |0.7750		     |0.7273	       |-4.47%   |

|Model                       |F1-score Validation| F1-score Testing|Variación|
|----------------------------|-------------------|-----------------|---------|
|Best Random Forest          |0.7172             | 0.6364          |-8.08%   |
|Best Logistic Regression    |0.6853             | 0.6765          |-0.88%   |
|Best K-Nearest Neighbors    |0.6898             | 0.6683          |-2.15%   |
|Best XGBoost Classifier     |0.7029             | 0.6207          |-8.22%   |
|Best Support Vector Machine |0.7059     	       | 0.6713          |-3.46%   |


El modelo `Best Logistic Regression` es el que sufre menos variaciones en sus métricas, demostrando robustez y buena capacidad de generalización frente a datos nuevos.
A partir de este modelo se identificaron las variables más influyentes en la evasión (tanto protectoras como factores de riesgo) son:

<img width="2963" height="1763" alt="Coeficiente_variables_LogReg_best_coef" src="https://github.com/user-attachments/assets/d6ed9be9-6099-4dad-9ef1-385274af71f7" />
<br>
A partir de las cuales se llevó a cabo el siguiente análisis de impacto:

<img width="3570" height="1768" alt="tabla_coef_logreg" src="https://github.com/user-attachments/assets/422990ad-2f5f-4971-95bf-a8a0ec0a2e77" />

### Pipeline de prueba

Finalmente, se desarrolló la simulación de un pipeline para la implementación del modelo en entorno productivo, utilizando datos sintéticos generados con la técnica `SMOTENC`.
El mismo, recibe un archivo JSON (formato original de la base de datos) con datos crudos (sin ninguna transformación) para producir predicciones.
Cuenta con dos modos de utilización:
* `mode='production'`: que devuelve un archivo JSON con `CustomerID`, `Probabilidad Churn` y `Churn` *(Etiqueta: si Probabilidad Churn > 0.5, Churn = 1, si Probabilidad Churn <= 0.5, Churn = 0)*
* `mode='monitor'`: devuelve un archivo JSON con las métricas `Accuracy`, `Precision`, `Recall` y `F1-score`, y un campo con fecha y hora de ejecución del monitoreo.

Dicho pipeline realiza las transformaciones necesarias sobre los datos crudos utilizando los artefactos creados a lo largo del proyecto.

Los resultados pueden verse en:
* <a href="https://github.com/ignaciomajo/proyecto_TelecomX_ML/tree/main/champion/log/production">Resultados Pipeline Producción</a>
* <a href="https://github.com/ignaciomajo/proyecto_TelecomX_ML/tree/main/champion/log/monitor">Resultados Pipeline Monitoreo</a>


Este enfoque no solo permitió construir un modelo predictivo sólido, sino también demostrar su aplicabilidad real en entornos simulados, sentando las bases para su escalado y mantenimiento en producción.
