---
title: Opgeslagen procedure aanroepen vanuit Azure Data Factory Kopieer activiteit
description: Meer informatie over het aanroepen van een opgeslagen procedure in Azure SQL Database of SQL Server vanuit een Azure Data Factory Kopieer activiteit.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374963"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>De opgeslagen procedure vanuit een Kopieer activiteit in Azure Data Factory aanroepen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens transformeren met behulp van opgeslagen procedure activiteit in Data Factory](../transform-data-using-stored-procedure.md).


Bij het kopiëren van gegevens naar [SQL Server](data-factory-sqlserver-connector.md) of [Azure SQL database](data-factory-azure-sql-connector.md), kunt u de **SqlSink** in de Kopieer activiteit configureren om een opgeslagen procedure aan te roepen. U kunt de opgeslagen procedure gebruiken voor het uitvoeren van eventuele aanvullende verwerking (het samen voegen van kolommen, het opzoeken van waarden, invoeging in meerdere tabellen, enzovoort). Dit is vereist voordat u gegevens in de doel tabel invoegt. Deze functie maakt gebruik van [para meters met tabel waarden](/dotnet/framework/data/adonet/sql/table-valued-parameters). 

In het volgende voor beeld ziet u hoe u een opgeslagen procedure in een SQL Server-Data Base aanroept vanuit een Data Factory pijp lijn (Kopieer activiteit):  

## <a name="output-dataset-json"></a>JSON van uitvoer gegevensset
Stel in de JSON van de uitvoer gegevensset het **type** in op: **SqlServerTable**. Stel deze in op **AzureSqlTable** om te gebruiken met Azure SQL database. De waarde voor de eigenschap **TableName** moet overeenkomen met de naam van de eerste para meter van de opgeslagen procedure.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>De sectie SqlSink in de JSON van de Kopieer activiteit
Definieer de sectie **SqlSink** in de JSON Copy activity als volgt. Als u een opgeslagen procedure wilt aanroepen terwijl u gegevens in de Sink/doel database invoegt, geeft u waarden op voor de eigenschappen **SqlWriterStoredProcedureName** en **SqlWriterTableType** . Zie voor beschrijvingen van deze eigenschappen [SqlSink sectie in het artikel over de SQL Server-connector](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedure 
Definieer in uw data base de opgeslagen procedure met de naam **SqlWriterStoredProcedureName**. De opgeslagen procedure verwerkt invoer gegevens uit de brongegevens opslag en voegt gegevens toe aan een tabel in de doel database. De naam van de eerste para meter van de opgeslagen procedure moet overeenkomen met de TableName die is gedefinieerd in de gegevensset JSON (marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definitie van tabel type
Definieer in uw data base het tabel type met de naam **SqlWriterTableType**. Het schema van het tabel type moet overeenkomen met het schema van de invoer gegevensset.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende connector artikelen voor volledige JSON-voor beelden: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)