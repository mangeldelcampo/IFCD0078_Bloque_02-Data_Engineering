# 1. Contenido del archivo 02-Starter-Sales Analysis.pbix

Cuando abres este archivo inicial, Power BI no está vacío. Microsoft ya ha dejado preconfigurada la conexión a los orígenes de datos para que no tengas que repetir la fase de ingesta del Laboratorio 01:   

## A. Consultas de base de datos relacional (SQL Server: AdventureWorksDW2020)
Incluye 6 tablas listas para transformar procedentes del almacén de datos empresarial:   

* **DimEmployee**: Información de todos los empleados de la compañía (tanto comerciales como personal administrativo o de fábrica).   
* **DimEmployeeSalesTerritory**: Registra qué empleado tiene asignado qué territorio de ventas. Actúa como vínculo intermedio porque un empleado puede cubrir varias regiones y una región puede tener varios empleados.   
* **DimProduct**: Catálogo de artículos con costes, colores y referencias.   
* **DimReseller**: Datos de los distribuidores o clientes mayoristas que compran los productos de Adventure Works.   
* **DimSalesTerritory**: Estructura geográfica de ventas (región, país y grupo continental), incluyendo una fila técnica para la sede central (Corporate HQ).   
* **FactResellerSales**: Tabla transaccional de hechos con las líneas de pedidos, costes, cantidades y ventas facturadas a distribuidores.   

## B. Consultas de archivos planos (CSV)
Contiene 2 orígenes auxiliares basados en ficheros locales:

* **ResellerSalesTargets**: Objetivos comerciales anuales por vendedor, formateados horizontalmente en 12 columnas (M01 a M12).
* **ColorFormats**: Tabla auxiliar con los códigos de color hexadecimales (HEX) para dar formato visual personalizado a los gráficos de productos.   

## C. Parámetros auxiliares
* **SQLInstance** (apuntando a `localhost`) y **Database** (apuntando a `AdventureWorksDW2020`): Variables para parametrizar el origen y no escribir los nombres fijos en cada consulta.

[Volver al laboratorio](readme2.md)