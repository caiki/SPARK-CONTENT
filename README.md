# SPARK-CONTENT
SPARK-CONTENT
Curso de Spark Big Data Feito pelo Ricardo Paiva.
https://www.youtube.com/playlist?list=PLzKHEB8QCel8UIA2JfUTYQaUgvJa6mUog

### Variable de ambiente para ingresar al shell de SPARK con python
    pyspark 

### Listar comandos y atributos disponibles con SPARK
    dir(spark)

### Se puede usar dataframes

    df.printSchema() # Para saber el esquema del dataframe.
    df.show() # Para mostrar la tabla en consola

### Para conseguir datos de ejemplo.
    www.portaltransparencia.gov.br/downloads

### Descomprimir 
    unzip data.zip

### Convertir datos en un formato mas apropiado para trabajar con SPARK
    iconv -f ISO-8859-1 -t UTF-8 201609_Diarias.csv > 201609_Diarias.utf8.csv
 
### Visualizar los primeros registros
    $ head 201609_Diarias.utf8.csv
 
### Cargar datos al SPARK "Crea una referencia a los datos csv, no esta cargando los datos en memoria"
    df =  spark.read.format("com.databricks.spark.csv")\
      .option("header", "true")\
      .option("delimiter", "\t")\
      .option("inferSchema", "true")\
      .load("201609_Diarias.utf8.csv") # Tambien se puede trabajar con archivos PARQUE, los cuales son diseñados para grandes cantidades de datos.
      
### Imprimir schema identificado por SPARK
    df.printSchema()
### Contar registros de datos
    df.count() #Ejecuta en paralelo
    df.show() # Muestra truncando los 20 primeros registros
    df.select("Nombre de Organo Superior", "Valor de nacimiento").show(truncate = False) # Muestra solo determinadas columnas
    df.select("Nombre de Organo Superior", "Valor de nacimiento")
### Solo para contar determinados registros
    df.filter(df["Nome Orgao Superior"]=="MINISTERIO DE CULTURA").count()
    df.filter(df["Nome Orgao Superior"]=="MINISTERIO DE CULTURA").distinct().show(100, truncate=False) 

### Agrupar valores de la columna
    df.groupBy("Nome Orgao Superior").count().show(truncate = False)
    df.groupBy("Nome Orgao Superior").count().orderBy("count").show(truncate = False)
    df.groupBy("Nome Orgao Superior").count().orderBy("count",ascending = False).show(100, truncate = False)
### Cuando los números esten en un formato String, es necesario hacer la transformación de los datos
    df.select("Valor Pagamento").show() # Numeros com "," , para trabajar deberia operar con "."
    to_value = lambda v: float(v.replace(",",".")) # Crear funcion de transformación 
    from pyspark.sql import functions as F # Importando libreria para usar UDF
    udf_to_value = F.udf(to_value,pyspark.sql.types.FloatType()) #  
    df2 = df.withColumn("value", udf_to_value(df["Valor Pagamento"])) # Transformando value to float
    df2.printSchema()
### Ahora si se puede usar la agregación de Suma
    df2.groupBy("Nome do Orgão Superior").sum("value").show() # Por cada organo
    df2.describe("value")
    df2.describe("value").show() # Porque los comandos son LAZY

### Otras agregaciones
    df2.groupBy("Nome do Orgao Superior").agg(F.max("value"),F.count("value"),F.sum("value"),F.avg("value"))\
    .show(100, truncate =False)
    df2.groupBy("Nome do Orgao Superior").agg(F.max("value"),F.count("value"),F.sum("value"),F.avg("value"))\
    .orderBy("sum(value)",descending = False).show(100, truncate =False)


### Mayor información para seguir jugando !!!!

    https://spark.apache.org/docs/2.4.0/api/python/pyspark.sql.html
    
    
    


