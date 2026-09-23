PL-300-Microsoft-Power-BI-Data-Analyst

# **Obtener datos en Power BI**

## **Historia del laboratorio**

Este laboratorio está diseñado para introducirte en la aplicación Power BI Desktop, cómo conectarte a los datos y cómo utilizar técnicas de vista previa para entender las características y la calidad de los datos fuente.  
En este laboratorio, aprendes cómo:

* Abre Power BI Desktop.  
* Conéctate a diferentes fuentes de datos.  
* Previsualizar los datos fuente con Power Query.  
* Utiliza funciones de perfilado de datos en Power Query.

**Este laboratorio debería durar aproximadamente 30 minutos.**

## **Empieza con Power BI Desktop**

Para completar este ejercicio, primero abre un navegador web e introduce la siguiente URL para descargar la carpeta zip:  
https://github.com/MicrosoftLearning/PL-300-Microsoft-Power-BI-Data-Analyst/raw/Main/Allfiles/Labs/01-get-data-in-power-bi/01-get-data.zip  
Extrae la carpeta a la **carpeta C:\\Users\\Student\\Downloads\\01-get-data**.  
Abre el archivo **01-Starter-Sales Analysis.pbik**.

* Este archivo inicial ha sido configurado especialmente para ayudarte a completar el laboratorio. Las siguientes configuraciones a nivel de informe han sido desactivadas en el archivo inicial:  
  * Relaciones de carga \> importación de datos desde fuentes de datos en la primera carga (Import relationships from data sources on first load) 
  * Carga de datos \> Autodetección de nuevas relaciones después de que se carguen los datos (Autodetect new relationships)
  

## **Obtener datos de SQL Server**

Esta tarea te enseña cómo conectarte a una base de datos SQL Server e importar tablas, que generan consultas en Power Query.

1. En la pestaña de **Inicio** de la cinta, desde dentro del grupo **de Datos**, selecciona **SQL Server**.

![][image1]

\!\[\](./imagen1.png)

2. En la ventana **de la base de datos SQL Server**, en el cuadro **del servidor**, introduce **localhost** y deja **Base de datos** en blanco, luego **selecciona OK.**  
   ***Nota:** En este laboratorio, te conectarás a la base de datos de SQL Server usando **localhost**. Aunque esto está bien para el laboratorio, no se considera una buena práctica para soluciones reales.*  
3. Si te piden las credenciales, selecciona **Windows \> Usar mis credenciales actuales** y luego **Conectar**.  
4. Selecciona **Vale** si recibes una advertencia de que no se puede establecer una conexión cifrada.  
5. En el panel **de Navigator**, amplía la base de datos **AdventureWorksDW2020**.  
   ***Nota:** La base de datos **AdventureWorksDW2020** se basa en la base de datos de ejemplo **AdventureWorksDW2017**. Ha sido modificado para apoyar los objetivos de aprendizaje de los laboratorios del curso.*  
6. Selecciona la **tabla DimEmployee** y observa la vista previa de los datos de la tabla.  
   ***![][image2]***  
   \!\[\](./imagen2.png)  
   ***Nota:** Los datos de vista previa permiten ver las columnas y una muestra de filas.*  
7. Selecciona las siguientes tablas **marcando las casillas** junto a sus nombres.  
   * DimEmployee  
   * DimEmployeeSalesTerritory  
   * DimProduct  
   * DimReseller  
   * DimSalesTerritory  
   * FactResellerSales  
8. Completa esta tarea seleccionando **Transformar datos**, lo que abrirá Power Query Editor \- deja este espacio abierto para la siguiente tarea.

Ahora has conectado seis tablas de una base de datos SQL Server.

## **Datos de vista previa en Power Query Editor**

Esta tarea introduce el Power Query Editor y te permite revisar y perfilar los datos. Esto te ayuda a determinar cómo limpiar y transformar los datos más adelante. También revisarás tanto las tablas dimensionales con el prefijo "Dim" como las tablas de hechos con el prefijo "Fact".

1. En la ventana **del Editor de Power Consultes**, a la izquierda, fíjate en el panel **de Consultas**. El panel **de Consultas** contiene una consulta por cada tabla que has comprobado.  
   ![][image3]  
   \!\[\](./imagen3.png)  
2. Selecciona la consulta **DimEmployee**.  
   *La tabla **DimEmployee** en la base de datos de SQL Server almacena una fila para cada empleado. Un subconjunto de las filas de esta tabla representa a los vendedores, que serán relevantes para el modelo que vayas a desarrollar.*  
3. En la esquina inferior izquierda de la barra de estado se muestran algunas estadísticas de la tabla: la tabla tiene 33 columnas y 296 filas.

![][image4]

\!\[\](./imagen4.png)

4. En el panel de vista previa de datos, desplázate horizontalmente para revisar todas las columnas. Fíjate en que las últimas cinco columnas contienen enlaces **de Tabla** o **Valor**.  
   *Estas cinco columnas representan relaciones con otras tablas de la base de datos. Pueden usarse para unir mesas. Más adelante unirás estas tablas en **el Load Transformed Data en Power BI Desktop** Lab.*  
5. Para evaluar la calidad de las columnas, en la pestaña **de Vista** de la cinta desde el grupo **de Vista previa de datos**, comprueba **Calidad de columna**. La función de calidad de columna te permite determinar fácilmente el porcentaje de valores válidos, de error o vacíos que se encuentran en las columnas.

![][image5]

\!\[\](./imagen5.png)

