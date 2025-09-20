# Desarrollo: Arquitectura de Big Data para Airbnb en la Ciudad de México

A continuación, se presenta una propuesta de arquitectura de Big Data diseñada para la empresa Airbnb, enfocada en el análisis de sus operaciones en un mercado clave como la Ciudad de México. El objetivo es estructurar un sistema capaz de procesar la vasta cantidad de datos generados por la plataforma para extraer información estratégica que permita optimizar la oferta, personalizar la experiencia del usuario y mejorar la toma de decisiones operativas.

## Pasos Fundamentales de la Arquitectura

La arquitectura se define siguiendo cinco pasos clave, desde la identificación de las fuentes de datos hasta la entrega de valor a través de la información procesada.

### 1. Identificación de los Orígenes de los Datos

El primer paso consiste en identificar todas las fuentes de datos relevantes para el negocio. Para Airbnb, estas fuentes son increíblemente diversas y generan datos con diferentes estructuras y velocidades.

*   **Datos de la Plataforma (Transaccionales y de Interacción):**
    *   **Listings (Anuncios):** Información detallada de las propiedades, incluyendo características (precio, número de habitaciones, servicios), ubicación geográfica, descripciones textuales y fotografías. Estos datos son semiestructurados.
    *   **Reviews (Reseñas):** Comentarios y calificaciones dejadas por los huéspedes. Son una fuente rica de datos no estructurados (texto libre) que contiene percepciones sobre la calidad, la ubicación y la experiencia general.
    *   **Calendar (Calendario de Disponibilidad):** Datos sobre la disponibilidad y precios de las propiedades a lo largo del tiempo. Son datos estructurados que cambian con alta frecuencia.
    *   **Interacciones de Usuario:** Logs de clics, búsquedas, filtros aplicados y propiedades visualizadas. Estos datos de comportamiento son fundamentales para entender las preferencias y la demanda.

*   **Datos Externos:**
    *   **Datos Demográficos y Socioeconómicos:** Información de fuentes como el INEGI sobre la población, ingresos y características de las diferentes alcaldías de la Ciudad de México para contextualizar la oferta y la demanda.
    *   **Eventos y Temporalidades:** Calendarios de eventos masivos, conciertos, días festivos y temporadas vacacionales que impactan directamente en la demanda de alojamiento.
    *   **Datos de Competidores:** Información sobre precios y disponibilidad de hoteles y otras plataformas de alojamiento para análisis competitivo.
    *   **Datos Geoespaciales:** Mapas de transporte público, zonas de interés turístico, índices de seguridad por colonia, etc.

*   **Datos de Redes Sociales:**
    *   Menciones y opiniones sobre Airbnb y experiencias de viaje en la Ciudad de México en plataformas como X (antes Twitter), Instagram o blogs de viajes. Son datos no estructurados clave para el análisis de sentimiento.

### 2. Obtención de los Datos (Ingesta)

Una vez identificadas las fuentes, se deben implementar mecanismos para recolectar y centralizar los datos.

*   **Tecnologías Propuestas:**
    *   **Apache Kafka:** Se propone como el bus de mensajería central para la ingesta de datos en tiempo real (streaming). Es ideal para capturar flujos de eventos como las interacciones de los usuarios en la web, actualizaciones de calendarios y nuevas reseñas. Su alta capacidad y tolerancia a fallos lo hacen perfecto para manejar picos de demanda.
    *   **Apache Flume o Logstash:** Herramientas especializadas en la recolección de logs de servidores web, que capturan las interacciones de los usuarios de manera eficiente para ser enviadas a Kafka.
    *   **APIs y Web Scraping:** Para los datos externos (eventos, competidores, datos gubernamentales), se utilizarían scripts en Python (con librerías como `Requests` y `BeautifulSoup`/`Scrapy`) orquestados por **Apache Airflow** para programar su ejecución periódica y asegurar la actualización constante de la información.

### 3. Almacenamiento de los Datos

Los datos recolectados necesitan un lugar donde ser almacenados de forma segura, escalable y accesible. Se propone una arquitectura de almacenamiento híbrida conocida como "Data Lakehouse".

*   **Tecnologías Propuestas:**
    *   **Amazon S3 (o similar como Google Cloud Storage):** Serviría como el **Data Lake**, el repositorio central para todos los datos en su formato original (bruto). Su bajo costo, durabilidad y escalabilidad infinita lo hacen ideal para almacenar grandes volúmenes de datos estructurados, semiestructurados y no estructurados (imágenes, textos, logs, CSVs).
    *   **Databricks Delta Lake (o Apache Hudi/Iceberg):** Se implementaría sobre el Data Lake para crear un **Lakehouse**. Esta capa de software de código abierto aporta capacidades de un Data Warehouse al Data Lake, como transacciones ACID, control de versiones de datos (Time Travel) y la unificación del procesamiento de datos en batch y streaming. Permite tener tablas de datos fiables y de alto rendimiento directamente sobre los archivos en S3.
    *   **Amazon Redshift o Google BigQuery (Data Warehouse):** Para datos ya procesados, agregados y modelados que alimentarán los dashboards de Business Intelligence (BI). Estos sistemas ofrecen un rendimiento de consulta extremadamente rápido para análisis interactivo por parte de los analistas de negocio.
    *   **MongoDB o Cassandra (Bases de Datos NoSQL):** Para almacenar datos específicos como perfiles de usuario o catálogos de propiedades que requieren alta disponibilidad y baja latencia de acceso desde la aplicación principal.

