# PL-300 Lab 03: Configuración de un Modelo Semántico en Power BI

Este repositorio contiene la evidencia y la guía paso a paso del laboratorio **03 - Configure a semantic model in Power BI** del curso **PL-300: Microsoft Power BI Data Analyst**.

---

## 📌 Resumen del Laboratorio

* **Duración estimada:** 45 minutos
* **Objetivo principal:** Desarrollar el modelo de datos en Power BI Desktop mediante la creación de relaciones, configuración de propiedades de tablas y columnas, creación de jerarquías, carpetas de presentación, medidas rápidas y manejo de relaciones muchos a muchos (*many-to-many*).
* **Archivo de inicio:** `03-Starter-Sales Analysis.pbix` (Extraído de `03-model-data.zip`)

---

## 📁 Estructura del Repositorio Sugerida

```text
├── README.md                      # Documentación y evidencia del laboratorio
├── pbix/
│   ├── 03-Starter-Sales Analysis.pbix
│   └── 03-Final-Sales Analysis.pbix  # Archivo final configurado
└── screenshots/                   # Capturas de pantalla como evidencia
    ├── 01-manage-relationships.png
    ├── 02-product-hierarchy.png
    ├── 03-region-data-category.png
    ├── 04-sales-properties.png
    ├── 05-hidden-keys.png
    ├── 06-profit-measures.png
    └── 07-many-to-many-model.png
```

---

## 🚀 Guía Paso a Paso con Puntos de Evidencia

### 1. Preparación del Entorno
1. Descargar el archivo de recursos desde el repositorio oficial de Microsoft Learning:  
   `https://github.com/MicrosoftLearning/PL-300-Microsoft-Power-BI-Data-Analyst/raw/Main/Allfiles/Labs/03-configure-semantic-model/03-model-data.zip`
2. Extraer el archivo `.zip` en la carpeta local (por ejemplo `Downloads\03-model-data`).
3. Abrir el archivo `03-Starter-Sales Analysis.pbix`.

---

### 2. Creación de Relaciones del Modelo (*Model Relationships*)

#### Tarea 1: Primera relación manual
1. En el panel **Datos (*Data*)**, expandir todas las tablas (clic derecho en área vacía > **Expandir todo** / *Expand All*).
2. Crear una visualización de tabla en el informe añadiendo `Product | Category` y `Sales | Sales`.
3. *Observación:* Todas las categorías muestran el mismo monto total de ventas debido a la falta de una relación.
4. Ir a la vista de **Modelo (*Model view*)** y seleccionar **Administrar relaciones (*Manage Relationships*)** > **+ Nueva relación (*New relationship*)**.
5. Configurar la relación:
   * **Desde la tabla (*From table*):** `Product` (`ProductKey`)
   * **Hacia la tabla (*To table*):** `Sales` (`ProductKey`)
   * **Cardinalidad:** Una a varias (*One to Many - 1:\**)
   * **Dirección de filtro cruzado:** Única (*Single*)
   * **Activa:** Sí
6. Volver a la vista de Informe y verificar que los montos de ventas ahora se filtran por categoría.

> 📷 **Evidencia 1 (`screenshots/01-manage-relationships.png`):** Captura de la vista de modelo mostrando la línea de relación entre `Product` y `Sales`.

#### Tarea 2: Creación de relaciones mediante arrastrar y soltar
1. En la vista de modelo, arrastrar el campo `ResellerKey` de la tabla `Reseller` sobre `ResellerKey` en la tabla `Sales`.
2. Repetir el proceso para:
   * `Region | SalesTerritoryKey` $\rightarrow$ `Sales | SalesTerritoryKey`
   * `Salesperson | EmployeeKey` $\rightarrow$ `Sales | EmployeeKey`
3. Organizar el diagrama en estructura de **estrella** con la tabla de hechos `Sales` en el centro.

---

### 3. Configuración de Tablas y Jerarquías

#### Tarea 3: Tabla `Product` (Jerarquía y Carpeta de Presentación)
1. En la tabla `Product`, hacer clic derecho en `Category` > **Crear jerarquía (*Create hierarchy*)**.
2. Renombrar la jerarquía a **`Products`**.
3. Añadir los niveles `Subcategory` y `Product`. Hacer clic en **Aplicar cambios de nivel (*Apply Level Changes*)**.
4. Seleccionar los campos `Background Color Format` y `Font Color Format` (usando `Ctrl`).
5. En el panel de propiedades, establecer **Carpeta de presentación (*Display Folder*)** en `Formatting`.

> 📷 **Evidencia 2 (`screenshots/02-product-hierarchy.png`):** Captura del panel de datos mostrando la jerarquía `Products` y la carpeta `Formatting`.

#### Tarea 4: Tabla `Region` (Jerarquía y Categoría de Datos)
1. Crear la jerarquía **`Regions`**: `Group` $\rightarrow$ `Country` $\rightarrow$ `Region`.
2. Seleccionar la columna `Country` (no el nivel de jerarquía).
3. En **Propiedades > Avanzado > Categoría de datos (*Data Category*)**, seleccionar **País o región (*Country/Region*)**.

