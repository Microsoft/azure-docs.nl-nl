---
title: Gegevens kopiëren van/naar Azure SQL Database
description: Meer informatie over het kopiëren van gegevens naar/van Azure SQL Database met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370509"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure SQL Database met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-sql-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-sql-database.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure SQL database-connector in v2](../connector-azure-sql-database.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar Azure SQL Database te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **van Azure SQL database** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar Azure SQL database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Ondersteund verificatie type
Azure SQL Database-connector ondersteunt basis verificatie.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een Azure SQL Database met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens kopieert van een Azure Blob-opslag naar Azure SQL Database, maakt u twee gekoppelde services om uw Azure-opslag account te koppelen en Azure SQL Database aan uw data factory. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure SQL database.
3. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de BLOB-container en de map op te geven die de invoer gegevens bevat. En u maakt een andere gegevensset om de SQL-tabel op te geven in Azure SQL Database die de gegevens bevat die zijn gekopieerd uit de Blob-opslag. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor informatie over eigenschappen die specifiek zijn voor Azure data Lake Store.
4. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u BlobSource als bron en SqlSink als Sink voor de Kopieer activiteit. En als u kopieert van Azure SQL Database naar Azure Blob Storage, gebruikt u SqlSource en BlobSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor de eigenschappen van de Kopieer activiteit die specifiek zijn voor Azure SQL database. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie de sectie [JSON-voor beelden](#json-examples-for-copying-data-to-and-from-sql-database) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens van/naar een Azure SQL database.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure SQL Database:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Een gekoppelde Azure SQL-service koppelt Azure SQL Database aan uw data factory. De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de gekoppelde Azure SQL-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **AzureSqlDatabase** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met het Azure SQL Database-exemplaar voor de Connections Tring-eigenschap. Alleen basis verificatie wordt ondersteund. |Yes |

> [!IMPORTANT]
> Configureer [Azure SQL database firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) de database server zodanig dat [Azure-Services toegang krijgen tot de server](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Als u gegevens kopieert naar Azure SQL Database van buiten Azure, met inbegrip van on-premises gegevens bronnen met data factory gateway, moet u ook het juiste IP-adres bereik configureren voor de computer die gegevens naar Azure SQL Database verzendt.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoer-of uitvoer gegevens in Azure SQL Database weer te geven, stelt u de eigenschap type van de gegevensset in op: **AzureSqlTable**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde Azure SQL-service.

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **AzureSqlTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in het Azure SQL Database exemplaar waarnaar de gekoppelde service verwijst. |Yes |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

> [!NOTE]
> De Kopieer activiteit heeft slechts één invoer en produceert slechts één uitvoer.

Terwijl de eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Als u gegevens verplaatst van Azure SQL Database, stelt u het bron type in de Kopieer activiteit in op **SqlSource**. En als u gegevens naar Azure SQL Database verplaatst, stelt u het sink-type in de Kopieer activiteit in op **SqlSink**. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door SqlSource en SqlSink.

### <a name="sqlsource"></a>SqlSource
Als de bron van het type **SqlSource** is in Kopieer activiteit, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de Kopieer activiteit deze query uit op basis van de Azure SQL database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in het gedeelte structuur van de JSON van de gegevensset zijn gedefinieerd, gebruikt om een query () te maken die wordt `select column1, column2 from mytable` uitgevoerd op basis van de Azure SQL database. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName** gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **TABLENAME** in de JSON van de gegevensset. Er zijn geen validaties die worden uitgevoerd voor deze tabel.
>
>

### <a name="sqlsource-example"></a>SqlSource-voor beeld

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**De definitie van de opgeslagen procedure:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript |Geef een query op voor de Kopieer activiteit die moet worden uitgevoerd, zodat de gegevens van een specifiek segment worden opgeruimd. Zie [Herhaal bare kopieën](#repeatable-copy)voor meer informatie. |Een query-instructie. |No |
| sliceIdentifierColumnName |Geef een kolom naam op voor de Kopieer activiteit die moet worden gevuld met een automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer het opnieuw wordt uitgevoerd. Zie [Herhaal bare kopieën](#repeatable-copy)voor meer informatie. |Kolom naam van een kolom met het gegevens type binary (32). |No |
| sqlWriterStoredProcedureName |De naam van de opgeslagen procedure die definieert hoe bron gegevens worden toegepast in de doel tabel, bijvoorbeeld om upsert of trans formatie te gebruiken met uw eigen bedrijfs logica. <br/><br/>Houd er rekening mee dat deze opgeslagen procedure **per batch wordt geactiveerd**. Als u een bewerking wilt uitvoeren die slechts één keer wordt uitgevoerd en niets hoeft te doen met bron gegevens, bijvoorbeeld verwijderen/truncate, gebruikt u de `sqlWriterCleanupScript` eigenschap. |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |
| sqlWriterTableType |Geef een naam op voor het tabel type dat moet worden gebruikt in de opgeslagen procedure. Als u de Kopieer activiteit uitvoert, worden de gegevens die in een tijdelijke tabel worden verplaatst, met dit tabel type beschikbaar. De opgeslagen procedure code kan vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |De naam van een tabel type. |No |

#### <a name="sqlsink-example"></a>SqlSink-voor beeld

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar SQL Database
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kunt kopiëren van en naar Azure SQL Database en Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** vanuit een wille keurige bron worden gekopieerd naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Voor beeld: gegevens kopiëren van Azure SQL Database naar Azure Blob
Hetzelfde definieert de volgende Data Factory entiteiten:

1. Een gekoppelde service van het type [AzureSqlDatabase](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [SqlSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van de tijd reeks (elk uur, dagelijks, enzovoort) gekopieerd van een tabel in Azure SQL Database naar een blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Azure SQL Database gekoppelde service:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zie de sectie gekoppelde Azure SQL-service voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.

**Gekoppelde Azure Blob Storage-service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Zie het artikel over [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.


**Azure SQL-invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u in Azure SQL een tabel ' MyTable ' hebt gemaakt en een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens.

Als u ' Extern ' instelt, informeert de Azure Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

Zie de sectie eigenschappen van Azure SQL dataset type voor de lijst met eigenschappen die worden ondersteund door dit type gegevensset.

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Zie de sectie eigenschappen van het [type Azure Blob-gegevensset](data-factory-azure-blob-connector.md#dataset-properties) voor de lijst met eigenschappen die worden ondersteund door dit type gegevensset.

**Een Kopieer activiteit in een pijp lijn met de SQL-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
In het voor beeld is **sqlReaderQuery** opgegeven voor de SqlSource. De Kopieer activiteit voert deze query uit op basis van de Azure SQL Database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in het gedeelte structuur van de JSON van de gegevensset zijn gedefinieerd, gebruikt om een query te maken die wordt uitgevoerd op basis van de Azure SQL Database. Bijvoorbeeld: `select column1, column2 from mytable`. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

Zie de sectie [SQL-bron](#sqlsource) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSource en BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Voor beeld: gegevens kopiëren van een Azure-Blob naar Azure SQL Database
Het voor beeld definieert de volgende Data Factory entiteiten:

1. Een gekoppelde service van het type [AzureSqlDatabase](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [SqlSink](#copy-activity-properties).

In het voor beeld worden alle gegevens van de tijd reeks (elk uur, dagelijks, enzovoort) gekopieerd van de Azure-Blob naar een tabel in Azure SQL Database elk uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure SQL-service:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zie de sectie gekoppelde Azure SQL-service voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.

**Gekoppelde Azure Blob Storage-service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Zie het artikel over [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor de lijst met eigenschappen die door deze gekoppelde service worden ondersteund.


**Invoer gegevensset voor Azure Blob:**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte Year, month en Day van de begin tijd en de bestands naam maakt gebruik van het uur gedeelte van de begin tijd. met de instelling ' Extern ': ' waar ' wordt de Data Factory-service informeert dat deze tabel extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
Zie de sectie eigenschappen van het [type Azure Blob-gegevensset](data-factory-azure-blob-connector.md#dataset-properties) voor de lijst met eigenschappen die worden ondersteund door dit type gegevensset.

**Uitvoer gegevensset Azure SQL Database:**

In het voor beeld worden gegevens gekopieerd naar een tabel met de naam ' MyTable ' in Azure SQL. Maak de tabel in Azure SQL met hetzelfde aantal kolommen als u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Zie de sectie eigenschappen van Azure SQL dataset type voor de lijst met eigenschappen die worden ondersteund door dit type gegevensset.

**Een Kopieer activiteit in een pijp lijn met Blob-bron en SQL-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Zie de sectie [SQL-Sink](#sqlsink) en [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSink en BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identiteits kolommen in de doel database
In deze sectie vindt u een voor beeld van het kopiëren van gegevens uit een bron tabel zonder een identiteits kolom naar een doel tabel met een identiteits kolom.

**Bron tabel:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Doel tabel:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
U ziet dat de doel tabel een identiteits kolom heeft.

**JSON-definitie van bron gegevensset**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**JSON-definitie van doel gegevensset**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

U ziet dat de bron-en doel tabel een ander schema hebben (doel heeft een aanvullende kolom met een identiteit). In dit scenario moet u de eigenschap **structure** opgeven in de definitie van de doel gegevensset, die de kolom Identity niet bevat.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Opgeslagen procedure vanuit SQL-Sink aanroepen
Zie voor een voor beeld van het aanroepen van een opgeslagen procedure van SQL sink in een Kopieer activiteit van een pijp lijn een [opgeslagen procedure aanroepen voor SQL sink in het artikel Kopieer activiteit](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-azure-sql-database"></a>Type toewijzing voor Azure SQL Database
Zoals vermeld in het artikel Kopieer activiteit van [gegevens verplaatsing](data-factory-data-movement-activities.md) voert automatische type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar en van Azure SQL Database, worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa. De toewijzing is hetzelfde als de SQL Server gegevens type toewijzing voor ADO.NET.

| Type SQL Server data base-engine | .NET Framework type |
| --- | --- |
| bigint |Int64 |
| binair |Byte [] |
| bit |Booleaans |
| char |Teken reeks, char [] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Date time offset |Date time offset |
| Decimaal |Decimaal |
| FILESTREAM-kenmerk (varbinary (max)) |Byte [] |
| Float |Dubbel |
| image |Byte [] |
| int |Int32 |
| money |Decimaal |
| nchar |Teken reeks, char [] |
| ntext |Teken reeks, char [] |
| numeriek |Decimaal |
| nvarchar |Teken reeks, char [] |
| werkelijk |Enkelvoudig |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimaal |
| sql_variant |Object |
| tekst |Teken reeks, char [] |
| tijd |TimeSpan |
| tijdstempel |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-copy"></a>Herhaal bare kopie
Bij het kopiëren van gegevens naar SQL Server Data Base, voegt de Kopieer activiteit standaard gegevens toe aan de Sink-tabel. Als u in plaats daarvan een UPSERT wilt uitvoeren, raadpleegt u het artikel [Herhaal bare write-to-SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.