tras terminar de configurar todas las consultas en el momento de "Cerrar y aplicar", para que las consultas generen las tablas del modelo y carguen sus datos y mantener el «Esquema sincronizado»,
puedes encontrarte con errores.
Lo mas sencillo es copiar el código del error y pasarlo a un modelo de IA para que evalúe los errores.

En mi caso después de realizar varios cambios la solución mas sencilla es:


El rastro de la excepción y el comportamiento confirman el diagnóstico definitivo del problema:

```text
Error: System.NullReferenceException
MethodDef: GetCanRefreshData
at Microsoft.PowerBI.Modeling.Engine.DataModel.Utilities.DataModelAuthoringExtensions.GetCanRefreshData(ITable table)
at Microsoft.PowerBI.Modeling.Engine.Loader.PowerQueryToModelLoader.ApplyQueryChangesAndCreateRelationships

```

Incluso habiendo borrado la caché y habiendo dejado únicamente la tabla `Region`, el error persiste con el mismo `NullReferenceException` en `GetCanRefreshData`. Esto demuestra que **el archivo `.pbix` base (`02-Starter-Sales Analysis.pbix`) tiene los metadatos del esquema de Analysis Services corrompidos internamente** en esta compilación de Power BI Desktop (`2.156.951.0` de julio de 2026). El archivo arrastra definiciones residuales de tablas y particiones del inicio que el motor intenta validar al refrescar, devolviendo `null` independientemente de lo que se configure en Power Query.

Para salir de este bucle técnico y completar el laboratorio sin perder un solo paso de tu trabajo, se debe transferir el código M a un archivo `.pbix` completamente nuevo.

---

### Solución Definitiva: Crear un `.pbix` limpio

Dado que el código M de todas tus consultas está completamente depurado y verificado, no tienes que rehacer ninguna transformación manual:

#### Paso 1: Abrir una nueva sesión de Power BI Desktop

1. Cierra la ventana de Power BI Desktop que da error.
2. Abre **Power BI Desktop** desde el menú Inicio de Windows (un archivo en blanco nuevo sin plantilla).
3. Haz clic en **Transformar datos** (*Transform Data*) en la cinta de opciones para abrir el Editor de Power Query vacío.

#### Paso 2: Crear los parámetros

1. En Power Query, haz clic en **Administrar parámetros** > **Nuevo parámetro** (*Manage Parameters* > *New Parameter*):
* **Nombre:** `SQLInstance`
* **Tipo:** Texto (*Text*)
* **Valor actual:** `localhost`
* Pulsa Aceptar.


2. Vuelve a pulsar **Nuevo parámetro**:
* **Nombre:** `Database`
* **Tipo:** Texto (*Text*)
* **Valor actual:** `AdventureWorksDW2020`
* Pulsa Aceptar.



#### Paso 3: Pegar las consultas usando el Editor Avanzado

Para cada una de las 8 consultas, el proceso es idéntico:

1. En la cinta de opciones, haz clic en **Nuevo origen** (*New Source*) > **Consulta en blanco** (*Blank Query*).
2. Haz clic en **Editor avanzado** (*Advanced Editor*).
3. Borra el código por defecto y pega el bloque correspondiente:

* **Para `Salesperson`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_DimEmployee = AdventureWorksDW2020{[Schema="dbo",Item="DimEmployee"]}[Data],
    #"Filas filtradas" = Table.SelectRows(dbo_DimEmployee, each ([SalesPersonFlag] = true)),
    #"Otras columnas quitadas" = Table.SelectColumns(#"Filas filtradas",{"EmailAddress", "EmployeeKey", "EmployeeNationalIDAlternateKey", "FirstName", "LastName", "Title"}),
    #"Columnas combinadas" = Table.CombineColumns(#"Otras columnas quitadas",{"FirstName", "LastName"},Combiner.CombineTextByDelimiter(" ", QuoteStyle.None),"Salesperson"),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Columnas combinadas",{{"EmployeeNationalIDAlternateKey", "EmployeeID"}, {"EmailAddress", "UPN"}})
in
    #"Columnas con nombre cambiado"

```

*(Cambia el nombre de la consulta en el panel derecho a `Salesperson`)*.

* **Para `SalespersonRegion`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_DimEmployeeSalesTerritory = AdventureWorksDW2020{[Schema="dbo",Item="DimEmployeeSalesTerritory"]}[Data],
    #"Columnas quitadas" = Table.RemoveColumns(dbo_DimEmployeeSalesTerritory,{"DimEmployee", "DimSalesTerritory"})
in
    #"Columnas quitadas"

```

*(Cambia el nombre a `SalespersonRegion`)*.

