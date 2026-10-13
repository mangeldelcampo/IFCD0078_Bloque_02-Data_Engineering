### Lab 04: Create DAX Calculations in Power BI (PL-300)

Este repositorio contiene la gu铆a de laboratorio, arquitectura de datos y plantilla de evidencias para el **Lab 04: Create DAX Calculations in Power BI** del curso **PL-300: Microsoft Power BI Data Analyst**.

---

#### 馃搼 Tabla de Contenidos
* [馃幆 Objetivo y L贸gica de Negocio](#-objetivo-y-l贸gica-de-negocio)
    * [Bloque 1: Tablas Calculadas y Relaciones Directas](#bloque-1-tablas-calculadas-y-relaciones-directas)
    * [Bloque 2: Columnas Calculadas, Ordenaci贸n Personalizada y Jerarqu铆a Fiscal](#bloque-2-columnas-calculadas-ordenaci贸n-personalizada-y-jerarqu铆a-fiscal)
    * [Bloque 3: Capa de Medidas Simples y Ergonom铆a del Modelo](#bloque-3-capa-de-medidas-simples-y-ergonom铆a-del-modelo)
    * [Bloque 4: Medidas Avanzadas y Control de Contexto de Evaluaci贸n](#bloque-4-medidas-avanzadas-y-control-de-contexto-de-evaluaci贸n)
* [馃搧 Estructura del Repositorio Recomendada](#-estructura-del-repositorio-recomendada)
* [馃洜锔?Tecnolog铆as y Entorno](#锔?tecnolog铆as-y-entorno)
* [馃洜锔?Realizaci贸n del Laboratorio](#锔?realizaci贸n-del-laboratorio)
* [馃摳 Arquitectura Final y Evidencias](#-arquitectura-final-y-evidencias)
* [馃 Decisiones T茅cnicas Destacadas (Key Takeaways)](#-decisiones-t茅cnicas-destacadas-key-takeaways)
* [鉁?Lista de Verificaci贸n (Checklist)](#-lista-de-verificaci贸n-checklist)
* [馃Л Navegaci贸n entre Laboratorios](#-navegaci贸n-entre-laboratorios)

---

#### 馃幆 Objetivo y L贸gica de Negocio
El prop贸sito de este laboratorio consiste en enriquecer el modelo sem谩ntico dimensional mediante el lenguaje **DAX (Data Analysis Expressions)**. Se crean tablas calculadas, columnas calculadas para soportar la inteligencia de tiempo fiscal, y medidas expl铆citas complejas que controlan la agregaci贸n en totales y organizan el cat谩logo m茅trico corporativo.

##### Bloque 1: Tablas Calculadas y Relaciones Directas
* **Objetivo:** Proporcionar perspectivas de an谩lisis independientes para los vendedores (ventas directas vs. rendimiento territorial) y construir una dimensi贸n de calendario en memoria.
* **L贸gica de negocio:**
    * **Tabla calculada `Salesperson`:** Duplicaci贸n mediante DAX de `Salesperson (Performance)` para conectarla directamente con `Sales` mediante relaci贸n $1:*$, permitiendo evaluar ventas por comercial individual.
    * **Tabla calculada `Date`:** Generaci贸n autom谩tica del calendario continuo con `CALENDARAUTO(6)`, ajustando el cierre del a帽o fiscal a junio para alinearse con el ejercicio financiero de Adventure Works.

##### Bloque 2: Columnas Calculadas, Ordenaci贸n Personalizada y Jerarqu铆a Fiscal
* **Objetivo:** Habilitar el filtrado temporal por a帽o fiscal, trimestre y mes, solucionando problemas de ordenaci贸n alfab茅tica en visualizaciones.
* **L贸gica de negocio:**
    * **C谩lculo de a帽o fiscal (`Year`):** Expresi贸n DAX que suma 1 al a帽o natural para fechas posteriores a junio (`FY2018`, `FY2019`, etc.).
    * **Clave de ordenaci贸n (`MonthKey`):** Generaci贸n de la columna num茅rica `(YEAR * 100) + MONTH` y aplicaci贸n de la propiedad **Ordenar por columna** (*Sort by Column*) sobre el campo `Month` para garantizar el orden cronol贸gico fiscal.
    * **Jerarqu铆a `Fiscal` y Marcado como Tabla de Fechas:** Estructuraci贸n de la jerarqu铆a `Year` $\rightarrow$ `Quarter` $\rightarrow$ `Month` y declaraci贸n oficial de *Mark as Date Table* para asegurar el correcto funcionamiento de las funciones Time Intelligence.

##### Bloque 3: Capa de Medidas Simples y Ergonom铆a del Modelo
* **Objetivo:** Prevenir agregaciones impl铆citas incorrectas en informes (como la suma de precios unitarios) y centralizar los c谩lculos clave.
* **L贸gica de negocio:**
    * **Ocultaci贸n de columnas num茅ricas:** Ocultaci贸n de `Unit Price` para forzar a los autores de informes a utilizar medidas DAX expl铆citas.
    * **Medidas expl铆citas de precios y recuentos:** Creaci贸n de `Avg Price`, `Median Price`, `Min Price`, `Max Price`, `Orders` (`DISTINCTCOUNT`) y `Order Lines` (`COUNTROWS`).
    * **Organizaci贸n en carpetas de presentaci贸n:** Agrupaci贸n visual en carpetas `Pricing` y `Counts` con formato estandarizado (2 decimales y separador de miles).

##### Bloque 4: Medidas Avanzadas y Control de Contexto de Evaluaci贸n
* **Objetivo:** Evitar la suma inconsistente de objetivos de ventas en filas de total general y calcular m茅tricas de desviaci贸n de cuotas.
* **L贸gica de negocio:**
    * **Control de total con `HASONEVALUE`:** Evaluaci贸n del contexto mediante `HASONEVALUE('Salesperson (Performance)'[Salesperson])` para devolver el objetivo de cuota s贸lo cuando se filtra un 煤nico comercial, devolviendo `BLANK` en las filas de total.
    * **Medidas de desviaci贸n:** Implementaci贸n de `Variance = [Sum of Sales] - [Target]` y `Variance Margin = DIVIDE([Variance], [Target])`.
    * **Conversi贸n a tabla de medidas:** Al ocultar `TargetAmount`, la tabla `Targets` se convierte en una tabla de medidas pura posicion谩ndose en la parte superior del panel de datos.

---

#### 馃搧 Estructura del Repositorio Recomendada
```text
.
鈹溾攢鈹€ README.md                      # Documentaci贸n del laboratorio
鈹溾攢鈹€ pbix/
鈹?  鈹溾攢鈹€ 04-Starter-Sales Analysis.pbix
鈹?  鈹斺攢鈹€ 04-Final-Sales Analysis.pbix  # Modelo con c谩lculos DAX
鈹斺攢鈹€ imagenes/                      # Capturas de pantalla de evidencia
    鈹溾攢鈹€ lab4_img01.png
    鈹溾攢鈹€ lab4_img02.png
    鈹斺攢鈹€ ...
```

---

#### 馃洜锔?Tecnolog铆as y Entorno
* **Herramienta:** Microsoft Power BI Desktop
* **Lenguaje:** DAX (Data Analysis Expressions)
* **Funciones clave:** `CALENDARAUTO`, `YEAR`, `MONTH`, `AVERAGE`, `MEDIAN`, `MIN`, `MAX`, `DISTINCTCOUNT`, `COUNTROWS`, `HASONEVALUE`, `IF`, `DIVIDE`
* **Enfoque de dise帽o:** Tablas calculadas, medidas expl铆citas, carpetas de presentaci贸n, ordenaci贸n por columna clave.

---

#### 馃洜锔?Realizaci贸n del Laboratorio

> 馃搼 **脥ndice Interno de Ejecuci贸n del Laboratorio (Bloques y Secciones)**
>
> * 馃搶 [**Historia del laboratorio**](#historia-del-laboratorio)
> * 馃搶 [**Empieza**](#empieza)
> * 馃З [**Bloque 1: Tablas Calculadas y Relaciones Directas**](#bloque-1-tablas-calculadas-y-relaciones-directas)
>   * 馃敼 [Crea la tabla calculada para Salesperson](#crea-la-tabla-calculada-para-salesperson)
>   * 馃敼 [Crea la tabla de fechas](#crea-la-tabla-de-fechas)
> * 馃З [**Bloque 2: Columnas Calculadas, Ordenaci贸n Personalizada y Jerarqu铆a Fiscal**](#bloque-2-columnas-calculadas-ordenaci贸n-personalizada-y-jerarqu铆a-fiscal)
>   * 馃敼 [Crear columnas calculadas](#crear-columnas-calculadas)
>   * 馃敼 [Completar la tabla de fechas](#completar-la-tabla-de-fechas)
>   * 馃敼 [Marca la tabla de fechas](#marca-la-tabla-de-fechas)
> * 馃З [**Bloque 3: Capa de Medidas Simples y Ergonom铆a del Modelo**](#bloque-3-capa-de-medidas-simples-y-ergonom铆a-del-modelo)
>   * 馃敼 [Crea medidas simples](#crea-medidas-simples)
> * 馃З [**Bloque 4: Medidas Avanzadas y Control de Contexto de Evaluaci贸n**](#bloque-4-medidas-avanzadas-y-control-de-contexto-de-evaluaci贸n)
>   * 馃敼 [Crear medidas adicionales](#crear-medidas-adicionales)
>   * 馃敼 [Laboratorio completo](#laboratorio-completo)

---

#### **Historia del laboratorio**

En este laboratorio, crear谩s tablas calculadas, columnas calculadas y medidas sencillas utilizando Expresiones de An谩lisis de Datos (DAX).

En este laboratorio, aprendes c贸mo:
* Crear tablas calculadas.
* Crear columnas calculadas.
* Crear medidas.

**Este laboratorio deber铆a durar aproximadamente 45 minutos.**

#### **Empieza**

Para completar este ejercicio, descarga los recursos del laboratorio desde el repositorio oficial de Microsoft Learning para PL-300.

Extrae los archivos en la carpeta de trabajo del laboratorio.

Abre el archivo **04-Starter-Sales Analysis.pbix**.

> [!NOTE]
> Puede que veas un di谩logo de inicio de sesi贸n mientras carga el archivo. Selecciona **Cancelar** para cerrar el cuadro de inicio de sesi贸n. Cierra cualquier otra ventana informativa. Selecciona **Solicitar m谩s tarde**, si se le pide aplicar cambios.
---

### Bloque 1: Tablas Calculadas y Relaciones Directas

**Secciones del laboratorio incluidas:**
* Crea la tabla calculada para Salesperson (Pasos 1 al 10).
* Crea la tabla de fechas (Pasos 1 al 4).

**Puntos t茅cnicos a ejecutar:**
* Creaci贸n de la tabla calculada `Salesperson = 'Salesperson (Performance)'` en DAX para permitir el an谩lisis directo vendedor-ventas.
* Conexi贸n de `Salesperson [EmployeeKey]` a `Sales [EmployeeKey]` mediante relaci贸n $1:*$ activa y eliminaci贸n de la relaci贸n inactiva antigua.
* Ocultaci贸n de claves t茅cnicas (`EmployeeID`, `EmployeeKey`, `UPN`) y configuraci贸n de descripciones de tablas en el panel de propiedades.
* Generaci贸n de la tabla de calendario calculada `Date = CALENDARAUTO(6)` para abarcar a帽os fiscales completos finalizados en junio.

#### Crea la tabla calculada para Salesperson

En esta tarea, crear谩s la tabla calculada `Salesperson` (que tendr谩 una relaci贸n directa con la tabla `Sales`).

Una tabla calculada se crea introduciendo primero el nombre de la tabla, seguido del s铆mbolo de igual (`=`), seguido de una f贸rmula DAX que devuelve una tabla. El nombre de la tabla no puede existir ya en el modelo de datos.

1. En Power BI Desktop, en la vista de informes, en la cinta de **Modelado**, desde dentro del grupo de **C谩lculos**, selecciona **Nueva tabla**.

![lab4_img01](./imagenes/lab4_img01.png)

2. En la barra de f贸rmulas (que se abre justo debajo de la cinta cuando creas o editas c谩lculos), escribe el siguiente c贸digo DAX y pulsa **Enter**:

```dax
Salesperson = 'Salesperson (Performance)'
```

![lab4_img02](./imagenes/lab4_img02.png)

*Esta definici贸n de tabla crea una copia de la tabla `Salesperson (Performance)`. Solo copia los datos; sin embargo, propiedades del modelo como visibilidad, formato y otras no se copian.*

3. En el panel de **Datos**, observa que el icono de la nueva tabla tiene una calculadora adicional delante (que indica una tabla calculada).

![lab4_img03](./imagenes/lab4_img03.png)

4. Cambia a la **vista de modelo** y observa que la tabla `Salesperson` est谩 disponible.
5. Crea una relaci贸n entre la columna `Salesperson | EmployeeKey` y la columna `Sales | EmployeeKey`.
6. Haz clic derecho en la relaci贸n inactiva (l铆nea de puntos) entre las tablas `Salesperson (Performance)` y `Sales` y luego selecciona **Eliminar**. Cuando se le pida confirmar la eliminaci贸n, seleccione **S铆**.
7. En la tabla `Salesperson`, selecciona las siguientes columnas y oc煤ltalas (establece **Est谩 oculto** en **S铆**):
    * `EmployeeID`
    * `EmployeeKey`
    * `UPN`
8. En el diagrama del modelo, selecciona la tabla `Salesperson`.
9. En el panel de **Propiedades**, en el cuadro de **Descripci贸n**, escribe: `Vendedor relacionado con ventas`.
10. Para la tabla `Salesperson (Performance)`, establece la descripci贸n a: `Salesperson relacionado con region(es)`.

*El modelo de datos ahora ofrece dos alternativas al analizar a los vendedores. La tabla `Salesperson` permite analizar las ventas realizadas por un comercial, mientras que la tabla `Salesperson (Performance)` permite analizar las ventas realizadas en la(s) regi贸n(es) de ventas asignadas al comercial.*

#### Crea la tabla de fechas

En esta tarea, crear谩s la tabla `Date`.

1. Cambia a la **vista de tabla**. En la pesta帽a de la cinta de inicio, desde dentro del grupo de **C谩lculos**, selecciona **Nueva tabla**.

![lab4_img04](./imagenes/lab4_img04.png)

2. En la barra de f贸rmulas, introduce el siguiente DAX:

```dax
Date = CALENDARAUTO(6)
```

![lab4_img05](./imagenes/lab4_img05.png)

*La funci贸n `CALENDARAUTO` devuelve una tabla de una sola columna que incluye los valores de fechas. El comportamiento "auto" escanea todas las columnas de fecha del modelo de datos para determinar los valores de fecha m谩s antiguo y m谩s reciente almacenados en el modelo de datos. Luego crea una fila para cada fecha dentro de este rango, ampliando el rango en ambas direcciones para asegurar que se almacenen a帽os completos de datos. El argumento `6` indica que junio es el 煤ltimo mes del a帽o fiscal.*

3. F铆jate en la columna de valores de fecha que podr铆a estar formateada usando configuraciones regionales.

![lab4_img06](./imagenes/lab4_img06.png)

4. En la esquina inferior izquierda, en la barra de estado, observa las estad铆sticas de la tabla que confirman que se han generado 1,826 filas de datos, lo que representa datos de cinco a帽os completos.

---

### Bloque 2: Columnas Calculadas, Ordenaci贸n Personalizada y Jerarqu铆a Fiscal

**Secciones del laboratorio incluidas:**
* Crear columnas calculadas (Pasos 1 al 18).
* Completar la tabla de fechas (Pasos 1 al 7).
* Marca la tabla de fechas (Pasos 1 al 7).

**Puntos t茅cnicos a ejecutar:**
* Creaci贸n de las columnas calculadas `Year`, `Quarter`, `Month` y la clave num茅rica `MonthKey`.
* Correcci贸n del ordenamiento alfab茅tico en el visual aplicando **Ordenar por columna** (*Sort by Column*) sobre `Month` utilizando `MonthKey`.
* Creaci贸n de la jerarqu铆a fiscal `Fiscal` (`Year` $\rightarrow$ `Quarter` $\rightarrow$ `Month`) y ocultaci贸n de `MonthKey`.
* Marcado formal de la tabla como **Tabla de fechas** (*Mark as Date Table*) sobre `Date [Date]`.
* Vinculaci贸n de `Date [Date]` hacia `Sales [OrderDate]` y `Targets [TargetMonth]`, ocultando las claves originales.

#### Crear columnas calculadas

En esta tarea, a帽adir谩s m谩s columnas para permitir el filtrado y agrupar por diferentes periodos de tiempo. Tambi茅n crear谩s una columna calculada para controlar el orden de ordenaci贸n de otras columnas.

1. En la cinta contextual de **Herramientas de Tabla**, desde dentro del grupo de **C谩lculos**, selecciona **Nueva columna**.

![lab4_img07](./imagenes/lab4_img07.png)

2. En la barra de f贸rmulas, escribe la siguiente f贸rmula DAX y pulsa **Enter**:

```dax
Year = "FY" & YEAR('Date'[Date]) + IF(MONTH('Date'[Date]) > 6, 1)
```

![lab4_img08](./imagenes/lab4_img08.png)

*La f贸rmula utiliza el valor anual de la fecha pero a帽ade uno al valor del a帽o cuando el mes es posterior a junio (inicio del a帽o fiscal de Adventure Works).*

3. Crea las siguientes dos columnas calculadas para la tabla `Date`:

```dax
Quarter = 'Date'[Year] & " " & "Q" & IF(MONTH('Date'[Date]) <= 3, 3, IF(MONTH('Date'[Date]) <= 6, 4, IF(MONTH('Date'[Date]) <= 9, 1, 2)))
```

```dax
Month = FORMAT('Date'[Date], "yyyy MMM")
```

4. Verifica que se hayan a帽adido las nuevas columnas en la vista de tabla.

![lab4_img09](./imagenes/lab4_img09.png)

5. Para validar los c谩lculos, cambia a la **vista de informe**.
6. Para crear una nueva p谩gina de informe, selecciona el icono de m谩s (`+`) junto a la P谩gina 1.

![lab4_img10](./imagenes/lab4_img10.png)

7. Para a帽adir una matriz visual a la nueva p谩gina de informe, en el panel de **Visualizaciones**, selecciona el tipo de matriz visual.

![lab4_img11](./imagenes/lab4_img11.png)

8. En el panel de datos, desde dentro de la tabla `Date`, arrastra el campo `Year` al 谩rea de **Filas**.

![lab4_img12](./imagenes/lab4_img12.png)

9. Arrastra el campo `Month` hacia el 谩rea de las **Filas**, justo debajo del campo `Year`.
10. En la parte superior derecha del visual de la matriz, selecciona el icono de doble flecha bifurcada (que expandir谩 todos los a帽os un nivel hacia abajo).

![lab4_img13](./imagenes/lab4_img13.png)

11. F铆jate que los a帽os se expanden a meses, y que los meses est谩n ordenados alfab茅ticamente en lugar de cronol贸gicamente.

![lab4_img14](./imagenes/lab4_img14.png)

12. Para personalizar el orden de ordenaci贸n del campo `Month`, cambia a la **vista de tabla**.
13. A帽ade la columna `MonthKey` a la tabla `Date`:

```dax
MonthKey = (YEAR('Date'[Date]) * 100) + MONTH('Date'[Date])
```

![lab4_img15](./imagenes/lab4_img15.png)

14. En la vista de tabla, comprueba que la nueva columna contiene valores num茅ricos (por ejemplo, `201707` para julio de 2017).

![lab4_img16](./imagenes/lab4_img16.png)

15. Vuelve a la **vista de informe**.
16. En el panel de datos, selecciona el campo `Month`.
17. En la cinta contextual de **Herramientas de Columna**, desde dentro del grupo **Ordenar**, selecciona **Ordenar por columna** y luego selecciona **MonthKey**.

![lab4_img17](./imagenes/lab4_img17.png)

18. En la matriz visual, f铆jate que los meses ahora est谩n ordenados cronol贸gicamente.

![lab4_img18](./imagenes/lab4_img18.png)

#### Completar la tabla de fechas

En esta tarea, completar谩s el dise帽o de la tabla `Date` ocultando una columna y creando una jerarqu铆a. Luego crear谩s relaciones con las tablas `Sales` y `Targets`.

1. Cambia a la **vista de modelo**.
2. En la tabla `Date`, oculta la columna `MonthKey` (establece **Est谩 oculto** en **S铆**).
3. En el panel de Datos, selecciona la tabla `Date`, haz clic derecho en la columna `Year` y selecciona **Crear jerarqu铆a**.

![lab4_img19](./imagenes/lab4_img19.png)

4. En el panel de **Propiedades**, en el cuadro de **Nombre**, sustituye el valor por **`Fiscal`**.

![lab4_img20](./imagenes/lab4_img20.png)

5. A帽ade dos niveles a la jerarqu铆a: en la lista desplegable de la jerarqu铆a, selecciona `Quarter` y luego `Month`. Despu茅s, haz clic en **Aplicar cambios de nivel**.

![lab4_img21](./imagenes/lab4_img21.png)

6. Crea las siguientes dos relaciones de modelo:
    * `Date | Date` a `Sales | OrderDate`
    * `Date | Date` a `Targets | TargetMonth`
7. Oculta las siguientes dos columnas:
    * `Sales | OrderDate`
    * `Targets | TargetMonth`

#### Marca la tabla de fechas

En esta tarea, marcar谩s la tabla `Date` como una tabla de fechas oficial.

1. Cambia a la **vista de informe**.
2. En el panel de Datos, selecciona la tabla `Date` (no un campo individual).
3. En la cinta contextual de **Herramientas de Tablas**, desde el grupo **Calendarios**, selecciona **Marcar como Tabla de Fechas**.

![lab4_img22](./imagenes/lab4_img22.png)

4. En la ventana **Marcar como tabla de fechas**, activa la propiedad **Marcar como tabla de fechas**.
5. En la lista desplegable **Elegir una columna de fecha**, selecciona **Date**.

![lab4_img23](./imagenes/lab4_img23.png)

6. Selecciona **Guardar**.
7. Guarda el archivo de Power BI Desktop.

---

### Bloque 3: Capa de Medidas Simples y Ergonom铆a del Modelo

**Secciones del laboratorio incluidas:**
* Crea medidas simples (Pasos 1 al 16).

**Puntos t茅cnicos a ejecutar:**
* Demostraci贸n del comportamiento de agregaci贸n impl铆cita en columnas num茅ricas y justificaci贸n de su ocultaci贸n.
* Creaci贸n de medidas expl铆citas en `Sales`: `Avg Price`, `Median Price`, `Min Price`, `Max Price`, `Orders` y `Order Lines`.
* Ocultaci贸n de la columna base `Unit Price`.
* Organizaci贸n de medidas en las carpetas de presentaci贸n `Pricing` y `Counts`.
* Formato masivo (2 decimales y separadores de miles).

#### Crea medidas simples

En esta tarea, crear谩s medidas sencillas. Las medidas simples agregan valores en una sola columna o cuentan filas de una tabla.

1. En la vista de informe, en la P谩gina 2, desde el panel de datos, arrastra el campo `Sales | Unit Price` a la matriz visual.

![lab4_img24](./imagenes/lab4_img24.png)

2. En el panel de campos visuales (ubicado en el panel de Visualizaciones), en el 谩rea de **Valores**, observa que el campo `Unit Price` est谩 configurado como **Promedio del Precio Unitario**.

![lab4_img25](./imagenes/lab4_img25.png)

3. Selecciona la flecha hacia abajo para **Promedio de Precio Unitario** y observa las opciones disponibles en el men煤.

![lab4_img26](./imagenes/lab4_img26.png)

*Las columnas num茅ricas visibles permiten a los autores de informes decidir la agregaci贸n en tiempo de dise帽o. Para evitar c谩lculos inconsistentes, el enfoque recomendado es ocultar estas columnas y exponer medidas expl铆citas.*

4. Para crear una medida, en el panel de Datos, haz clic derecho en la tabla `Sales` y selecciona **Nueva medida**.
5. En la barra de f贸rmulas, a帽ade la siguiente definici贸n de medida:

```dax
Avg Price = AVERAGE(Sales[Unit Price])
```

![lab4_img27](./imagenes/lab4_img27.png)

6. A帽ade la medida `Avg Price` a la matriz visual y observa que produce el mismo resultado que la columna `Unit Price`.
7. En el 谩rea de Valores, abre el men煤 contextual del campo `Avg Price` y observa que no es posible cambiar la t茅cnica de agregaci贸n de una medida.
8. Crea las siguientes cinco medidas adicionales para la tabla `Sales`:

```dax
Median Price = MEDIAN(Sales[Unit Price])
```

```dax
Min Price = MIN(Sales[Unit Price])
```

```dax
Max Price = MAX(Sales[Unit Price])
```

```dax
Orders = DISTINCTCOUNT(Sales[SalesOrderNumber])
```

```dax
Order Lines = COUNTROWS(Sales)
```

9. Cambia a la **vista de modelo** y selecciona con `Ctrl` las cuatro medidas de precio: `Avg Price`, `Max Price`, `Median Price` y `Min Price`.
10. Para la selecci贸n m煤ltiple de medidas:
    * Establece el formato con **2 decimales**.
    * Asigna la **Carpeta de presentaci贸n**: `Pricing`.

![lab4_img28](./imagenes/lab4_img28.png)

11. Oculta la columna `Unit Price`.
12. Selecciona las medidas `Order Lines` y `Orders` y configura:
    * Activa el **Separador de millares**.
    * Asigna la **Carpeta de presentaci贸n**: `Counts`.

![lab4_img29](./imagenes/lab4_img29.png)

13. En la vista de informe, en el 谩rea de valores del gr谩fico matricial, elimina `Average of Unit Price`.
14. Aumenta el tama帽o de la matriz visual.
15. A帽ade las cinco medidas a la matriz visual (`Median Price`, `Min Price`, `Max Price`, `Orders`, `Order Lines`).
16. Verifica que los resultados est茅n formateados correctamente.

![lab4_img30](./imagenes/lab4_img30.png)

---

### Bloque 4: Medidas Avanzadas y Control de Contexto de Evaluaci贸n

**Secciones del laboratorio incluidas:**
* Crear medidas adicionales (Pasos 1 al 15).
* Laboratorio completo (Pasos 1 al 6).

**Puntos t茅cnicos a ejecutar:**
* Detecci贸n del problema de agregaci贸n en totales con la columna de objetivos `TargetAmount`.
* Implementaci贸n de la medida `Target` utilizando `HASONEVALUE` para suprimir la suma en el total general.
* Creaci贸n de las medidas `Variance` y `Variance Margin`.
* Conversi贸n de la tabla `Targets` en una **tabla de medidas** pura mediante la ocultaci贸n de `TargetAmount`.
* Validaci贸n final en el objeto visual de tabla.

#### Crear medidas adicionales

En esta tarea, crear谩s m谩s medidas que usan f贸rmulas m谩s complejas.

1. En la vista de informe, selecciona la P谩gina 1 y revisa la imagen de la tabla de los vendedores (a la derecha), observando el total incorrecto de la columna **Suma de objetivos** ($676,210,000).

![lab4_img31](./imagenes/lab4_img31.png)

2. Selecciona la tabla visual y elimina **Suma de Objetivos**.
3. Renombra la columna `Targets | Target` como `TargetAmount`.
4. Crea la siguiente medida en la tabla `Targets`:

```dax
Target = 
IF(
    HASONEVALUE('Salesperson (Performance)'[Salesperson]),
    SUM(Targets[TargetAmount])
)
```

![lab4_img33](./imagenes/lab4_img33.png)

*La funci贸n `HASONEVALUE` eval煤a si hay un 煤nico vendedor filtrado en el contexto. Si es verdadero, devuelve la suma de los objetivos del vendedor; si es falso (como en la fila de total), devuelve `BLANK`.*

5. Formatea la medida `Target` con **0 decimales**.
6. Oculta la columna `TargetAmount`.

![lab4_img32](./imagenes/lab4_img32.png)

*Al contener s贸lo medidas visibles, la tabla `Targets` se mueve autom谩ticamente a la parte superior de la lista en el panel de datos.*

7. A帽ade la medida `Target` a la tabla visual.
8. Observa que el total de la columna Objetivo es ahora **`BLANK`**.

![lab4_img34](./imagenes/lab4_img34.png)

9. Crea las siguientes dos medidas para la tabla `Targets`:

```dax
Variance = [Sales] - [Target]
```

```dax
Variance Margin = DIVIDE([Variance], [Target])
```

10. Formatea `Variance` con **0 decimales**.
11. Formatea `Variance Margin` como **Porcentaje** con **2 decimales**.
12. A帽ade las medidas `Variance` y `Variance Margin` a la tabla visual.
13. Redimensiona la tabla para visualizar correctamente todas las filas y columnas.

![lab4_img35](./imagenes/lab4_img35.png)

14. Guarda el archivo de Power BI Desktop.

#### Laboratorio completo

1. Ve al men煤 **Archivo** y selecciona **Guardar como**.
2. Guarda el archivo como `04-Final-Sales Analysis.pbix`.
3. Cierra Power BI Desktop.

---

#### 馃摳 Arquitectura Final y Evidencias
* **Estado Inicial:** Modelo sin tablas calculadas de calendario ni medidas DAX expl铆citas (`./imagenes/lab04_start_dax_calc.jpg`).
* **Estado Final:** Modelo enriquecido con tabla de calendario fiscal, jerarqu铆a `Fiscal`, medidas organizadas en carpetas de presentaci贸n (`Pricing`, `Counts`) y m茅tricas de cuotas con control `HASONEVALUE` (`./imagenes/lab04_final_dax_calc.jpg`).

---

#### 馃 Decisiones T茅cnicas Destacadas (Key Takeaways)
1. **Materializaci贸n de Tablas Calculadas:** La funci贸n `CALENDARAUTO(6)` genera y almacena datos en memoria dentro del modelo. Es ideal cuando el origen de datos carece de una dimensi贸n de tiempo corporativa.
2. **Propiedad Ordenar por Columna (*Sort by Column*):** Los campos de texto como nombres de meses se ordenan alfab茅ticamente por defecto. Asignar la clave num茅rica `MonthKey` garantiza el orden cronol贸gico fiscal en todos los informes.
3. **Control del Contexto en Totales (`HASONEVALUE`):** Previene la suma inconsistente de cuotas de ventas no aditivas devolviendo `BLANK` en agregaciones generales donde no hay un 煤nico vendedor seleccionado.
4. **Ergonom铆a mediante Tablas de Medidas Puras:** Ocultar todas las columnas impl铆citas convierte a una tabla en un contenedor exclusivo de medidas, reposicion谩ndola autom谩ticamente al principio del panel de Datos.

---

#### 鉁?Lista de Verificaci贸n (Checklist)
* [x] Tabla calculada `Salesperson` creada y conectada directamente a `Sales`.
* [x] Tabla `Date` generada con `CALENDARAUTO(6)` y marcada oficialmente como Tabla de Fechas.
* [x] Columnas calculadas `Year`, `Quarter`, `Month` y `MonthKey` creadas.
* [x] Columna `Month` ordenada cronol贸gicamente mediante `MonthKey`.
* [x] Jerarqu铆a `Fiscal` configurada en la tabla `Date`.
* [x] Medidas simples de precios (`Avg Price`, `Median Price`, etc.) creadas y organizadas en la carpeta `Pricing`.
* [x] Medidas de recuento (`Orders`, `Order Lines`) creadas en la carpeta `Counts`.
* [x] Columna `Unit Price` ocultada para evitar sumas err贸neas.
* [x] Medida `Target` implementada con `HASONEVALUE` para controlar totales vac铆os.
* [x] Medidas `Variance` y `Variance Margin` calculadas e integradas en el informe.

---

#### 馃Л Navegaci贸n entre Laboratorios
| 猬咃笍 Laboratorio Anterior | 馃彔 脥ndice General | 鉃★笍 Siguiente Laboratorio |
| :--- | :---: | :--- |
| [Lab 03: Configure a Semantic Model in Power BI](../03_configure_a_semantic_model_in_power_bi/README.md) | [馃摎 脥ndice General PL-300](../../README.md) | [Lab 05: Modify DAX Filter Context in Power BI](../05_modify_dax_filter_context/README.md) |

---
*Documentaci贸n estructurada a partir del archivo oficial para el repositorio de evidencias PL-300.*
