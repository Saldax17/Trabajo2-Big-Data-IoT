# 🌎 Proyecto 2 – Arquitectura Batch Big Data en AWS (EAFIT 2025-2)

**Autor:** Santiago Saldarriaga Saldarriaga  
**Curso:** SI7006 / SI6003 – Almacenamiento y Procesamiento de Grandes Datos  
**Fecha de entrega:** 31 de octubre de 2025  

---

## 🧠 Descripción general

Este proyecto implementa una **solución analítica batch** en la nube para el monitoreo de la calidad del aire en espacios interiores (como parqueaderos o túneles).  
El objetivo es transformar datos históricos en **conocimiento útil**, aplicando análisis exploratorio y modelos de aprendizaje automático sobre un **Data Lake en AWS S3**.

---

## 🏗️ Arquitectura general

La solución sigue un **ciclo de vida Batch clásico**, estructurado en seis fases:

1. **Ingesta:**  
   Carga del dataset *IoT Indoor Air Quality* desde Kaggle hacia la zona `raw/` del bucket S3 `ssaldarridatalake2`.

2. **Almacenamiento:**  
   Creación de un **Data Lake** en S3 con zonas:
   - `raw/` – Datos originales.  
   - `trusted/` – Datos limpios y transformados.  
   - `refined/` – Resultados del EDA y predicciones del modelo.

3. **Preparación:**  
   Limpieza, transformación y normalización de los datos mediante **PySpark** en **AWS EMR** o **Google Colab**, generando salidas optimizadas en formato **Parquet**.

4. **Catalogación:**  
   Uso de **AWS Glue Crawler** para registrar los datasets procesados dentro de la base `proyecto1db`, habilitando consultas en **Amazon Athena** y **SparkSQL**.

5. **Análisis exploratorio (EDA):**  
   Cálculo de métricas por `ventilation_status`, promedios de temperatura, humedad y CO₂, máximos de PM2.5 y desviaciones estándar, almacenadas en  
   `s3://ssaldarridatalake2/proyecto1/refined/iot_summary/`.

6. **Modelado predictivo:**  
   Entrenamiento de un **Random Forest Classifier** de **SparkML** para predecir el estado de ventilación (`On/Off`) a partir de variables ambientales.  
   Resultados guardados en `s3://ssaldarridatalake2/proyecto1/refined/iot_predictions/`.

---

## ☁️ Componentes AWS utilizados

| Servicio | Rol principal |
|-----------|----------------|
| **S3** | Almacenamiento del Data Lake (`raw`, `trusted`, `refined`). |
| **EMR** | Procesamiento distribuido con PySpark y ejecución del modelo ML. |
| **Glue** | Catalogación automática y definición del esquema de datos. |
| **Athena** | Consulta SQL directa sobre los datos catalogados. |
| **IAM** | Control de accesos y credenciales seguras para EMR / Colab. |

---

## 💻 Implementación en Google Colab

Se configuró un entorno PySpark en Colab para:

- Conectarse a **S3** mediante `fs.s3a` y credenciales IAM.  
- Leer los datos Parquet desde la zona `refined/`.  
- Ejecutar nuevamente el EDA y el modelo ML.  
- Visualizar resultados mediante `matplotlib` y `seaborn`.

Ejemplo de conexión:

```python
from pyspark.sql import SparkSession
spark = (SparkSession.builder
         .appName("S3Connection")
         .master("local[*]")
         .config("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem")
         .getOrCreate())
