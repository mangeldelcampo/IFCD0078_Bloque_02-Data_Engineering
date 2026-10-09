# Lab 04: Configure a Semantic Model in Power BI (PL-300)

Este repositorio contiene la guía de laboratorio, arquitectura de datos y plantilla de evidencias para el **Lab 04: Configure a Semantic Model in Power BI** del curso **PL-300: Microsoft Power BI Data Analyst**.

---

## 📑 Tabla de Contenidos
* [🎯 Objetivo y Lógica de Negocio](#-objetivo-y-lógica-de-negocio)
    * [Bloque 1: Arquitectura de Relaciones y Esquema en Estrella](#bloque-1-arquitectura-de-relaciones-y-esquema-en-estrella)
    * [Bloque 2: Ergonomía del Modelo y Navegación Jerárquica](#bloque-2-ergonomía-del-modelo-y-navegación-jerárquica)
    * [Bloque 3: Optimización de Tipos, Formatos y Ocultación Técnica](#bloque-3-optimización-de-tipos-formatos-y-ocultación-técnica)
    * [Bloque 4: Capa de Métricas de Rentabilidad (Medidas Rápidas)](#bloque-4-capa-de-métricas-de-rentabilidad-medidas-rápidas)
    * [Bloque 5: Resolución de Relaciones Muchos a Muchos y Rendimiento](#bloque-5-resolución-de-relaciones-muchos-a-muchos-y-rendimiento)
* [📁 Estructura del Repositorio Recomendada](#-estructura-del-repositorio-recomendada)
* [🛠️ Tecnologías y Entorno](#️-tecnologías-y-entorno)
* [📝 Guía Paso a Paso del Laboratorio](#-guía-paso-a-paso-del-laboratorio)
* [📸 Arquitectura Final y Evidencias](#-arquitectura-final-y-evidencias)
* [🧠 Decisiones Técnicas Destacadas (Key Takeaways)](#-decisiones-técnicas-destacadas-key-takeaways)
* [✅ Lista de Verificación (Checklist)](#-lista-de-verificación-checklist)
* [🧭 Navegación entre Laboratorios](#-navegación-entre-laboratorios)

---

## 🎯 Objetivo y Lógica de Negocio
El propósito de este laboratorio consiste en transformar las consultas aisladas e importadas durante la fase ETL en un **modelo semántico dimensional (esquema en estrella)** optimizado para el autoservicio de análisis (*Self-Service BI*). Se definen caminos claros de propagación de filtros, se estructuran jerarquías de agregación y se resuelve la ambigüedad en relaciones complejas para garantizar la integridad métrica.

### Bloque 1: Arquitectura de Relaciones y Esquema en Estrella
* **Objetivo:** Garantizar la propagación unívoca del contexto de filtro desde las entidades maestras hacia la tabla de transacciones de ventas.
* **Lógica de negocio:** Conexión de la tabla transaccional `Sales` con sus dimensiones satélite (`Product`, `Reseller`, `Region`, `Salesperson`) mediante relaciones de cardinalidad uno a varios ($1:*$) y dirección de filtro cruzado única (*Single*). Esto garantiza que los filtros fluyan desde el extremo dimensional hacia el granular sin introducir ciclos cerrados ni duplicidades.

### Bloque 2: Ergonomía del Modelo y Navegación Jerárquica
* **Objetivo:** Habilitar experiencias de exploración multinivel (*drill-down*) y simplificar el catálogo de datos.
* **Lógica de negocio:**
    * **Jerarquías analíticas:** Modelado de jerarquías en `Product` (`Products`: Category $\rightarrow$ Subcategory $\rightarrow$ Product) y territorios en `Region` y `Reseller` (`Regions` y `Geography`), optimizando el análisis desde la dirección corporativa hasta la tienda o código local.
    * **Carpetas de visualización (*Display Folders*):** Aislamiento de atributos auxiliares (como `Background Color Format` y `Font Color Format` dentro de la carpeta `Formatting`) para evitar ruido analítico al crear informes.
    * **Categorización geográfica:** Declaración explícita de metadatos espaciales (`Country/Region`, `State or Province`, `City`) para habilitar el motor de geolocalización en mapas interactivos de Power BI.

### Bloque 3: Optimización de Tipos, Formatos y Ocultación Técnica
* **Objetivo:** Prevenir agregaciones incorrectas de negocio y ocultar claves del pipeline relacional.
* **Lógica de negocio:**
    * **Ocultación en masa:** Ocultar 13 claves técnicas foráneas y subrogadas (`ProductKey`, `EmployeeKey`, etc.) para impedir que se utilicen como métricas sumables en los visuales.
    * **Agregaciones por defecto:** Configuración de `Unit Price` en modo promedio (*Average*) para evitar la suma inconsistente de tipos unitarios, así como la normalización de posiciones decimales a cero en costes y ventas consolidadas.
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
    * **Resolución de ambigüedad:** Desactivación deliberada de la relación directa `Salesperson` $\rightarrow$ `Sales`, forzando a que la propagación del contexto de filtro fluya exclusivamente a través de la asignación territorial para medir el desempeño integral de cuotas (`Targets`).

---

## 📁 Estructura del Repositorio Recomendada

```text
.
├── README.md                      # Documentación del laboratorio
├── pbix/
│   ├── 04-Starter-Sales Analysis.pbix
│   └── 04-Final-Sales Analysis.pbix  # Modelo semántico configurado
└── assets/                        # Capturas de pantalla de evidencia
    ├── 01-model-view-star-schema.png
    └── 02-report-view-performance-matrix.png
```

---

## 🛠️ Tecnologías y Entorno
* **Herramienta:** Microsoft Power BI Desktop
* **Origen de datos:** SQL Server (`AdventureWorksDW2020`) / Archivos del repositorio PL-300
* **Enfoque de diseño:** Modelado dimensional tabular, Kimball star-schema, desambiguación de relaciones cruzadas

---

## 📝 Guía Paso a Paso del Laboratorio

### Paso 1: Configuración Inicial del Archivo
1. Abrir el archivo inicial `04-Starter-Sales Analysis.pbix` en Power BI Desktop.
2. Desactivar **Inteligencia de tiempo automática**:
   * Ir a **Archivo > Opciones y configuración > Opciones**.
   * En **Archivo actual > Carga de datos**, desmarcar **Fecha/hora automática**.

### Paso 2: Creación de Relaciones en el Modelo
1. Abrir la vista de **Modelo (*Model View*)**.
2. Crear las siguientes relaciones $1:*$ con dirección de filtro cruzado **Única (*Single*)**:
   * `Product [ProductKey]` (1) $\rightarrow$ `Sales [ProductKey]` (*)
   * `Reseller [ResellerKey]` (1) $\rightarrow$ `Sales [ResellerKey]` (*)
   * `Region [SalesTerritoryKey]` (1) $\rightarrow$ `Sales [SalesTerritoryKey]` (*)
   * `Salesperson [EmployeeKey]` (1) $\rightarrow$ `Sales [EmployeeKey]` (*)
3. Organizar las tablas en la vista de diagrama en forma de **Esquema en Estrella (*Star Schema*)**, colocando `Sales` en el centro.

### Paso 3: Configuración de Jerarquías y Carpetas de Presentación
1. **Tabla `Product`**:
   * Crear la jerarquía **`Products`**: `Category` $\rightarrow$ `Subcategory` $\rightarrow$ `Product`.
   * Seleccionar `Background Color Format` y `Font Color Format` y asignar la **Carpeta de presentación**: `Formatting`.
2. **Tabla `Region`**:
   * Crear la jerarquía **`Regions`**: `Group` $\rightarrow$ `Country` $\rightarrow$ `Region`.
   * Asignar a `Country` la **Categoría de datos**: *Country/Region*.
3. **Tabla `Reseller`**:
   * Crear la jerarquía **`Resellers`**: `Business Type` $\rightarrow$ `Reseller`.
   * Crear la jerarquía **`Geography`**: `Country-Region` $\rightarrow$ `State-Province` $\rightarrow$ `City` $\rightarrow$ `Reseller`.
   * Categorizar los campos geográficos (`Country/Region`, `State or Province`, `City`).

### Paso 4: Propiedades de Campos y Ocultación Técnica
1. En la tabla `Sales`:
   * `Cost`: Añadir descripción `Based on standard cost`.
   * `Quantity`: Activar **Separador de millares**.
   * `Unit Price`: Asignar formato de **2 decimales** y **Resumir por**: *Promedio (Average)*.
2. Seleccionar en masa las 13 claves técnicas en las tablas (`ProductKey`, `EmployeeKey`, `SalesTerritoryKey`, `ResellerKey`, `EmployeeID`, `SalesOrderNumber`, `UPN`) y establecer **Está oculto (*Is Hidden*)** en **Sí**.

### Paso 5: Creación de Medidas Rápidas
1. En `Sales`, crear la medida rápida **`Profit`**: `Sales` - `Cost`.
2. En `Sales`, crear la medida rápida **`Profit Margin`**: `Profit` / `Sales` (Formato: **Porcentaje**, **2 decimales**).

### Paso 6: Configuración del Modelo Muchos a Muchos
1. Incorporar la tabla puente `SalespersonRegion`.
2. Configurar relaciones:
   * `Salesperson [EmployeeKey]` (1) $\rightarrow$ `SalespersonRegion [EmployeeKey]` (*)
   * `Region [SalesTerritoryKey]` (1) $\rightarrow$ `SalespersonRegion [SalesTerritoryKey]` (*)
3. En la relación `Region` $\rightarrow$ `SalespersonRegion`, cambiar la dirección de filtro cruzado a **Ambos (*Both*)** y activar la regla de seguridad bidireccional.
4. Desactivar (marcar como **Inactiva**) la relación directa entre `Salesperson` y `Sales`.
5. Renombrar la tabla `Salesperson` a **`Salesperson (Performance)`**.
6. Conectar `Salesperson (Performance) [EmployeeID]` (1) $\rightarrow$ `Targets [EmployeeID]` (*).

---

## 📸 Arquitectura Final y Evidencias

*Inserta aquí las capturas de pantalla de tus resultados:*
* ![Diagrama del Modelo](./assets/01-model-view-star-schema.png)
* ![Matriz de Métricas y Jerarquías](./assets/02-report-view-performance-matrix.png)

---

## 🧠 Decisiones Técnicas Destacadas (Key Takeaways)
1. **Filtro cruzado bidireccional en tablas puente:** Se aplicó dirección de filtro cruzado en ambas direcciones (*Both*) junto con la regla de seguridad entre `Region` y `SalespersonRegion` para permitir que el filtro de vendedores alcance las ventas regionales.
2. **Relación inactiva intencionada:** La relación directa `Salesperson` $\rightarrow$ `Sales` se marca como inactiva para evitar caminos alternativos (*loops*) que el motor de Analysis Services resolvería de forma impredecible por distancia de saltos.

---

## ✅ Lista de Verificación (Checklist)

- [x] Esquema en estrella configurado con `Sales` como tabla de hechos central.
- [x] Desactivada la opción de *Auto Date/Time*.
- [x] Jerarquías `Products`, `Regions`, `Resellers` y `Geography` creadas y verificadas.
- [x] Carpetas de presentación (*Formatting*) aplicadas a campos de diseño.
- [x] Ocultas las 13 claves técnicas del modelo.
- [x] Medidas `Profit` y `Profit Margin` creadas con formato adecuado.
- [x] Implementada la tabla puente `SalespersonRegion` con filtro bidireccional activado.
- [x] Desactivada la relación directa entre vendedores y ventas.
- [x] Capturas guardadas en la carpeta `./assets/`.

---

## 🧭 Navegación entre Laboratorios

| ⬅️ Laboratorio Anterior | 🏠 Índice de Laboratorios | ➡️ Siguiente Laboratorio |
| :--- | :---: | :--- |
| [Lab 03: Load and Transform Data in Power BI](../lab-03-load-transform-data/README.md) | [📚 Índice General PL-300](../../README.md) | [Lab 05: Create DAX Calculations in Power BI Desktop](../lab-05-create-dax-calculations/README.md) |

---
*Documentación estructurada a partir del archivo de fuente `readme4.md` para el repositorio de evidencias PL-300.*
