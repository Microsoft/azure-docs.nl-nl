---
title: Een-op-weinig relationele gegevens migreren naar Azure Cosmos DB SQL-API
description: Meer informatie over het verwerken van complexe gegevens migratie voor een-op-een-relatie in de SQL-API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096747"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Een-op-weinig relationele gegevens migreren naar Azure Cosmos DB SQL-API-account
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Als u wilt migreren van een relationele data base naar Azure Cosmos DB SQL-API, kan het nodig zijn om wijzigingen aan te brengen in het gegevens model voor Optima Lise ring.

Een veelvoorkomende trans formatie is het denormaliseren van gegevens door het insluiten van gerelateerde subitems binnen één JSON-document. Hier zien we een aantal opties voor het gebruik van Azure Data Factory of Azure Databricks. Raadpleeg voor algemene richt lijnen voor gegevens modellering voor Cosmos DB [gegevens modellering in azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Voorbeeldscenario

We gaan ervan uit dat we de volgende twee tabellen hebben in onze SQL database, orders en OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Scherm opname waarin de tabellen Orders en OrderDetails in de SQL database worden weer gegeven." border="false" :::

We willen deze een-op-een-relatie combi neren in één JSON-document tijdens de migratie. Hiervoor kunnen we een T-SQL-query maken met behulp van ' voor JSON ', zoals hieronder wordt beschreven:

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

De resultaten van deze query zien er als volgt uit: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Bestellingsgegevens" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

IDEA liter wilt u een Kopieer activiteit met één Azure Data Factory (ADF) gebruiken om SQL-gegevens als bron op te vragen en de uitvoer rechtstreeks naar Azure Cosmos DB sink te schrijven als juiste JSON-objecten. Het is momenteel niet mogelijk om de benodigde JSON-trans formatie uit te voeren in één Kopieer activiteit. Als we proberen de resultaten van de bovenstaande query te kopiëren naar een Azure Cosmos DB SQL API-container, zien we het veld OrderDetails als een teken reeks eigenschap van het document, in plaats van de verwachte JSON-matrix.

De huidige beperking kan op een van de volgende manieren worden omzeild:

* **Gebruik Azure Data Factory met twee Kopieer activiteiten**: 
  1. Gegevens in JSON-indeling ophalen van SQL naar een tekst bestand in een tussenliggende Blob-opslag locatie en 
  2. Laad gegevens uit het JSON-tekst bestand naar een container in Azure Cosmos DB.

* **Gebruik Azure Databricks om te lezen van SQL en te schrijven naar Azure Cosmos DB** -er worden hier twee opties weer gegeven.


We gaan deze benaderingen nader bekijken:

## <a name="azure-data-factory"></a>Azure Data Factory

Hoewel OrderDetails niet kan worden inge sloten als een JSON-matrix in het doel Cosmos DB document, kunnen we het probleem omzeilen door twee afzonderlijke Kopieer activiteiten te gebruiken.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 van de Kopieer activiteit: SqlJsonToBlobText

Voor de bron gegevens gebruiken we een SQL-query voor het ophalen van de resultatenset als één kolom met één JSON-object (dat de volg orde vertegenwoordigt) per rij met behulp van de SQL Server openjson en voor de mogelijkheden van JSON-pad:

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


Voor de Sink van de Kopieer activiteit SqlJsonToBlobText kiest u "tekst met scheidings tekens" en wijst u deze naar een specifieke map in Azure Blob Storage met een dynamisch gegenereerde unieke bestands naam (bijvoorbeeld ' @concat (pipeline (). RunId, '. json ').
Omdat het tekst bestand niet echt "gescheiden" is en we niet wilt dat het wordt geparseerd in afzonderlijke kolommen met komma's en u de dubbele aanhalings tekens (") wilt behouden, stellen we ' kolom scheidings teken ' in op een tab (' \t '), of een ander teken dat niet in het gegevens-en aanhalings tekens wordt gebruikt voor geen aanhalings teken.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Scherm opname van de instellingen van het kolom scheidings teken en de aanhalings tekens.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 van de Kopieer activiteit: BlobJsonToCosmos

Vervolgens wijzigen we onze ADF-pijp lijn door de tweede Kopieer activiteit toe te voegen die in Azure Blob Storage zoekt naar het tekst bestand dat door de eerste activiteit is gemaakt. Het wordt als ' JSON '-bron verwerkt om Cosmos DB Sink als één document per JSON-rij gevonden in het tekst bestand.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Scherm afbeelding die het JSON-bron bestand en de velden voor het bestandspad markeert.":::

U kunt eventueel ook een ' delete '-activiteit toevoegen aan de pijp lijn, zodat alle vorige bestanden in de map/orders/vóór elke uitvoering worden verwijderd. Onze ADF-pijp lijn ziet er nu ongeveer als volgt uit:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Scherm afbeelding die de Delete-activiteit markeert.":::

Nadat we de pijp lijn hierboven hebben geactiveerd, zien we een bestand dat is gemaakt op onze intermediaire Azure-Blob Storage locatie met één JSON-object per rij:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Scherm opname van het bestand dat wordt gemaakt met de JSON-objecten.":::

We zien ook bestellingen van documenten met correct Inge sloten OrderDetails die zijn ingevoegd in onze Cosmos DB verzameling:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Scherm afbeelding van de details van de bestelling als onderdeel van het Cosmos DB document":::


## <a name="azure-databricks"></a>Azure Databricks

We kunnen ook Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) gebruiken om de gegevens van onze SQL database bron te kopiëren naar de Azure Cosmos DB bestemming zonder de tussenliggende tekst/json-bestanden in Azure Blob Storage te maken. 

> [!NOTE]
> Voor de duidelijkheid en eenvoud zijn de onderstaande code fragmenten een dummy database wachtwoord, maar u moet altijd Azure Databricks geheimen gebruiken.
>

Eerst maken en koppelen we de vereiste [SQL-connector](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) en [Azure Cosmos DB connector](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) bibliotheken aan ons Azure Databricks-cluster. Start het cluster opnieuw op om ervoor te zorgen dat de tape wisselaars worden geladen.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Scherm afbeelding die laat zien waar u de vereiste SQL-connector en Azure Cosmos DB connector bibliotheken kunt maken en koppelen aan ons Azure Databricks cluster.":::

We presen teren nu twee voor beelden, voor scala en python. 

### <a name="scala"></a>Scala
Hier krijgen we de resultaten van de SQL-query met de uitvoer voor JSON naar een data frame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Scherm opname van de SQL-query uitvoer in een data frame.":::

We gaan nu verbinding maken met onze Cosmos DB-Data Base en-verzameling:

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

Ten slotte definiëren we ons schema en gebruiken we from_json om de data frame toe te passen voordat ze worden opgeslagen in de CosmosDB-verzameling.

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Scherm afbeelding die de juiste matrix markeert voor het opslaan in een Cosmos DB verzameling.":::


### <a name="python"></a>Python

Als alternatief kunt u ook JSON-trans formaties uitvoeren in Spark (als de bron database geen ondersteuning biedt voor JSON of een vergelijk bare bewerking), of als u parallelle bewerkingen wilt gebruiken voor een zeer grote gegevensset. Hier bieden we een PySpark-voor beeld. Begin met het configureren van de bron-en doel database verbindingen in de eerste cel:

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

Vervolgens wordt de bron database (in dit geval SQL Server) in de volg orde van de order-en order detail records opgevraagd, waarna de resultaten in Spark dataframes worden geplaatst. Er wordt ook een lijst gemaakt met alle order-Id's en een thread groep voor parallelle bewerkingen:

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

Maak vervolgens een functie voor het schrijven van orders naar de doel-SQL-API-verzameling. Met deze functie worden alle Order Details voor de opgegeven order-ID gefilterd, geconverteerd naar een JSON-matrix en wordt de matrix in een JSON-document ingevoegd dat wordt geschreven in de doel-SQL-API-verzameling voor die order:

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

Ten slotte noemen we het bovenstaande met behulp van een functie map in de thread groep om parallel uit te voeren, waarbij u de volg orde van de eerder gemaakte order-Id's doorgeeft.

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
In beide gevallen moeten we aan het eind het op de juiste manier opgeslagen Inge sloten OrderDetails binnen elk Bestel document in Cosmos DB verzameling ophalen:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gegevens modellering in azure Cosmos DB](./modeling-data.md)
* Meer informatie over [het model leren en partitioneren van gegevens op Azure Cosmos DB](./how-to-model-partition-example.md)