### 4. Tratamiento de los Datos (Procesamiento)

En esta fase, los datos brutos se limpian, transforman, enriquecen y agregan para prepararlos para el análisis.

*   **Tecnologías Propuestas:**
    *   **Apache Spark:** Es el motor de procesamiento unificado por excelencia. Se utilizaría para la mayoría de las tareas de ETL (Extracción, Transformación y Carga) y ELT. Corriendo sobre un clúster (gestionado por servicios como Amazon EMR o Databricks), Spark puede procesar grandes volúmenes de datos en paralelo de manera muy eficiente.
        *   **Spark SQL:** Para que los analistas y científicos de datos exploren y transformen los datos usando un lenguaje familiar como SQL.
        *   **Spark Streaming:** Para procesar los datos en tiempo real provenientes de Kafka, permitiendo casos de uso como la detección de fraudes o la monitorización de tendencias al minuto.
        *   **MLlib (Machine Learning Library):** Para entrenar modelos de aprendizaje automático, como modelos de recomendación de propiedades, predicción de precios (tarifación dinámica) o análisis de sentimiento de las reseñas.
    *   **Python (con Pandas, scikit-learn):** Ampliamente utilizado por los científicos de datos para la exploración, limpieza y modelado en fases iniciales o con conjuntos de datos más pequeños.

### 5. Utilización de la Información Resultante (Consumo)

La última capa es donde se extrae el valor final de los datos y se pone a disposición de los usuarios finales.

*   **Tecnologías Propuestas:**
    *   **Herramientas de BI y Visualización (Tableau, Power BI, Metabase):** Conectadas al Data Warehouse (Redshift/BigQuery), estas herramientas permitirían a los equipos de negocio crear dashboards interactivos y reportes para monitorizar KPIs clave, como la tasa de ocupación por alcaldía, la evolución de precios, la satisfacción del cliente, etc.
    *   **APIs de Datos:** Se desarrollarían APIs (usando frameworks como FastAPI en Python) para servir los resultados de los modelos de Machine Learning. Por ejemplo, una API que devuelva precios recomendados para una nueva propiedad o una que ofrezca recomendaciones personalizadas a un usuario en tiempo real en la aplicación.
    *   **Jupyter Notebooks y Zeppeling:** Para que los científicos de datos realicen análisis exploratorios avanzados, creen prototipos de modelos y compartan sus hallazgos de manera interactiva con otros stakeholders.

## Diagrama de la Arquitectura Propuesta (Formato draw.io)

El diagrama de la arquitectura se encuentra en el archivo `arquitectura_airbnb.drawio.xml`, que puede ser importado directamente en la herramienta draw.io o en servicios compatibles como Lucidchart o el plugin de Visual Studio Code.

### Explicación del Diagrama

1.  **Orígenes de Datos (Capa Superior):** Representa las diversas fuentes de información. Se divide en datos internos de la plataforma (anuncios, reseñas, logs), datos externos (APIs, web scraping) y redes sociales. Cada uno genera un tipo de dato diferente (estructurado, semiestructurado, no estructurado).
2.  **Ingesta de Datos:** Es la puerta de entrada. **Flume/Logstash** recolecta logs, **Apache Airflow** orquesta la recolección de datos externos en batch, y todo converge en **Apache Kafka**, que actúa como un búfer centralizado para los flujos de datos en tiempo real y por lotes.
3.  **Almacenamiento (Capa Central):** Aquí reside la data.
    *   **Amazon S3** actúa como el Data Lake, guardando una copia fiel de todos los datos brutos.
    *   **Delta Lake** se superpone a S3 para aportar fiabilidad y rendimiento, creando el Lakehouse.
    *   **Amazon Redshift** funciona como el Data Warehouse para los datos ya limpios y agregados, listos para el análisis de negocio.
    *   **MongoDB** sirve para casos de uso operacionales que requieren acceso rápido a datos específicos.
4.  **Tratamiento y Procesamiento:** El cerebro de la arquitectura. **Apache Spark** se conecta a las fuentes de almacenamiento (principalmente el Lakehouse) para ejecutar tareas de limpieza, transformación, agregación y, crucialmente, para entrenar y ejecutar modelos de Machine Learning (ML).
5.  **Utilización y Consumo (Capa Inferior):** Es la interfaz con el usuario final. Los analistas de negocio utilizan **Tableau/Power BI** para visualizar los datos del Data Warehouse. Las aplicaciones consumen los resultados de los modelos de ML a través de **APIs**. Y los científicos de datos usan **Jupyter Notebooks** para explorar los datos en el Lakehouse y desarrollar nuevos modelos.

## Referencias

Cox, M. (s.f.). *Inside Airbnb: Get the Data*. Recuperado el 20 de septiembre de 2025, de [http://insideairbnb.com/get-the-data/](http://insideairbnb.com/get-the-data/)
