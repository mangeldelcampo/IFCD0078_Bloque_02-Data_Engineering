# ¿Qué ocurre por debajo al crear un Warehouse en Microsoft Fabric?

Cuando creas un **Warehouse (Almacén de datos)** en Microsoft Fabric (por ejemplo, al realizar el laboratorio *"Design and implement a dimensional model"*), la plataforma no está creando una base de datos relacional tradicional (con archivos `.mdf` o `.ndf` como en SQL Server). En su lugar, Fabric despliega una arquitectura moderna que separa el almacenamiento del procesamiento.

Por debajo, esto es lo que Fabric crea y aprovisiona exactamente:

### 1. Almacenamiento Físico: Carpetas en formato Delta Parquet dentro de OneLake
Cada vez que creas una tabla en tu Warehouse, Fabric no la guarda en un formato propietario. Los datos se almacenan físicamente como archivos **Parquet** encapsulados bajo el estándar abierto **Delta Lake**, todo esto dentro de **OneLake** (el OneDrive para datos de Fabric).
* **¿Qué significa esto en tu laboratorio?** Cuando ejecutes las instrucciones `CREATE TABLE` para tus dimensiones y hechos, Fabric está creando carpetas en OneLake y preparándolas para escribir los logs de transacciones Delta y los archivos de datos Parquet.

### 2. Motor de Computación: SQL Distribuido (Motor Polaris)
Fabric aprovisiona acceso a su motor de procesamiento de consultas masivamente paralelo (MPP), conocido internamente como la arquitectura **Polaris**. 
* Es completamente *Serverless* (sin servidor). No tienes que elegir cuántos núcleos o RAM necesitas; el motor escala automáticamente para leer los archivos Delta Parquet de OneLake y procesar tus consultas T-SQL al instante.

### 3. Punto de Conexión SQL (TDS Endpoint)
Fabric crea una cadena de conexión estándar (TDS) que "engaña" a las herramientas cliente haciéndoles creer que se están conectando a un SQL Server tradicional.
* Esto te permite conectarte al Warehouse usando SQL Server Management Studio (SSMS), Azure Data Studio, Power BI o aplicaciones de terceros, y usar comandos T-SQL normales (DDL y DML).

### 4. Un Modelo Semántico Predeterminado (Default Semantic Model)
Automáticamente, al crear el Warehouse, Fabric genera un **modelo semántico de Power BI** vinculado a él. 
* A medida que vayas creando tus tablas de hechos y dimensiones en el laboratorio, este modelo semántico se actualizará automáticamente. 
* Este modelo utiliza el modo **Direct Lake**, lo que significa que Power BI leerá directamente los archivos Delta Parquet en OneLake a la velocidad de la memoria, sin tener que hacer importaciones de datos ni ejecutar consultas SQL pesadas (DirectQuery).

### 5. Artefactos del Explorador (Metastore)
Fabric crea un catálogo de metadatos que registra tus esquemas (`dbo`, etc.), vistas, procedimientos almacenados y funciones. Todo esto se visualiza en la interfaz gráfica que usas en el navegador durante tu laboratorio (el explorador de objetos de Fabric).

---

> **💡 En resumen:**
> Aunque tú vas a escribir código T-SQL clásico (`CREATE TABLE`, `INSERT`, `SELECT`) para construir un modelo en estrella (Dimensiones y Hechos), por debajo Fabric está orquestando un motor distribuido que escribe y lee archivos **Delta Lake en OneLake**, preparándolos automáticamente para ser consumidos de forma ultrarrápida por Power BI mediante **Direct Lake**.