#### Tarea 5: Tabla `Reseller` (Jerarquías múltiples y Geo-categorización)
1. Crear la jerarquía **`Resellers`**: `Business Type` $\rightarrow$ `Reseller`.
2. Crear la jerarquía **`Geography`**: `Country-Region` $\rightarrow$ `State-Province` $\rightarrow$ `City` $\rightarrow$ `Reseller`.
3. Asignar categorías de datos a las columnas individuales:
   * `Country-Region` $\rightarrow$ *Country/Region*
   * `State-Province` $\rightarrow$ *State or Province*
   * `City` $\rightarrow$ *City*

---

### 4. Formato, Descripción y Ocultación Masiva de Campos

#### Tarea 6: Tabla `Sales`
1. En la columna `Cost`, agregar la descripción: `Based on standard cost`.
2. En `Quantity`, activar **Separador de millares (*Thousands Separator*)** = *Sí*.
3. En `Unit Price`, ajustar a **2 decimales** y cambiar **Resumir por (*Summarize By*)** a **Promedio (*Average*)**.

#### Tarea 7: Ocultar columnas clave en masa
1. Seleccionar con `Ctrl` las 13 columnas de claves técnicas en el modelo:
   * `ProductKey` (`Product`, `Sales`)
   * `SalesTerritoryKey` (`Region`, `Sales`, `SalespersonRegion`)
   * `ResellerKey` (`Reseller`, `Sales`)
   * `EmployeeKey` (`Sales`, `Salesperson`, `SalespersonRegion`)
   * `EmployeeID` (`Salesperson`, `Targets`)
   * `SalesOrderNumber` (`Sales`)
   * `UPN` (`Salesperson`)
2. En Propiedades, cambiar **Está oculto (*Is Hidden*)** a **Sí (*Yes*)**.
3. Formatear las columnas de moneda (`Standard Cost`, `Cost`, `Sales`) a **0 decimales**.

---

### 5. Configuración de Inteligencia de Tiempo y Medidas Rápidas

#### Tarea 8: Desactivar Fecha/Hora Automática
1. Ir a **Archivo > Opciones y configuración > Opciones**.
2. En **Archivo actual > Carga de datos > Inteligencia de tiempo**, desmarcar **Fecha/hora automática (*Auto Date/Time*)**.

#### Tarea 9: Creación de Medidas Rápidas (*Quick Measures*)
1. En la tabla `Sales`, crear nueva medida rápida:
   * **Operación:** Resta (*Subtraction*)
   * **Valor base:** `Sales | Sales`
   * **Valor a restar:** `Sales | Cost`
   * **Nombre:** `Profit`
2. Crear segunda medida rápida:
   * **Operación:** División (*Division*)
   * **Numerador:** `Sales | Profit`
   * **Denominador:** `Sales | Sales`
   * **Nombre:** `Profit Margin`
   * **Formato:** Porcentaje (`%`), 2 decimales.

---

### 6. Relación Muchos a Muchos (*Many-to-Many*) y Tabla de Objetivos

#### Tarea 10: Configurar la tabla puente `SalespersonRegion`
1. Insertar la tabla `SalespersonRegion` como puente entre `Region` y `Salesperson`.
2. Crear relaciones:
   * `Salesperson [EmployeeKey]` (1) $\rightarrow$ `SalespersonRegion [EmployeeKey]` (*)
   * `Region [SalesTerritoryKey]` (1) $\rightarrow$ `SalespersonRegion [SalesTerritoryKey]` (*)
3. Editar la relación entre `Region` y `SalespersonRegion`:
   * **Dirección de filtro cruzado:** Ambos (*Both*)
   * Activar la opción *Aplicar filtro de seguridad en ambas direcciones*.
4. Editar la relación entre `Salesperson` y `Sales` y cambiar a **Inactiva** (marcar como línea punteada).
5. Renombrar la tabla `Salesperson` a **`Salesperson (Performance)`**.
6. Crear relación entre `Salesperson (Performance) [EmployeeID]` (1) $\rightarrow$ `Targets [EmployeeID]` (*).

---

## 📊 Lista de Verificación de Resultados (*Verification Checklist*)

- [x] La tabla visual en Informe muestra ventas filtradas correctamente por `Category`.
- [x] Las jerarquías `Products`, `Regions`, `Resellers` y `Geography` están creadas y funcionales.
- [x] Las claves primarias/foráneas están ocultas en el panel de Datos.
- [x] Las medidas `Profit` y `Profit Margin` calculan los valores correctamente.
- [x] La relación de rendimiento de vendedores utiliza la tabla puente `SalespersonRegion` de forma activa.
- [x] El archivo final está guardado como `03-Final-Sales Analysis.pbix`.

---
*Documentación generada para el repositorio de evidencias PL-300.*
