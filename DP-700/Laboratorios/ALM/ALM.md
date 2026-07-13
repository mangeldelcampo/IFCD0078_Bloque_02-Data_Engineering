ALM significa Application Lifecycle Management (Gestión del Ciclo de Vida de las Aplicaciones).

# Application Lifecycle Management (ALM)

[⬅️ Volver a Laboratorio 1 (readme1.md)](readme1.md) | [🏠 Volver al Inicio (Readmedp-700.md)](../Readmedp-700.md)

---

ALM significa Application Lifecycle Management (Gestión del Ciclo de Vida de las Aplicaciones).

En términos sencillos, es la disciplina, los procesos y las herramientas que utilizamos para gobernar un proyecto de software o de datos desde que es apenas una idea hasta que se retira de producción. No se trata solo de escribir código, sino de todo lo que rodea a ese código para que sea seguro, auditado, colaborativo y escalable.

![Fases de ALM](/DP-700/Laboratorios/ALM/imagenes/imagen_ALM.png)

## Las Fases Clásicas de ALM

Tradicionalmente, cualquier estrategia ALM se divide en estas etapas cíclicas:

* **Requisitos y Planificación:** Qué vamos a construir y por qué.
* **Desarrollo:** Creación de la solución. Aquí entra en juego el control de versiones (como Git) para que varios desarrolladores puedan trabajar juntos sin sobrescribir el trabajo del otro.
* **Pruebas (Testing / QA):** Validar que lo construido funciona correctamente, cumple los requisitos de negocio y no rompe funcionalidades previas.
* **Despliegue (Deployment):** Promover la solución validada de un entorno de pruebas a un entorno productivo de forma automatizada, predecible y segura.
* **Operaciones y Mantenimiento:** Monitorización continua en producción, resolución de incidencias y optimización.

## ALM en el contexto de Microsoft Fabric y el DP-700

Cuando hablamos de ALM en esta certificación, no estamos creando una aplicación web tradicional, estamos aplicando estos conceptos al mundo de los datos, lo que a menudo se conoce como **DataOps**.

En Fabric, tus "aplicaciones" son los distintos artefactos que construyes: flujos de datos (Dataflows Gen2), cuadernos de PySpark (Notebooks), modelos semánticos y estructuras de Lakehouse/Warehouse. Implementar ALM aquí significa abandonar la mala práctica de modificar código directamente en Producción y adoptar un flujo profesional que el examen evaluará a fondo:

* **Control de código fuente:** Utilizar la integración con Git (a través de Azure DevOps o GitHub) sincronizada exclusivamente con tu área de trabajo de *Development*. Esto te permite mantener un historial exacto (commits y ramas) de cada cambio en tus pipelines de datos.
* **Despliegue entre entornos:** Utilizar los Deployment Pipelines (lo que configuramos en el laboratorio) para automatizar la promoción de esos artefactos ya validados desde Desarrollo hacia Pruebas y, finalmente, hacia Producción.
* **Aislamiento y Seguridad:** Garantizar que los usuarios de negocio solo consumen datos del entorno de Producción, mientras que los ingenieros de datos pueden romper y arreglar cosas libremente en el entorno de Desarrollo sin afectar las operaciones críticas de la empresa.

---

[⬅️ Volver a Laboratorio 1 (readme1.md)](../1.Implement_Continuous_Integration_and_Continuous_Delivery_CI-CD_in_Microsoft_Fabric/readme1.md) | [🏠 Volver al Inicio (Readmedp-700.md)](../Readmedp-700.md)