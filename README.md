# 🌎 Proyecto 2 – Arquitectura Batch Big Data en AWS (EAFIT 2025-2)

**Autor:** Santiago Saldarriaga Saldarriaga  
**Curso:** SI7006 / SI6003 – Almacenamiento y Procesamiento de Grandes Datos  
**Fecha de entrega:** 31 de octubre de 2025  

---

## 🧠 Descripción general

Este proyecto implementa una **solución analítica batch** en la nube para el monitoreo de la calidad del aire en espacios interiores (por ejemplo, parqueaderos o túneles).  
El objetivo principal es transformar datos históricos de sensores IoT en **conocimiento útil**, aplicando análisis exploratorio y modelos de aprendizaje automático sobre un **Data Lake en AWS S3**.

---

## 🏗️ Arquitectura general

La solución se diseñó siguiendo el flujo **Batch Architecture**, con separación por zonas en el Data Lake y herramientas de AWS:

1. **Ingesta:**  
   Carga del dataset *IoT Indoor Air Quality* desde Kaggle hacia la zona `raw/` del bucket S3 `ssaldarridatalake2`.

2. **Almacenamiento:**  
   Creación de un **Data Lake** con las zonas:
   - `raw/` → Datos originales sin procesar.  
   - `trusted/` → Datos limpios y transformados.  
   - `refined/` → Resultados finales del análisis y modelo ML.

3. **Preparación:**  
   Limpieza, transformación y normalización de los datos con **PySpark** ejecutado en **AWS EMR** o **Google Colab**, generando salidas optimizadas en formato **Parquet**.

4. **Catalogación:**  
   Uso de **AWS Glue Crawler** para registrar los datasets en la base `proyecto1db` y habilitar consultas con **Amazon Athena** y **SparkSQL**.

5. **Análisis Exploratorio (EDA):**  
   Cálculo de métricas ambientales (promedios, máximos, desviaciones) por hora y por estado de ventilación, almacenadas en  
   `s3://ssaldarridatalake2/proyecto1/refined/iot_summary/`.

6. **Modelado Predictivo:**  
   Entrenamiento de un modelo **Random Forest Classifier** con **SparkML** para predecir el estado de ventilación (`ventilation_status`) en función de variables ambientales.  
   Resultados almacenados en `s3://ssaldarridatalake2/proyecto1/refined/iot_predictions/`.

---

## ☁️ Componentes AWS utilizados

| Servicio | Funcionalidad |
|-----------|----------------|
| **Amazon S3** | Data Lake con zonas `raw`, `trusted` y `refined`. |
| **Amazon EMR** | Ejecución distribuida de PySpark y entrenamiento del modelo ML. |
| **AWS Glue** | Descubrimiento y catalogación automática de los datos procesados. |
| **Amazon Athena** | Consulta SQL sobre datos en formato Parquet. |
| **IAM** | Gestión de credenciales seguras para acceso desde EMR y Colab. |

---

## 💻 Implementación en Google Colab

En Colab se configuró un entorno **PySpark** con acceso directo a **S3** mediante el conector `s3a://`.  
Este entorno permitió realizar análisis exploratorio, modelado y visualización directamente desde la nube.

### 🔧 Configuración resumida de la conexión
```python
from pyspark.sql import SparkSession
import os

os.environ["AWS_ACCESS_KEY_ID"] = "TU_ACCESS_KEY"
os.environ["AWS_SECRET_ACCESS_KEY"] = "TU_SECRET_KEY"
os.environ["AWS_REGION"] = "us-west-1"

spark = (
    SparkSession.builder
    .appName("S3Connection")
    .master("local[*]")
    .config("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem")
    .config("spark.hadoop.fs.s3a.aws.credentials.provider",
            "com.amazonaws.auth.EnvironmentVariableCredentialsProvider")
    .config("spark.hadoop.fs.s3a.endpoint", "s3.us-west-1.amazonaws.com")
    .config("spark.hadoop.fs.s3a.path.style.access", "true")
    .getOrCreate()
)
