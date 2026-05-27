# telecom-analysis

Este repositorio contiene el proyecto de análisis exploratorio de datos (EDA), limpieza, agregación y segmentación de la base de clientes de una empresa de telecomunicaciones. El objetivo principal es identificar patrones de consumo de los usuarios, analizar la distribución de planes tarifarios (Básico vs. Premium) y proveer insights estratégicos y comerciales para la toma de decisiones.

## 🎯 Objetivo del Proyecto

El proyecto busca resolver preguntas clave de negocio mediante el procesamiento de datos históricos de uso telefónico:
1. Integrar y limpiar bases de datos provenientes de tres fuentes distintas.
2. Aplicar técnicas de validación, estandarización de tipos de datos y detección de valores inconsistentes.
3. Construir un perfil estadístico del uso (llamadas y mensajes) por cliente y por segmentos demográficos.
4. Detectar outliers y comportamientos atípicos mediante métodos estadísticos y visuales.
5. Crear segmentaciones de clientes basadas en edad y nivel de uso.
6. Visualizar diferencias entre segmentos y extraer insights comerciales relevantes.

---

## 📊 Datasets Utilizados

El análisis combina tres conjuntos de datos principales estructurados en formato relacional:

* **`users`**: Contiene la información de cada usuario
    * `user_id`: Identificador único del usuario.
    * `first_name` / `last_name`: Nombre y apellido del cliente.
    * `age`: Edad (rango de 18 a 79 años).
    * `city`: Ciudad de residencia (con un 11.72% de valores faltantes iniciales).
    * `reg_date`: Fecha de registro de la línea.
    * `plan`: Tipo de plan contratado (`Basico` o `Premium`).
    * `churn_date`: Fecha de cancelación del servicio (88.35% de valores nulos, indicando clientes activos).
* **`usage`**: Registro detallado e histórico de la actividad generada por los usuarios
    * `user_id`: Identificador del cliente asociado.
    * `type`: Tipo de tráfico (`text` para mensajes, `call` para llamadas, `duration` para consumo en minutos).
    * `date`: Fecha y hora del registro del evento (llamada o mensaje)
    * `duration`: Minutos consumidos en llamadas (presenta valores nulos en interacciones que no son llamadas).
    * `length`: Cantidad de caracteres del mensaje (presenta valores nulos en interacciones que no son mensajes).
* **`plans`**: Catálogo de planes con sus precios y beneficios.
   * `plan_name`: Tipo de plan (Básico o Premium)
   * `messages_included`: Cantidad de mensajes incluidos en el plan por mes
   * `gb_per_month`: Cantidad de gigabytes incluidos en el plan por mes
   * `minutes_included`: Cantidad de minutis incluidos en el plan por mes
   * `usd_monthly_pay`: Costo del plan 
   * `usd_per_gb`: Costo unitario del gigabyte
   * `usd_per_message`: Costo unitario del mensaje
   * `usd_per_minute`: Costo unitario del minuto 
---

## 🔄 Etapas del Análisis Realizadas

1.  Tratamiento y Diagnóstico de Calidad de Datos: Se evaluó los porcentajes de valores faltantes (ej. `City`, `churn_date`, `duration`). Se imputaron ceros en consumos nulos para posibilitar operaciones matemáticas precisas.
2.  Ingeniería de Características y Agregación: Se establecieron variables auxiliares booleanas (`is_text`, `is_call`, `is_duration`) para agrupar y resumir la información mediante `.groupby('user_id').agg()` generando las métricas consolidadas: `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada`.
3.  Unión de Tablas (Merging): Se combianaron los data sets users y usage con la tabla agregada mediante un `left join` sobre la clave común `user_id` para construir la matriz maestra `user_profile`.
4.  Análisis de Distribuciones: Se generaron histogramas con Seaborn (`sns.histplot`) cruzados por el parámetro `hue='plan'` para evaluar las formas de distribución (uniforme en edad y exponencial/sesgada a la derecha en consumo de servicios).
5.  Detección Estadística de Outliers (IQR): Se aplicó la regla del Rango Intercuartílico para establecer los techos normales de consumo, determinando la conveniencia de mantener los valores extremos en el negocio.
6.  Segmentación Multivariable: Se construyó lógica condicional con Pandas/NumPy para segmentar a los usuarios en categorías de uso (`Bajo uso`, `Uso medio` y `Alto uso`) y visualizarlos mediante histogramas.

---

## 🚀 Cómo Ejecutar el Notebook desde Jupyter Notebook Local

1. Clona este repositorio en tu máquina:
   ```bash
   git clone [https://github.com/tu-usuario/telecom-analysis.git](https://github.com/tu-usuario/telecom-analysiss.git)
2. Instala las librerías necesarias en tu entorno de Python (se recomienda usar un entorno virtual):
   ```bash
  pip install pandas numpy matplotlib seaborn
4. Inicia el servidor de Jupyter:
  ```bash
  jupyter notebook
5. Abre el archivo del proyecto y ejecuta todas las celdas en orden jerárquico.

## 🚀 Guía corta de reproducción

Para garantizar que el flujo de ejecución no falle y los gráficos se generen correctamente, sigue estas directrices:

1. Orden estricto: No alteres el orden de ejecución de las celdas. La tabla maestra user_profile requiere que las operaciones previas de agregación de la tabla usage hayan concluido exitosamente.
2. Manejo de Errores Comunes: * Si experimentas un SyntaxError al graficar o agrupar, asegúrate de no haber olvidado comas entre los argumentos y de pasar correctamente las variables a los ejes (ejemplo: x='age', hue='plan').
3. Si experimentas un KeyError, comprueba que ejecutaste la celda de la unión (pd.merge) para que user_profile contenga las columnas de consumo calculadas.
4. Resultados Esperados: El notebook finalizará mostrando un resumen ejecutivo automatizado y visualizaciones profesionales que segmentan el perfil del consumidor de ConnectaTel.