6. Fíjate que la columna **Posición** tiene un 94% de filas vacías (nulas).  
   ![][image6]  
   \!\[\](./imagen6.png)  
7. Para evaluar la distribución de columnas, en la pestaña **de cinta Vista**, desde dentro del grupo **de Vista previa de datos**, comprueba **Distribución de columnas**.  
8. Revisa de nuevo la columna **Posición** y observa que hay cuatro valores distintos y uno único.  
9. Revisa la distribución de columnas para la columna **EmployeeKey**: hay 296 valores distintos y 296 valores únicos.  
   ***![][image7]***  
   \!\[\](./imagen7.png)  
   ***Nota:** Cuando los recuentos distintos y únicos son los mismos, significa que la columna contiene valores únicos. Al modelar, es importante que algunas tablas de modelos tengan columnas únicas. Estas columnas únicas pueden usarse para crear relaciones de uno a muchos, que harás en el **Model Data en Power BI Desktop** Lab.*  
10. En el panel **de Consultas**, selecciona la consulta **DimProduct**.  
    *La tabla **DimProduct** contiene una fila por cada producto vendido por la empresa.*  
11. En el panel **de Consultas**, selecciona la **consulta DimRevendedor**.  
    *La tabla **de DimReseller** contiene una fila por distribuidor. Los revendedores venden, distribuyen o añaden valor a los productos de Adventure Works.*  
12. Para ver los valores de las columnas, en la pestaña **de cinta Vista**, desde dentro del grupo **de Vista previa de datos**, marque **Perfil de columna**.  
13. Selecciona la cabecera **de la columna BusinessType** y observa el nuevo panel debajo del panel de vista previa de datos. Revisa las estadísticas de las columnas y la distribución de valores en el panel de vista previa de datos.  
    *Fíjate en el problema de calidad de datos: hay dos etiquetas para almacén (**Warehouse** y el mal escrito **Warehouse**).*  
    *![][image8]*  
    \!\[\](./imagen8.png)  
14. Pasa el cursor sobre la barra **de Ware** House y observa que hay cinco filas con este valor.  
15. En el **panel de Consultas**, selecciona la consulta **DimSalesTerritory**.  
    *La tabla **DimSalesTerritory** contiene una fila por región de ventas, incluyendo **la sede corporativa** (sede central). Las regiones se asignan a un país y los países a grupos. En el **Model Data en Power BI Desktop** Lab crearás una jerarquía para apoyar el análisis a nivel regional, país o grupo.*  
16. En el panel **de Consultas**, selecciona la consulta **FactResellerSales**.  
    *La tabla **FactResellerSales** contiene una fila por cada línea de pedido de venta: una orden de venta contiene uno o más elementos de línea.*  
17. Revisa la calidad de la columna **CosteProducto Total**, y observa que el 8% de las filas están vacías.  
    *La falta de valores de columna **TotalProductCost** es un problema de calidad de datos.*

## **Obtener datos de un archivo CSV**

En esta tarea, crearás una nueva consulta basada en archivos CSV.

1. Para añadir una nueva consulta, en la ventana **del Editor de Power Query**, en la pestaña **de la cinta de inicio**, desde dentro del grupo **de Nuevas Consultas**, selecciona la flecha **hacia abajo Nueva Fuente** y luego **selecciona Texto/CSV**.  
2. Ve a la **carpeta Downloads \> 01-get-data** que extrajiste antes y selecciona el **archivo ResellerSalesTargets.csv**. **Selecciona Abrir**.  
3. En la **ventana ResellerSalesTargets.csv**, revisa los datos de vista previa. Selecciona **OK**.  
4. En el **panel de Consultas**, observa la adición de la **consulta ResellerSalesTargets**.  
   *El archivo **CSV ResellerSalesTargets** contiene una fila por vendedor, por año. Cada fila registra 12 objetivos de ventas mensuales (expresados en miles). El año económico de la empresa Adventure Works comienza el 1 de julio.*  
5. Observa que ninguna columna contiene valores vacíos. Si falta un objetivo mensual de ventas, la columna muestra un guion en su lugar.  
6. Revisa los iconos de cada encabezado de columna, a la izquierda del nombre de la columna. Los iconos representan el tipo de dato de la columna. **123** es número entero, y **ABC** es texto.  
   ![][image9]  
   \!\[\](./imagen9.png)  
7. Repite los pasos para crear una consulta basada en el **archivo ColorFormats.csv**.  
   *El archivo **CSV de ColorFormats** contiene una fila por color de producto. Cada fila registra los códigos HEX para formatear los colores de fondo y de fuente.*

Ahora deberías tener dos nuevas consultas, **ResellerSalesTargets** y **ColorFormats**.  
![][image10]

\!\[\](./imagen10.png)

## **Laboratorio completo**

Puedes optar por guardar tu informe de Power BI, aunque no es necesario para este laboratorio. En el siguiente ejercicio, trabajarás con un archivo inicial predefinido.

1. Ve al menú **"Archivo"** en la esquina superior izquierda y **selecciona "Guardar como".**  
2. Seleccione **Explorar este dispositivo**.  
3. Selecciona la carpeta donde quieres guardar el archivo y ponle un nombre descriptivo.  
4. Selecciona el botón **Guardar** para guardar tu informe como archivo .pbik.  
5. Si aparece un cuadro de diálogo que te pide que apliques cambios pendientes en la consulta, selecciona **Aplicar**.  
6. Cierra Power BI Desktop.
