---
title: Overzicht van het gebruik van Linux Foundation Delta Lake in Apache Spark voor Azure Synapse Analytics
description: Leer hoe u Delta Lake in Apache Spark gebruikt voor Azure Synapse Analytics om tabellen met ACID-eigenschappen te maken en te gebruiken.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: d960b64d71861044aa1b0344ccbfdaf5e6328e6a
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120322"
---
# <a name="linux-foundation-delta-lake-overview"></a>Overzicht van Linux Foundation Delta Lake

De oorspronkelijke inhoud ([hier](https://docs.delta.io/latest/quick-start.html)) van dit artikel is aangepast voor meer duidelijkheid. Gebruik dit artikel om snel de belangrijkste functies van [Delta Lake](https://delta.io) te verkennen. In het artikel vindt u codefragmenten die aangeven hoe u gegevens uit Delta Lake-tabellen leest en hoe u hier gegevens heen schrijft op basis van query's over interactiviteit, batches en streaming. De codefragmenten zijn ook beschikbaar in een aantal notebooks ([PySpark hier](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala hier](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb) en [C# hier](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb))

Het volgende wordt behandeld:

* Een tabel maken
* Gegevens lezen
* Tabelgegevens bijwerken
* Tabelgegevens overschrijven
* Voorwaardelijke update zonder overschrijven
* Oudere gegevensversies lezen met behulp van Time Travel
* Een gegevensstroom naar een tabel schrijven
* Een stroom wijzigingen uit een tabel lezen
* SQL-ondersteuning

## <a name="configuration"></a>Configuratie

Zorg ervoor dat u het onderstaande aanpast voor zover dat in uw omgeving past.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Resulteert in:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Een tabel maken

Als u een Delta Lake-tabel wilt schrijven, schrijft u een DataFrame op basis van een DataFrame in de Delta-indeling. U kunt de indeling van Parquet, CSV, JSON, enzovoort, wijzigen in Delta.

In de volgende code ziet u hoe u een nieuwe Delta Lake-tabel maakt met behulp van het schema dat uit uw DataFrame is afgeleid.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Resulteert in:

| Id|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Gegevens lezen

U leest gegevens in uw Delta Lake-tabel door het pad naar de bestanden en de Delta-indeling op te geven.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Resulteert in:

| Id|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

De volgorde waarin de resultaten worden opgegeven, is anders dan hierboven staat aangezien er geen expliciete volgorde is opgegeven voordat de resultaten zijn uitgevoerd.

## <a name="update-table-data"></a>Tabelgegevens bijwerken

Delta Lake biedt ondersteuning voor verschillende bewerkingen om tabellen aan te passen met behulp van standaard-DataFrame-API's. Dit is een van de grote verbeteringen die door de Delta-indeling worden toegevoegd. In het volgende voorbeeld wordt een batchtaak uitgevoerd om de gegevens in de tabel te overschrijven.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Resulteert in:

| Id|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Hier ziet u dat alle vijf records zijn bijgewerkt met nieuwe waarden.

## <a name="save-as-catalog-tables"></a>Opslaan als catalogustabellen

Delta Lake kan schrijven naar beheerde of externe catalogustabellen.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Resulteert in:

|database|         tableName|isTemporary|
|--------|------------------|-----------|
| standaardinstelling|externaldeltatable|      false|
| standaardinstelling| manageddeltatable|      false|

Met deze code hebt u een nieuwe tabel in de catalogus gemaakt op basis van een bestaand DataFrame. Deze tabel wordt een beheerde tabel genoemd. Vervolgens hebt u een nieuwe externe tabel in de catalogus gedefinieerd waarvoor een bestaande locatie wordt gebruikt. Dit wordt een externe tabel genoemd. In de uitvoer ziet u dat beide tabellen, ongeacht de manier waarop ze zijn gemaakt, in de catalogus worden vermeld.

Bekijk nu de uitgebreide eigenschappen van beide tabellen

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Resulteert in:

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Gedetailleerde tabelgegevens  |                                                                                                             |       |
|Database                    |standaardinstelling                                                                                                      |       |
|Tabel                       |manageddeltatable                                                                                            |       |
|Eigenaar                       |trusted-service-user                                                                                         |       |
|Gemaakt om                |za 25 apr 00:35:34 UTC 2020                                                                                 |       |
|Laatste toegang                 |do 1 jan 00:00:00 UTC 1970                                                                                 |       |
|Created By                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|Type                        |BEHEERD                                                                                                      |       |
|Provider                    |delta                                                                                                        |       |
|Tabeleigenschappen            |[transient_lastDdlTime=1587774934]                                                                           |       |
|statistieken                  |2407 bytes                                                                                                   |       |
|Locatie                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde-bibliotheek               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Opslageigenschappen          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Resulteert in:

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Gedetailleerde tabelgegevens  |                                                                      |       |
|Database                    |standaardinstelling                                                               |       |
|Tabel                       |externaldeltatable                                                    |       |
|Eigenaar                       |trusted-service-user                                                  |       |
|Gemaakt om                |za 25 apr 00:35:38 UTC 2020                                          |       |
|Laatste toegang                 |do 1 jan 00:00:00 UTC 1970                                          |       |
|Created By                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|Type                        |EXTERN                                                              |       |
|Provider                    |DELTA                                                                 |       |
|Tabeleigenschappen            |[transient_lastDdlTime=1587774938]                                    |       |
|Locatie                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde-bibliotheek               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Opslageigenschappen          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Voorwaardelijke update zonder overschrijven

Delta Lake biedt programmatische API's om gegevens voorwaardelijk bij te werken, te verwijderen en samen te voegen in tabellen (dit wordt samen een 'upsert' genoemd).

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Resulteert in:

| Id|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Hier hebt u 100 toegevoegd aan elke even id.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Resulteert in:

| Id|
|---|
|  5|
|  7|
|  9|

U ziet dat elke even rij is verwijderd.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Resulteert in:

| Id|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Hier hebt u een combinatie van de bestaande gegevens. Aan de bestaande gegevens is een waarde van -1 toegewezen in het codepad update(WhenMatched). Die nieuwe gegevens die boven op het codefragment zijn gemaakt en die via het invoegcodepad (WhenNotMatched) zijn toegevoegd, zijn ook toegevoegd.

### <a name="history"></a>Geschiedenis

Delta Lake biedt de mogelijkheid om de geschiedenis van een tabel weer te geven. Dat wil zeggen: de wijzigingen die aan de onderliggende Delta-tabel zijn gemaakt. In de onderstaande cel ziet u hoe eenvoudig het is om de geschiedenis te bekijken.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Resulteert in:

|versie|          tijdstempel|userId|userName|bewerking|                                                operationParameters| taak|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|25-04-2020 00:36:27|  null|    null|    SAMENVOEGEN|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    null|     null|          3|          null|        false|
|      3|25-04-2020 00:36:08|  null|    null|   DELETE|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    null|     null|          2|          null|        false|
|      2|25-04-2020 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    null|     null|          1|          null|        false|
|      1|25-04-2020 00:35:05|  null|    null|    SCHRIJVEN|                             [mode -> Overwrite, partitionBy -> []]|null|    null|     null|          0|          null|        false|
|      0|25-04-2020 00:34:34|  null|    null|    SCHRIJVEN|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    null|     null|       null|          null|         true|

Hier ziet u alle aanpassingen die op de bovenstaande codefragmenten zijn aangebracht.

## <a name="read-older-versions-of-data-using-time-travel"></a>Oudere gegevensversies lezen met behulp van Time Travel

U kunt met behulp van de functie Time Travel een query uitvoeren op vorige momentopnamen van uw Delta Lake-tabel. Als u toegang wilt krijgen tot de gegevens die u hebt overschreven, kunt u een query uitvoeren op een momentopname van de tabel voordat u de eerste gegevensset hebt overschreven met behulp van de optie versionAsOf.

Zodra u de onderstaande cel uitvoert, ziet u als het goed is de eerste gegevensset van vóór u deze hebt overschreven. Time Travel is een extreem krachtige functie die van het vermogen van het Delta Lake-transactielogboek profiteert om toegang te krijgen tot gegevens die niet langer in de tabel staan. Als u de versie 0-optie verwijdert (of versie 1 opgeeft), ziet u weer de nieuwere gegevens. Zie [Query uitvoeren op een oudere momentopname van een tabel](https://docs.delta.io/latest/delta-batch.html#deltatimetravel) voor meer informatie.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Resulteert in:

| Id|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Hier ziet u dat u bent teruggegaan naar de eerste versie van de gegevens.

## <a name="write-a-stream-of-data-to-a-table"></a>Een gegevensstroom naar een tabel schrijven

U kunt ook gegevens naar een Delta Lake-tabel schrijven met behulp van Gestructureerd streamen van Spark. Het Delta Lake-transactielogboek garandeert exact één verwerking, zelfs als er geen andere stromen of batchquery's gelijktijdig worden uitgevoerd op de tabel. Standaard worden stromen uitgevoerd in de toevoegmodus, waarmee nieuwe records aan de tabel worden toegevoegd.

Zie [Lees- en schrijfbewerkingen in tabelstreaming](https://docs.delta.io/latest/delta-streaming.html) voor meer informatie over Delta Lake-integratie met Gestructureerd streamen.

Bekijk bij de onderstaande cellen wat we aan het doen zijn:

* Cel 30: de zojuist toegevoegde gegevens weergeven
* Cel 31: de geschiedenis inspecteren
* Cel 32: de gestructureerde streamingtaak stoppen
* Cel 33: de geschiedenis inspecteren (u ziet dat er geen gegevens meer worden toegevoegd)

Eerst gaat u een eenvoudige Spark-streamingtaak instellen om een reeks te genereren en ervoor te zorgen dat de taak gegevens naar uw Delta-tabel schrijft.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Een stroom wijzigingen uit een tabel lezen

Terwijl de stream gegevens naar de Delta Lake-tabel schrijft, kunt u gegevens uit die tabel lezen als een streamingbron. U kunt bijvoorbeeld nog een streamingquery's starten waarmee alle wijzigingen van de Delta Lake-tabel worden afgedrukt.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Resulteert in:

| Id|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Resulteert in:

|versie|          tijdstempel|       bewerking|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|25-04-2020 00:37:09|STREAMINGUPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|25-04-2020 00:36:27|           SAMENVOEGEN|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|25-04-2020 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|25-04-2020 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|25-04-2020 00:35:05|           SCHRIJVEN|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|25-04-2020 00:34:34|           SCHRIJVEN|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

Hier plaatst u een aantal minder interessante kolommen om de geschiedenisweergave te vereenvoudigen.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Resulteert in:

|versie|          tijdstempel|       bewerking|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|25-04-2020 00:37:09|STREAMINGUPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|25-04-2020 00:36:27|           SAMENVOEGEN|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|25-04-2020 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|25-04-2020 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|25-04-2020 00:35:05|           SCHRIJVEN|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|25-04-2020 00:34:34|           SCHRIJVEN|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Parquet omzetten in Delta

U kunt ter plekke gegevens in de Parquet-indeling omzetten naar Delta.

Hier test u of de bestaande tabel een Delta-indeling heeft of niet.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulteert in:

Niet waar

Nu gaat u de gegevens omzetten in de Delta-indeling en controleren of dit heeft gewerkt.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulteert in:

Waar

## <a name="sql-support"></a>SQL-ondersteuning

Delta biedt ondersteuning voor opdrachten van het hulpprogramma voor tabellen via SQL. U kunt SQL gebruiken voor het volgende:

* De geschiedenis van een Delta-tabel ophalen
* Een Delta-tabel isoleren
* Een Parquet-bestand omzetten in een Delta-indeling

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Resulteert in:

|versie|          tijdstempel|userId|userName|       bewerking| operationParameters| taak|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|25-04-2020 00:37:09|  null|    null|STREAMINGUPDATE|[outputMode -> Ap...|null|    null|     null|          4|          null|         true|
|      4|25-04-2020 00:36:27|  null|    null|           SAMENVOEGEN|[predicate -> (ol...|null|    null|     null|          3|          null|        false|
|      3|25-04-2020 00:36:08|  null|    null|          DELETE|[predicate -> ["(...|null|    null|     null|          2|          null|        false|
|      2|25-04-2020 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    null|     null|          1|          null|        false|
|      1|25-04-2020 00:35:05|  null|    null|           SCHRIJVEN|[mode -> Overwrit...|null|    null|     null|          0|          null|        false|
|      0|25-04-2020 00:34:34|  null|    null|           SCHRIJVEN|[mode -> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Resulteert in:

|                leertraject|
|--------------------|
|abfss://data@arca...|

U gaat nu controleren of een tabel geen tabel in de Delta-indeling is. Vervolgens zet u de gegevens met behulp van Spark SQL om in de Delta-indeling en bevestigt u dat de gegevens goed zijn omgezet.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resulteert in:

Waar

Zie de [pagina met documentatie voor Delta Lake](https://docs.delta.io/latest/delta-intro.html) voor de volledige documentatie

Zie [Delta Lake-project](https://github.com/delta-io/delta) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](../index.yml)