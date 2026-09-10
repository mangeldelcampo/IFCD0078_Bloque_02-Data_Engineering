
¿que es Direct Lake en Microsoft Fabric?

**Direct Lake** es un modo de almacenamiento de tablas en el modelo semántico de **Power BI** dentro de **Microsoft Fabric** que permite analizar grandes volúmenes de datos con el alto rendimiento del modo de importación y la actualización en tiempo casi real de DirectQuery, sin necesidad de duplicar los datos.

Funciona leyendo directamente las **tablas Delta** almacenadas en **OneLake** (el repositorio único de datos de Fabric) mediante el motor de almacenamiento **VertiPaq**. A diferencia del modo de importación, que copia físicamente los datos, Direct Lake solo carga en memoria las columnas necesarias para la consulta y realiza actualizaciones de bajo costo que consisten en copiar únicamente los metadatos (un proceso conocido como *framing*) para reflejar los cambios más recientes en OneLake.

Este modo es ideal para arquitecturas de *lakehouse* y almacenes de datos que requieren:
*   **Máximo rendimiento** en consultas DAX comparables al modo de importación.
*   **Baja latencia** de datos, ya que los nuevos datos están disponibles inmediatamente tras ser escritos en el lago.
*   **Eficiencia de almacenamiento**, evitando la redundancia de copiar datos completos en el modelo semántico.

Para utilizarlo, los datos deben residir en formatos Delta compatibles con Fabric, y en casos donde no es posible cargar directamente desde una tabla Delta (como al usar vistas SQL), el sistema puede recurrir automáticamente al modo DirectQuery.

https://learn.microsoft.com/es-es/fabric/fundamentals/direct-lake-overview
