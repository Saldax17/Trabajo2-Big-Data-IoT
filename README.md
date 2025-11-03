⚙️ Instrucciones de ejecución
🔹 Opción 1 – Ejecución en AWS EMR

Subir el notebook Trabajo #2 - Big Data Definitivo.ipynb a EMR Notebook o Zeppelin.

Ajustar rutas del bucket S3 (raw, trusted, refined).

Ejecutar todas las celdas del flujo ETL y modelo ML.

🔹 Opción 2 – Ejecución en Google Colab

Subir el notebook Trabajo_2_colab_pyspark_Definitivo.ipynb a Colab.

Insertar las credenciales AWS IAM (Access Key y Secret Key).

Ejecutar las celdas de configuración e instalación de Spark.

Confirmar la conexión (✅ SparkSession creada con soporte S3A).

Leer los archivos Parquet desde S3 y realizar el análisis visual.

📊 Visualización y análisis final

La visualización se realiza sobre los datos refinados (iot_summary) para identificar patrones en las variables ambientales:

import matplotlib.pyplot as plt
df_s3.toPandas().groupby("hour")["CO2"].mean().plot(kind="line", figsize=(8,4))
plt.title("Concentración promedio de CO₂ por hora del día")
plt.xlabel("Hora")
plt.ylabel("CO₂ (ppm)")
plt.grid(True)
plt.show()


Ejemplo de resultados esperados:

Tendencia horaria del CO₂.

Correlación entre temperatura y ventilación.

Distribución de humedad según estado de ventilación.

📂 Estructura del repositorio
├── README.md
├── si7006-252-trabajo2-Santiago-Saldarriaga-Saldarriaga.pdf
├── Trabajo #2 - Big Data Definitivo.ipynb          # Notebook usado en EMR
├── Trabajo_2_colab_pyspark_Definitivo.ipynb        # Notebook con conexión S3A desde Colab
├── /scripts/                                       # Scripts PySpark o SQL adicionales
└── /data/                                          # Ejemplos o muestras de datos (si aplica)

📈 Resultados destacados

Implementación completa del flujo Batch Big Data en AWS.

Conexión validada entre PySpark (Colab) y AWS S3.

Modelo de Random Forest entrenado y almacenado en la zona refined.

Visualizaciones de comportamiento ambiental basadas en datos reales.

Integración exitosa entre S3, EMR, Glue, Athena y Colab.

🧾 Conclusiones

El proyecto demuestra el ciclo completo de procesamiento batch en AWS, integrando componentes analíticos y de almacenamiento a gran escala.
El uso de PySpark tanto en EMR como en Colab facilita la experimentación y análisis, mientras que el Data Lake asegura escalabilidad y trazabilidad del proceso.

🤝 Créditos

Proyecto desarrollado por Santiago Saldarriaga Saldarriaga
como parte del curso Almacenamiento y Procesamiento de Grandes Datos – Universidad EAFIT (2025-2).

📜 Licencia

Este proyecto se distribuye con fines académicos bajo la licencia MIT.