* **Para `Region`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_DimSalesTerritory = AdventureWorksDW2020{[Schema="dbo",Item="DimSalesTerritory"]}[Data],
    #"Filas filtradas" = Table.SelectRows(dbo_DimSalesTerritory, each ([SalesTerritoryAlternateKey] <> 0)),
    #"Otras columnas quitadas" = Table.SelectColumns(#"Filas filtradas",{"SalesTerritoryKey", "SalesTerritoryRegion", "SalesTerritoryCountry", "SalesTerritoryGroup"}),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Otras columnas quitadas",{{"SalesTerritoryRegion", "Region"}, {"SalesTerritoryCountry", "Country"}, {"SalesTerritoryGroup", "Group"}})
in
    #"Columnas con nombre cambiado"

```

*(Cambia el nombre a `Region`)*.

* **Para `Reseller`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_DimReseller = AdventureWorksDW2020{[Schema="dbo",Item="DimReseller"]}[Data],
    #"Columnas quitadas" = Table.RemoveColumns(dbo_DimReseller,{"GeographyKey", "ResellerAlternateKey", "Phone", "NumberEmployees", "OrderFrequency", "OrderMonth", "FirstOrderYear", "LastOrderYear", "ProductLine", "AddressLine1", "AddressLine2", "AnnualSales", "BankName", "MinPaymentType", "MinPaymentAmount", "AnnualRevenue", "YearOpened", "FactResellerSales"}),
    #"Se expandió DimGeography" = Table.ExpandRecordColumn(#"Columnas quitadas", "DimGeography", {"City", "StateProvinceName", "EnglishCountryRegionName"}, {"City", "StateProvinceName", "EnglishCountryRegionName"}),
    #"Filas filtradas" = Table.SelectRows(#"Se expandió DimGeography", each true),
    #"Valor reemplazado" = Table.ReplaceValue(#"Filas filtradas","Ware House","Warehouse",Replacer.ReplaceText,{"BusinessType"}),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Valor reemplazado",{{"BusinessType", "Business Type"}, {"ResellerName", "Reseller"}, {"StateProvinceName", "State-Province"}, {"EnglishCountryRegionName", "Country-Region"}})
in
    #"Columnas con nombre cambiado"

```

*(Cambia el nombre a `Reseller`)*.

* **Para `ColorFormats`:**

```powerquery
let
    Source = Csv.Document(Web.Contents("https://raw.githubusercontent.com/MicrosoftLearning/PL-300-Microsoft-Power-BI-Data-Analyst/Main/Allfiles/Resources/ColorFormats.csv"),[Delimiter=",", Columns=3, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}}),
    #"Encabezados promovidos" = Table.PromoteHeaders(#"Changed Type", [PromoteAllScalars=true]),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Encabezados promovidos",{{"Color", type text}, {"Background Color Format", type text}, {"Font Color Format", type text}})
in
    #"Tipo cambiado"

```

*(Cambia el nombre a `ColorFormats`)*.

* **Para `Product`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_DimProduct = AdventureWorksDW2020{[Schema="dbo",Item="DimProduct"]}[Data],
    #"Filas filtradas" = Table.SelectRows(dbo_DimProduct, each ([FinishedGoodsFlag] = true)),
    #"Columnas quitadas" = Table.RemoveColumns(#"Filas filtradas",{"ProductAlternateKey", "ProductSubcategoryKey", "WeightUnitMeasureCode", "SizeUnitMeasureCode", "SpanishProductName", "FrenchProductName", "FinishedGoodsFlag", "SafetyStockLevel", "ReorderPoint", "ListPrice", "Size", "SizeRange", "Weight", "DaysToManufacture", "ProductLine", "DealerPrice", "Class", "Style", "ModelName", "LargePhoto", "EnglishDescription", "FrenchDescription", "ChineseDescription", "ArabicDescription", "HebrewDescription", "ThaiDescription", "GermanDescription", "JapaneseDescription", "TurkishDescription", "StartDate", "EndDate", "Status", "FactInternetSales", "FactProductInventory", "FactResellerSales"}),
    #"Se expandió DimProductSubcategory" = Table.ExpandRecordColumn(#"Columnas quitadas", "DimProductSubcategory", {"EnglishProductSubcategoryName", "DimProductCategory"}, {"EnglishProductSubcategoryName", "DimProductCategory"}),
    #"Se expandió DimProductCategory" = Table.ExpandRecordColumn(#"Se expandió DimProductSubcategory", "DimProductCategory", {"EnglishProductCategoryName"}, {"EnglishProductCategoryName"}),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Se expandió DimProductCategory",{{"EnglishProductName", "Product"}, {"StandardCost", "Standard Cost"}, {"EnglishProductSubcategoryName", "Subcategory"}, {"EnglishProductCategoryName", "Category"}}),
    #"Consultas combinadas" = Table.NestedJoin(#"Columnas con nombre cambiado", {"Color"}, ColorFormats, {"Color"}, "ColorFormats", JoinKind.LeftOuter),
    #"Se expandió ColorFormats" = Table.ExpandTableColumn(#"Consultas combinadas", "ColorFormats", {"Background Color Format", "Font Color Format"}, {"Background Color Format", "Font Color Format"})
