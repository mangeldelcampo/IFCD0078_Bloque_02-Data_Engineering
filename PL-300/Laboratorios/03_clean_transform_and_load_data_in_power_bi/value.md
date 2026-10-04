¿Por qué dice «Value» y no un nombre o un número?
1. Es un hipervínculo de navegación (Navigation Link):
    - En la base de datos SQL Server, la tabla DimProduct apunta a DimProductSubcategory mediante su clave externa (ProductSubcategoryKey).
    - En lugar de cargar y duplicar en memoria todos los campos de esa subcategoría de golpe, el motor de Power Query coloca un puntero con la etiqueta Value.
    - Si hicieras clic sobre el espacio en blanco al lado de la palabra Value (sin hacer clic en el texto directamente), en la parte inferior verías la ficha con todos los campos asociados a esa subcategoría concreta (ProductSubcategoryKey, nombres en varios idiomas, categoría asociada, etc.). 
2.  Diferencia técnica entre Table y Value en Power Query:
    - Table: Aparece cuando la relación es de uno a muchos (por ejemplo, desde un cliente hacia todas sus líneas de pedidos). La celda contiene un conjunto de múltiples filas.
    - Value: Aparece habitualmente cuando la relación es de muchos a uno hacia una dimensión padre. Cada producto pertenece a una única subcategoría, por lo que la celda almacena el objeto/registro único correspondiente a esa subcategoría específica.
3.  Propósito en el flujo ETL:
    - Es la indicación explícita de que no necesitas hacer un Merge (combinación) tradicional con otra consulta, porque la relación ya viene resuelta desde el origen de datos relacional.
    - Al pulsar el botón de expansión en la esquina superior derecha de la cabecera, ese enlace Value se «desempaqueta» y sus campos pasan a ser columnas normales de la tabla. 