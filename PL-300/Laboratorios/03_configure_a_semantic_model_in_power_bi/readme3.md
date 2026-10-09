# Lab 04: Configure a Semantic Model in Power BI (PL-300)

## 📑 Tabla de Contenidos
- [🎯 Objetivo y Lógica de Negocio](#-objetivo-y-lógica-de-negocio)
  - [Bloque 1: Arquitectura de Relaciones y Esquema en Estrella](#bloque-1-arquitectura-de-relaciones-y-esquema-en-estrella)
  - [Bloque 2: Ergonomía del Modelo y Navegación Jerárquica](#bloque-2-ergonomía-del-modelo-y-navegación-jerárquica)
  - [Bloque 3: Optimización de Tipos, Formatos y Ocultación Técnica](#bloque-3-optimización-de-tipos-formatos-y-ocultación-técnica)
  - [Bloque 4: Capa de Métricas de Rentabilidad (Medidas Rápidas)](#bloque-4-capa-de-métricas-de-rentabilidad-medidas-rápidas)
  - [Bloque 5: Resolución de Relaciones Muchos a Muchos y Rendimiento](#bloque-5-resolución-de-relaciones-muchos-a-muchos-y-rendimiento)
- [🛠️ Tecnologías y Entorno](#️-tecnologías-y-entorno)
- [📸 Arquitectura Final y Evidencias](#-arquitectura-final-y-evidencias)
- [🧠 Decisiones Técnicas Destacadas (Key Takeaways)](#-decisiones-técnicas-destacadas-key-takeaways)
- [🧭 Navegación entre Laboratorios](#-navegación-entre-laboratorios)

---

## 🎯 Objetivo y Lógica de Negocio

El propósito de este laboratorio consiste en transformar las consultas aisladas e importadas durante la fase ETL en un **modelo semántico dimensional (esquema en estrella)** optimizado para el autoservicio de análisis (*Self-Service BI*). Se definen caminos claros de propagación de filtros, se estructuran jerarquías de agregación y se resuelve la ambigüedad en relaciones complejas para garantizar la integridad métrica.

### Bloque 1: Arquitectura de Relaciones y Esquema en Estrella
* **Objetivo:** Garantizar la propagación unívoca del contexto de filtro desde las entidades maestras hacia la tabla de transacciones de ventas.
* **Lógica de negocio:** Conexión de la tabla transaccional `Sales` con sus dimensiones satélite (`Product`, `Reseller`, `Region`, `Salesperson`) mediante relaciones de cardinalidad uno a varios (`1:*`) y dirección de filtro cruzado única (*Single*). Esto garantiza que los filtros fluyan desde el extremo dimensional hacia el granular sin introducir ciclos cerrados ni duplicidades.

### Bloque 2: Ergonomía del Modelo y Navegación Jerárquica
* **Objetivo:** Habilitar experiencias de exploración multinivel (*drill-down*) y simplificar el catálogo de datos.
* **Lógica de negocio:**
  * **Jerarquías analíticas:** Modelado de jerarquías en `Product` (`Products`: Category ➔ Subcategory ➔ Product) y territorios en `Region` y `Reseller` (`Regions` y `Geography`), optimizando el análisis desde la dirección corporativa hasta la tienda o código local.
  * **Carpetas de visualización (*Display Folders*):** Aislamiento de atributos auxiliares (como `Background Color Format` y `Font Color Format` dentro de la carpeta `Formatting`) para evitar ruido analítico al crear informes.
  * **Categorización geográfica:** Declaración explícita de metadatos espaciales (`Country/Region`, `State or Province`, `City`) para habilitar el motor de geolocalización en mapas interactivos de Power BI.

### Bloque 3: Optimización de Tipos, Formatos y Ocultación Técnica
* **Objetivo:** Prevenir agregaciones incorrectas de negocio y ocultar claves del pipeline relacional.
* **Lógica de negocio:**
  * **Ocultación en masa:** Ocultar 13 claves técnicas foráneas y subrogadas (`ProductKey`, `EmployeeKey`, etc.) para impedir que se utilicen como métricas sumables en los visuales.
  * **Agregaciones por defecto:** Configuración de `Unit Price` en modo promedio (`Average`) para evitar la suma inconsistente de tipos unitarios, así como la normalización de posiciones decimales a cero en costes y ventas consolidadas.
  * **Supresión de Time Intelligence automático:** Desactivación global de la opción de fecha/hora automática en el archivo, evitando tablas ocultas que asumen año natural frente al calendario fiscal corporativo (inicio en julio) de Adventure Works.

### Bloque 4: Capa de Métricas de Rentabilidad (Medidas Rápidas)
* **Objetivo:** Modelar indicadores financieros dinámicos evaluados bajo cualquier contexto de filtro.
* **Lógica de negocio:** Creación de las métricas clave de margen bruto:
  * $\text{Profit} = \text{Sales} - \text{Cost}$
  * $\text{Profit Margin} = \frac{\text{Profit}}{\text{Sales}}$ (formateada como porcentaje con dos decimales)

### Bloque 5: Resolución de Relaciones Muchos a Muchos y Rendimiento
* **Objetivo:** Resolver el desacoplamiento entre las ventas directas y el rendimiento global por áreas comerciales.
* **Lógica de negocio:** 
  * Un comercial puede tener asignados múltiples territorios y un territorio contar con varios representantes. Se implementa una relación *many-to-many* utilizando `SalespersonRegion` como tabla puente (*bridge table*).
  * **Resolución de ambigüedad:** Desactivación deliberada de la relación directa `Salesperson` ➔ `Sales`, forzando a que la propagación del contexto de filtro fluya exclusivamente a través de la asignación territorial para medir el desempeño integral de cuotas (`Targets`).

---

## 🛠️ Tecnologías y Entorno
* **Herramienta:** Microsoft Power BI Desktop
* **Origen de datos:** SQL Server (`AdventureWorksDW2020`) y repositorios web de recursos PL-300
* **Enfoque de diseño:** Modelado dimensional tabular, Kimball star-schema, desambiguación de relaciones cruzadas

---

## 📸 Arquitectura Final y Evidencias
*Inserta aquí las capturas de pantalla de tus resultados:*
* `![Diagrama del Modelo](./assets/01-model-view-star-schema.png)`
* `![Matriz de Métricas y Jerarquías](./assets/02-report-view-performance-matrix.png)`

---

## 🧠 Decisiones Técnicas Destacadas (Key Takeaways)
1. **Filtro cruzado bidireccional en tablas puente:** Se aplicó dirección de filtro cruzado en ambas direcciones (*Both*) junto con la regla de seguridad entre `Region` y `SalespersonRegion` para permitir que el filtro de vendedores alcance las ventas regionales.
2. **Relación inactiva intencionada:** La relación directa `Salesperson` ➔ `Sales` se marca como inactiva para evitar caminos alternativos (*loops*) que el motor de Analysis Services resolvería de forma impredecible por distancia de saltos.

---

## 🧭 Navegación entre Laboratorios

| ⬅️ Laboratorio Anterior | 🏠 Índice de Laboratorios | ➡️ Siguiente Laboratorio |
| :--- | :---: | :---: |
| [Lab 02: Clean transform and load data in PowerBi](../02_clean_transform_and_load_data_in_power_bi/) | [📚 Índice General laboratorios PL-300](../readmeLab_PL-300.md) | [Lab 04: Create DAX Calculations in Power BI Desktop](../04_create_dax_calculations_in_semantic_models/) |