in
    #"Se expandió ColorFormats"

```

*(Cambia el nombre a `Product`)*.

* **Para `Sales`:**

```powerquery
let
    Source = Sql.Databases(SQLInstance),
    AdventureWorksDW2020 = Source{[Name=Database]}[Data],
    dbo_FactResellerSales = AdventureWorksDW2020{[Schema="dbo",Item="FactResellerSales"]}[Data],
    #"Otras columnas quitadas" = Table.SelectColumns(dbo_FactResellerSales,{"SalesOrderNumber", "OrderDate", "ProductKey", "ResellerKey", "EmployeeKey", "SalesTerritoryKey", "OrderQuantity", "UnitPrice", "TotalProductCost", "SalesAmount", "DimProduct"}),
    #"Se expandió DimProduct" = Table.ExpandRecordColumn(#"Otras columnas quitadas", "DimProduct", {"StandardCost"}, {"StandardCost"}),
    #"Personalizada agregada" = Table.AddColumn(#"Se expandió DimProduct", "Cost", each if [TotalProductCost] = null then [OrderQuantity] * [StandardCost] else [TotalProductCost]),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Personalizada agregada",{{"OrderQuantity", "Quantity"}, {"UnitPrice", "Unit Price"}, {"SalesAmount", "Sales"}}),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Columnas con nombre cambiado",{{"Quantity", Int64.Type}, {"Unit Price", Currency.Type}, {"Sales", Currency.Type}, {"Cost", Currency.Type}}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Tipo cambiado",{"TotalProductCost", "StandardCost"})
in
    #"Columnas quitadas"

```

*(Cambia el nombre a `Sales`)*.

* **Para `Targets`:**

```powerquery
let
    Source = Csv.Document(Web.Contents("https://raw.githubusercontent.com/MicrosoftLearning/PL-300-Microsoft-Power-BI-Data-Analyst/Main/Allfiles/Resources/ResellerSalesTargets.csv"),[Delimiter=",", Columns=14, Encoding=1200, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Year", Int64.Type}, {"EmployeeID", Int64.Type}, {"M01", type text}, {"M02", type text}, {"M03", type text}, {"M04", type text}, {"M05", type text}, {"M06", type text}, {"M07", type text}, {"M08", type text}, {"M09", type text}, {"M10", type text}, {"M11", type text}, {"M12", type text}}),
    #"Otras columnas con anulación de dinamización" = Table.UnpivotOtherColumns(#"Changed Type", {"Year", "EmployeeID"}, "Atributo", "Valor"),
    #"Filas filtradas" = Table.SelectRows(#"Otras columnas con anulación de dinamización", each ([Valor] <> "-")),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Filas filtradas",{{"Atributo", "MonthNumber"}, {"Valor", "Target"}}),
    #"Valor reemplazado" = Table.ReplaceValue(#"Columnas con nombre cambiado","M","",Replacer.ReplaceText,{"MonthNumber"}),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Valor reemplazado",{{"MonthNumber", Int64.Type}}),
    #"Columna combinada insertada" = Table.AddColumn(#"Tipo cambiado", "Combinada", each Text.Combine({"1/", Text.From([MonthNumber], "es-ES"), "/", Text.From([Year], "es-ES")}), type text),
    #"Columnas con nombre cambiado1" = Table.RenameColumns(#"Columna combinada insertada",{{"Combinada", "TargetMonth"}}),
    #"Columnas quitadas" = Table.RemoveColumns(#"Columnas con nombre cambiado1",{"Year", "MonthNumber"}),
    #"Tipo cambiado1" = Table.TransformColumnTypes(#"Columnas quitadas",{{"Target", Currency.Type}, {"TargetMonth", type date}}),
    #"Columna multiplicada" = Table.TransformColumns(#"Tipo cambiado1", {{"Target", each _ * 1000, Currency.Type}})
in
    #"Columna multiplicada"

```

*(Cambia el nombre a `Targets`)*.

---

#### Paso 4: Ajustar la carga y aplicar

1. Haz clic derecho sobre **`ColorFormats`** y desmarca **Habilitar carga** (*Enable load*).


2. Ve a la pestaña **Inicio** y pulsa **Cerrar y aplicar** (*Close & Apply*).



Al tratarse de una instancia limpia de Analysis Services generada desde cero, las 7 tablas se cargarán directamente en el panel de datos sin encontrar ninguna referencia nula en el modelo. Guarda el archivo resultante con el nombre del laboratorio (`02-Sales Analysis.pbix`).
[Volver al laboratorio](readme2.md)
