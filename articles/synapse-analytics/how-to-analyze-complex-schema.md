---
title: Schema analyseren met matrices en geneste structuren
description: Matrices en geneste structuren analyseren met Apache Spark en SQL
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: d4004887a4806a5b8e50429031fb204f4eec5000
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627962"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Complexe gegevens typen analyseren in azure Synapse Analytics

Dit artikel is relevant voor Parquet-bestanden en-containers in [Azure Synapse-koppeling voor Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). U kunt Spark of SQL gebruiken om gegevens te lezen of transformeren met complexe schema's zoals matrices of geneste structuren. Het volgende voor beeld wordt met één document voltooid, maar het kan eenvoudig worden geschaald naar miljarden documenten met Spark of SQL. De code die in dit artikel is opgenomen, maakt gebruik van PySpark (python).

## <a name="use-case"></a>Gebruiksvoorbeeld

Complexe gegevens typen worden steeds vaker gebruikt en vormen een uitdaging voor gegevens technici. Het analyseren van geneste schema's en matrices kan tijdrovende en complexe SQL-query's omvatten. Het kan ook lastig zijn om het gegevens type geneste kolommen te hernoemen of te casten. Wanneer u werkt met diep geneste objecten, kunt u ook prestatie problemen tegen komen.

Data engineers moeten begrijpen hoe complexe gegevens typen efficiënt kunnen worden verwerkt en eenvoudig toegankelijk zijn voor iedereen. In het volgende voor beeld gebruikt u Spark in azure Synapse Analytics om objecten te lezen en transformeren naar een platte structuur via gegevens frames. U gebruikt het serverloze model van SQL in azure Synapse Analytics om dergelijke objecten rechtstreeks te doorzoeken en deze resultaten te retour neren als een normale tabel.

## <a name="what-are-arrays-and-nested-structures"></a>Wat zijn matrices en geneste structuren?

Het volgende object is afkomstig uit [Application Insights](../azure-monitor/app/app-insights-overview.md). In dit object zijn geneste structuren en matrices die geneste structuren bevatten.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Schema voorbeeld van matrices en geneste structuren
Wanneer u het schema van het gegevens frame van het object ( **DF**) afdrukt met de opdracht `df.printschema` , ziet u de volgende weer gave:

* Yellow vertegenwoordigt geneste structuren.
* Groen vertegenwoordigt een matrix met twee elementen.

[![Code met gele en groene markering, met schema oorsprong](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` en `_etag` zijn aan het systeem toegevoegd als het document is opgenomen in het Azure Cosmos DB transactionele archief.

Het voor gaande gegevens frame telt voor vijf kolommen en één rij. Na de trans formatie bevat het gegevens frame met de curator 13 kolommen en twee rijen, in een tabel vorm.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Geneste structuren plat en matrices exploderen

Met Spark in azure Synapse Analytics kunt u eenvoudig geneste structuren omzetten in kolommen en matrix elementen in meerdere rijen. Gebruik de volgende stappen voor de implementatie.

[![Stroom diagram met stappen voor Spark-trans formaties](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definieer een functie voor het afvlakken van het geneste schema

U kunt deze functie zonder wijziging gebruiken. Maak een cel in een [PySpark-notebook](quickstart-apache-spark-notebook.md) met de volgende functie:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>De functie gebruiken voor het afvlakken van het geneste schema

In deze stap wordt het geneste schema van het gegevens frame (**DF**) afgevlakt in een nieuw gegevens frame ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

De weergave functie moet 10 kolommen en één rij retour neren. De matrix en de geneste elementen ervan zijn nog steeds beschikbaar.

### <a name="transform-the-array"></a>De matrix transformeren

Hier transformeert u de matrix, `context_custom_dimensions` , in het gegevens frame `df_flat` , naar een nieuw gegevens frame `df_flat_explode` . In de volgende code definieert u ook welke kolom u wilt selecteren:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

De weergave functie moet 10 kolommen en twee rijen retour neren. De volgende stap is het samen voegen van geneste schema's met de functie die u in stap 1 hebt gedefinieerd.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>De functie gebruiken voor het afvlakken van het geneste schema

Ten slotte gebruikt u de functie voor het afvlakken van het geneste schema van het gegevens frame `df_flat_explode` , in een nieuw gegevens frame `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

De weergave functie moet 13 kolommen en twee rijen weer geven.

De functie `printSchema` van het gegevens frame `df_flat_explode_flat` retourneert het volgende resultaat:

[![Code die het definitieve schema weergeeft](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Direct matrices en geneste structuren lezen

Met het serverloze model van SQL kunt u weer gaven en tabellen voor dergelijke objecten opvragen en maken.

Eerst moeten gebruikers de volgende taxonomie gebruiken, afhankelijk van de manier waarop de gegevens zijn opgeslagen. Alles wat in hoofd letters wordt weer gegeven, is specifiek voor uw use-case:

| Bulk | Indeling |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = AccountName; data base = DATABASENAME; verzameling = verzamelingnaam; Region = REGIONTOQUERY ', geheim = ' YOURSECRET ' |' CosmosDB ' (Azure Synapse-koppeling)|


Vervang elk veld als volgt:
* ' Uw BULKSGEWIJS bovenstaande ' is de connection string van de gegevens bron waarmee u verbinding maakt.
* ' Uw TYPE hierboven ' is de indeling die u gebruikt om verbinding te maken met de bron.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Er zijn twee verschillende soorten bewerkingen:

- Het eerste bewerkings type wordt aangegeven in de volgende regel code, waarmee de kolom wordt gedefinieerd `contextdataeventTime` die verwijst naar het geneste element `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Deze regel definieert de kolom met de naam `contextdataeventTime` die verwijst naar het geneste element `Context>Data>eventTime` .

- Het tweede bewerkings type gebruikt `cross apply` om nieuwe rijen te maken voor elk element onder de matrix. Vervolgens wordt elk genest object gedefinieerd. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Als de matrix 5 elementen met 4 geneste structuren had, worden in het serverloze model van SQL 5 rijen en 4 kolommen geretourneerd. Het model van SQL serverloze kan een query uitvoeren op de locatie, de matrix wordt toegewezen aan twee rijen en alle geneste structuren worden weer gegeven in kolommen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het opvragen van de Synapse-koppeling voor Azure Cosmos DB met Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Query uitvoeren op met Parquet geneste typen](./sql/query-parquet-nested-types.md)