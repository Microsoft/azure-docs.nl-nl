---
title: Een-op-weinig relationele gegevens migreren naar Azure Cosmos DB SQL API
description: Meer informatie over het verwerken van complexe gegevensmigratie voor een-op-weinig-relaties in SQL API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589615"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Een-op-weinig relationele gegevens migreren naar Azure Cosmos DB SQL API-account
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Als u wilt migreren van een relationele database naar Azure Cosmos DB SQL API, kan het nodig zijn om wijzigingen aan te brengen in het gegevensmodel voor optimalisatie.

Een veelvoorkomende transformatie is het denormaliseren van gegevens door gerelateerde subitems in één JSON-document in te voegen. Hier bekijken we enkele opties hiervoor met behulp van Azure Data Factory of Azure Databricks. Raadpleeg Gegevensmodelleren in Cosmos DB voor algemene richtlijnen [voor Azure Cosmos DB.](modeling-data.md)  

## <a name="example-scenario"></a>Voorbeeldscenario

Stel dat we de volgende twee tabellen in onze SQL database, Orders en OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Schermopname van de tabellen Orders en OrderDetails in de SQL database." border="false" :::

We willen deze een-op-weinig-relatie tijdens de migratie combineren in één JSON-document. Hiervoor kunnen we een T-SQL-query maken met 'FOR JSON' zoals hieronder wordt weergegeven:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

De resultaten van deze query zien er als hieronder uit: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Orderdetails" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Idealiter wilt u één ADF-kopieeractiviteit (Azure Data Factory) gebruiken om SQL-gegevens op te vragen als bron en de uitvoer rechtstreeks naar de Azure Cosmos DB sink te schrijven als juiste JSON-objecten. Op dit moment is het niet mogelijk om de benodigde JSON-transformatie in één kopieeractiviteit uit te voeren. Als we de resultaten van de bovenstaande query proberen te kopiëren naar een Azure Cosmos DB SQL API-container, zien we het veld OrderDetails als een tekenreeks-eigenschap van ons document in plaats van de verwachte JSON-matrix.

We kunnen deze huidige beperking op een van de volgende manieren omzeilen:

* **Gebruik Azure Data Factory twee kopieeractiviteiten:** 
  1. Gegevens in JSON-indeling van SQL naar een tekstbestand op een tussenliggende blobopslaglocatie halen, en 
  2. Laad gegevens uit het JSON-tekstbestand naar een container in Azure Cosmos DB.

* **Gebruik Azure Databricks sql te lezen en** te schrijven naar Azure Cosmos DB. Hier worden twee opties besproken.


Laten we deze benaderingen eens nader bekijken:

## <a name="azure-data-factory"></a>Azure Data Factory

Hoewel we OrderDetails niet kunnen insluiten als een JSON-matrix in het Cosmos DB-doeldocument, kunnen we het probleem oplossen door twee afzonderlijke kopieeractiviteiten te gebruiken.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Kopieeractiviteit #1: SqlJsonToBlobText

Voor de brongegevens gebruiken we een SQL-query om het resultaat op te halen als één kolom met één JSON-object (dat de Volgorde vertegenwoordigt) per rij met behulp van de mogelijkheden SQL Server OPENJSON en FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF-kopie":::


Voor de sink van de kopieeractiviteit SqlJsonToBlobText kiezen we 'Tekst met scheidingstekens' en wijzen we deze naar een specifieke map in Azure Blob Storage met een dynamisch gegenereerde unieke bestandsnaam (bijvoorbeeld ' @concat (pipeline(). RunId, '.json').
Omdat ons tekstbestand niet echt 'gescheiden' is en we niet willen dat het wordt geparseerd in afzonderlijke kolommen met behulp van komma's en de dubbele aanhalingstekens (' ) wilt behouden, stellen we 'Kolom scheidingsteken' in op een tabblad ('\t') - of een ander teken dat niet voorkomt in de gegevens - en 'Aanhalingsteken' op 'Geen aanhalingsteken'.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Schermopname met de instellingen kolom scheidingsteken en prijsopgave.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Kopieeractiviteit #2: BlobJsonToCosmos

