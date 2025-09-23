# Respuestas al Foro de Discusión - Semana 4

A continuación, se presentan las respuestas a las preguntas del foro, basadas en el análisis de la bibliografía de referencia y la arquitectura de Big Data propuesta para Airbnb en la Ciudad de México.

### ¿En qué consiste la computación distribuida?

La computación distribuida es un modelo en el que las tareas de procesamiento de información se dividen en fragmentos más pequeños y se ejecutan de manera simultánea en múltiples computadores autónomos (nodos) que están conectados en red. En lugar de depender de un único y potente equipo centralizado, este paradigma aprovecha la potencia combinada de varias máquinas para resolver un problema común. Según Santos y Costa (2020), el ciclo de vida del Big Data requiere inherentemente un entorno que pueda manejar un procesamiento masivo y paralelo, algo que los sistemas centralizados no pueden ofrecer de manera eficiente.

Los principios fundamentales de la computación distribuida son:
*   **Paralelismo:** Las tareas se ejecutan al mismo tiempo en diferentes nodos, lo que reduce drásticamente el tiempo total de procesamiento.
*   **Tolerancia a fallos:** El sistema está diseñado para seguir funcionando incluso si uno o varios de sus componentes (nodos) fallan. Esto se logra a menudo mediante la replicación de datos y tareas a través del clúster.
*   **Coordinación:** Aunque los nodos son autónomos, un componente central (como un gestor de recursos) coordina la asignación de tareas y la consolidación de los resultados.

Este enfoque es la piedra angular de prácticamente todas las arquitecturas de Big Data, ya que proporciona la única vía viable para gestionar los enormes volúmenes de datos generados actualmente (Ryzko, 2020).

### ¿Qué son los sistemas distribuidos escalables?

Un sistema distribuido escalable es aquel que puede mantener o mejorar su rendimiento y capacidad de respuesta a medida que aumenta la carga de trabajo, simplemente añadiendo más recursos a la red. La escalabilidad es uno de los requisitos más críticos en el ciclo de vida del Big Data (Santos & Costa, 2020).

Existen dos tipos principales de escalabilidad:
1.  **Escalabilidad Vertical (Scaling Up):** Consiste en aumentar la capacidad de un único servidor añadiendo más recursos como CPU, RAM o almacenamiento. Este enfoque tiene límites físicos y su costo aumenta exponencialmente.
2.  **Escalabilidad Horizontal (Scaling Out):** Consiste en añadir más máquinas (nodos) al sistema para distribuir la carga de trabajo entre ellas. Este es el enfoque preferido en las arquitecturas de Big Data, ya que permite un crecimiento casi ilimitado utilizando hardware de bajo costo (*commodity hardware*).

Los sistemas distribuidos escalables, como los que soportan plataformas en la nube (Ryzko, 2020), están diseñados desde su origen para escalar horizontalmente. Tecnologías como Apache Hadoop, Apache Spark y las bases de datos NoSQL son ejemplos de sistemas que permiten a las organizaciones empezar con un clúster pequeño y expandirlo progresivamente a medida que sus necesidades de datos crecen, garantizando así un rendimiento sostenible a largo plazo.

### Justificación de la Arquitectura de Big Data para Airbnb

Para el caso de Airbnb en la Ciudad de México, se propuso una **arquitectura *Lakehouse* basada en la nube**, que unifica el procesamiento de datos en tiempo real (*streaming*) y por lotes (*batch*). La elección de esta arquitectura se justifica por la naturaleza de los datos que maneja la empresa y sus objetivos de negocio.

**Justificación de la Arquitectura:**

La operación de Airbnb genera datos que se caracterizan por las "V" del Big Data:
*   **Volumen:** Millones de listados, reseñas, calendarios de disponibilidad e interacciones de usuarios generan un volumen masivo de datos que un sistema tradicional no podría gestionar.
*   **Variedad:** La plataforma maneja datos estructurados (precios, fechas), semiestructurados (descripciones de anuncios en formato JSON) y no estructurados (reseñas en texto libre, fotografías, logs de clics). Una arquitectura *Lakehouse* es ideal porque permite almacenar todos estos formatos en un único repositorio central (Data Lake) y aplicarles esquemas y gobernanza (como en un Data Warehouse).
*   **Velocidad:** Los datos se generan a alta velocidad. Las interacciones de los usuarios, las actualizaciones de precios y la disponibilidad de las propiedades ocurren en tiempo real. Esto exige una arquitectura capaz de ingerir y procesar flujos de datos continuos para casos de uso como la tarificación dinámica o las recomendaciones personalizadas.

**Integración de Sistemas Distribuidos:**

La arquitectura propuesta depende enteramente de sistemas distribuidos en cada una de sus capas para lograr la escalabilidad, resiliencia y rendimiento necesarios:
1.  **Ingesta:** Se utiliza **Apache Kafka** como bus de mensajería. Kafka es un sistema distribuido que puede manejar múltiples flujos de datos en tiempo real de forma concurrente y tolerante a fallos, actuando como un búfer central antes del almacenamiento.
2.  **Almacenamiento:** El núcleo del almacenamiento es **Amazon S3**, un servicio de almacenamiento de objetos distribuido que ofrece una escalabilidad prácticamente infinita y alta durabilidad. Sobre él, la capa **Delta Lake** gestiona los datos de manera transaccional, pero manteniendo la naturaleza distribuida. Para datos operativos, se emplean bases de datos **NoSQL** (como MongoDB o Cassandra), que son sistemas distribuidos por diseño para garantizar baja latencia y alta disponibilidad.
3.  **Procesamiento:** El motor principal es **Apache Spark**. Este es un sistema de computación distribuida que ejecuta las tareas de limpieza, transformación y análisis de datos en paralelo a través de un clúster de nodos. Es su capacidad para distribuir la carga de trabajo lo que permite procesar petabytes de datos de manera eficiente, ya sea para análisis por lotes o para el entrenamiento de modelos de *Machine Learning*.
4.  **Consumo:** Incluso la capa final, donde se sirven los datos a los analistas a través de un *Data Warehouse* como **Amazon Redshift**, es un sistema de base de datos distribuido y columnar, optimizado para ejecutar consultas analíticas complejas sobre grandes volúmenes de datos de forma paralela.

En conclusión, la arquitectura para Airbnb se ha diseñado explícitamente sobre un ecosistema de sistemas distribuidos, ya que es la única forma de cumplir con los requisitos de volumen, variedad y velocidad que impone su modelo de negocio, transformando los datos en un activo estratégico para la toma de decisiones.

### Referencias

Alla, S. (2018). *Big data analytics with Hadoop 3: Build highly effective analytics solutions to gain valuable insight into your big data*. Packt Publishing.

Ciavotta, M., et al. (2022). Supporting semantic data enrichment at scale. En E. Curry, et al. (Eds.), *Technologies and applications for big data value* (pp. 103-109). Springer International Publishing AG.

Cox, M. (s.f.). *Get the data*. Inside Airbnb. Recuperado el 20 de septiembre de 2025, de http://insideairbnb.com/get-the-data/

Curry, E., et al. (Eds.). (2022). *Technologies and applications for big data value*. Springer International Publishing AG.

Ryzko, D. (2020). *Modern big data architectures: A multi-agent systems perspective*. John Wiley & Sons, Incorporated.

Santos, M., & Costa, C. (2020). *Big data: Concepts, warehousing, and analytics*. River Publishers.

Serrano, M., et al. (2022). Communications quality. En E. Curry, et al. (Eds.), *Technologies and applications for big data value* (pp. 21-80). Springer International Publishing AG.