Vervolgens wijzigen we de ADF-pijplijn door de tweede kopieeractiviteit toe te voegen die in Azure Blob Storage zoekt naar het tekstbestand dat is gemaakt door de eerste activiteit. Deze wordt verwerkt als 'JSON'-bron om in te voegen Cosmos DB sink als één document per JSON-rij in het tekstbestand.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Schermopname met de velden JSON-bronbestand en Bestandspad.":::

Optioneel voegen we ook een activiteit Verwijderen toe aan de pijplijn, zodat alle vorige bestanden die nog in de map /Orders/ staan vóór elke run worden verwijderd. Onze ADF-pijplijn ziet er nu zo uit:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Schermopname met de activiteit Verwijderen.":::

Nadat we de bovenstaande pijplijn hebben activeert, zien we een bestand dat is gemaakt in onze tussenliggende Azure Blob Storage locatie met één JSON-object per rij:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Schermopname van het gemaakte bestand met de JSON-objecten.":::

We zien ook Orders-documenten met goed ingesloten OrderDetails die zijn ingevoegd in onze Cosmos DB verzameling:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Schermopname van de orderdetails als onderdeel van het Cosmos DB document":::


## <a name="azure-databricks"></a>Azure Databricks

We kunnen Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) ook gebruiken om de gegevens van onze SQL Database-bron naar de Azure Cosmos DB-bestemming te kopiëren zonder de intermediaire tekst-/JSON-bestanden in Azure Blob Storage. 

> [!NOTE]
> Voor de duidelijkheid en eenvoud bevatten de onderstaande codefragmenten expliciet inline dummydatabasewachtwoorden, maar u moet altijd Azure Databricks gebruiken.
>

Eerst maken en koppelen we de vereiste [SQL-connector](/connectors/sql/) en [Azure Cosmos DB connectorbibliotheken](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) aan ons Azure Databricks cluster. Start het cluster opnieuw op om ervoor te zorgen dat bibliotheken worden geladen.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Schermopname die laat zien waar u de vereiste SQL-connector en Azure Cosmos DB connectorbibliotheken kunt maken en koppelen aan Azure Databricks cluster.":::

Vervolgens presenteren we twee voorbeelden, voor Scala en Python. 

### <a name="scala"></a>Scala
Hier krijgen we de resultaten van de SQL-query met 'FOR JSON'-uitvoer in een DataFrame:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Schermopname van de uitvoer van de SQL-query in een DataFrame.":::

Vervolgens maken we verbinding met onze Cosmos DB database en verzameling:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Ten slotte definiëren we ons schema en gebruiken we from_json dataframe toe te passen voordat het wordt opgeslagen in de CosmosDB-verzameling.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Schermopname die de juiste matrix markeert voor het opslaan naar een Cosmos DB verzameling.":::


### <a name="python"></a>Python

Als alternatieve methode moet u mogelijk JSON-transformaties uitvoeren in Spark (als de brondatabase geen ONDERSTEUNING biedt voor 'FOR JSON' of een vergelijkbare bewerking), of als u parallelle bewerkingen wilt gebruiken voor een zeer grote gegevensset. Hier presenteren we een PySpark-voorbeeld. Begin met het configureren van de bron- en doeldatabaseverbindingen in de eerste cel:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Vervolgens voeren we een query uit op de brondatabase (in dit geval SQL Server) voor zowel de order- als de ordergegevensrecords, en plaatsen we de resultaten in Spark Dataframes. We maken ook een lijst met alle order-ID's en een threadgroep voor parallelle bewerkingen:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Maak vervolgens een functie voor het schrijven van Orders naar de doel-SQL API-verzameling. Met deze functie worden alle orderdetails voor de opgegeven order-id gefilterd, geconverkeerd naar een JSON-matrix en wordt de matrix in een JSON-document toegevoegd dat we voor die volgorde naar de doel-SQL API-verzameling schrijven:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Ten slotte roepen we het bovenstaande aan met behulp van een kaartfunctie in de threadpool, om deze parallel uit te voeren en door te geven in de lijst met order-ID's die we eerder hebben gemaakt:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Bij beide benaderingen moeten we aan het einde ingesloten OrderDetails in elk Order-document in de verzameling Cosmos DB opgeslagen:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Volgende stappen
* Meer informatie [over gegevensmodelleren in Azure Cosmos DB](./modeling-data.md)
* Meer [informatie over het modelleren en partitioneren van gegevens op Azure Cosmos DB](./how-to-model-partition-example.md)
