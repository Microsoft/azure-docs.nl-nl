---
title: Gegevens verplaatsen van en naar SQL Server
description: Meer informatie over het verplaatsen van gegevens naar/van SQL Server data base on-premises of in een Azure-VM met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368809"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Gegevens verplaatsen van en naar SQL Server met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-sqlserver-connector.md)
> * [Versie 2 (huidige versie)](../connector-sql-server.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [SQL Server-connector in v2](../connector-sql-server.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van/naar een SQL Server-Data Base. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **van een SQL Server Data Base** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar een SQL Server-Data Base**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Ondersteunde SQL Server-versies
Deze SQL Server-connector ondersteunt het kopiëren van gegevens van/naar de volgende versies van instance die lokaal worden gehost of in azure IaaS met behulp van SQL-verificatie en Windows-verificatie: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
De concepten en stappen die nodig zijn om verbinding te maken met SQL Server lokaal gehost of in azure IaaS (Infrastructure-as-a-Service) zijn hetzelfde. In beide gevallen moet u Data Management Gateway gebruiken voor connectiviteit.

Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Het instellen van een gateway-exemplaar is een vereiste voor het maken van verbinding met SQL Server.

Hoewel u een gateway kunt installeren op dezelfde lokale machine of in de Cloud-VM-instantie als de SQL Server voor betere prestaties, wordt u aangeraden ze op afzonderlijke computers te installeren. Als de gateway en SQL Server op afzonderlijke computers, wordt de bron conflicten verminderd.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een SQL Server-Data Base met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens uit een SQL Server Data Base naar een Azure Blob-opslag kopieert, maakt u twee gekoppelde services om uw SQL Server-Data Base en Azure Storage-account aan uw data factory te koppelen. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor SQL Server Data Base.
3. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de SQL-tabel op te geven in uw SQL Server-Data Base die de invoer gegevens bevat. En u maakt een andere gegevensset om de BLOB-container en de map op te geven die de gegevens bevat die zijn gekopieerd uit de SQL Server Data Base. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor eigenschappen van gegevens sets die specifiek zijn voor SQL Server Data Base.
4. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u SqlSource als bron en BlobSink als Sink voor de Kopieer activiteit. En als u kopieert van Azure Blob Storage naar SQL Server-Data Base, gebruikt u BlobSource en SqlSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor informatie over de eigenschappen van de Kopieer activiteit die specifiek zijn voor SQL Server Data Base. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie het gedeelte [JSON-voor beelden](#json-examples-for-copying-data-from-and-to-sql-server) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens van/naar een SQL Server-Data Base.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SQL Server:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een SQL Server-Data Base aan een Data Factory te koppelen. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| connectionString |Geef Connections Tring-gegevens op die nodig zijn om verbinding te maken met de SQL Server-Data Base via SQL-verificatie of Windows-verificatie. |Yes |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de SQL Server-Data Base. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u Windows-verificatie gebruikt. Voor beeld: **domainname \\ gebruikers naam**. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |

U kunt referenties versleutelen met de cmdlet **New-AzDataFactoryEncryptValue** en deze gebruiken in de Connection String, zoals wordt weer gegeven in het volgende voor beeld (eigenschap **EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Voorbeelden
**JSON voor het gebruik van SQL-verificatie**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON voor het gebruik van Windows-verificatie**

Data Management Gateway imiteert het opgegeven gebruikers account om verbinding te maken met de SQL Server-Data Base.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
In de voor beelden hebt u een gegevensset van het type **SqlServerTable** gebruikt om een tabel in een SQL Server-Data Base weer te geven.

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (SQL Server, Azure-Blob, Azure-tabel, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **SqlServerTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in de SQL Server Data Base-instantie waarnaar de gekoppelde service verwijst. |Yes |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Als u gegevens verplaatst van een SQL Server Data Base, stelt u het bron type in de Kopieer activiteit in op **SqlSource**. En als u gegevens naar een SQL Server Data Base verplaatst, stelt u het sink-type in de Kopieer activiteit in op **SqlSink**. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door SqlSource en SqlSink.

Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleids regels zijn beschikbaar voor alle typen activiteiten.

> [!NOTE]
> De Kopieer activiteit heeft slechts één invoer en produceert slechts één uitvoer.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

### <a name="sqlsource"></a>SqlSource
Wanneer de bron in een Kopieer activiteit van het type **SqlSource** is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: Select * from MyTable. Kan verwijzen naar meerdere tabellen vanuit de data base waarnaar wordt verwezen door de invoer gegevensset. Indien niet opgegeven, de SQL-instructie die wordt uitgevoerd: Selecteer een van de MyTable. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de Kopieer activiteit deze query uit op basis van de SQL Server database bron om de gegevens op te halen.

U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de sectie structuur zijn gedefinieerd, gebruikt om een SELECT-query te maken die wordt uitgevoerd op basis van de SQL Server Data Base. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName** gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **TABLENAME** in de JSON van de gegevensset. Er zijn geen validaties die worden uitgevoerd voor deze tabel.

### <a name="sqlsink"></a>SqlSink
**SqlSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript |Geef een query op om de Kopieer activiteit uit te voeren, zodat de gegevens van een specifiek segment worden opgeruimd. Zie de sectie Herhaal bare [kopieën](#repeatable-copy) voor meer informatie. |Een query-instructie. |No |
| sliceIdentifierColumnName |Geef de kolom naam voor de Kopieer activiteit op die moet worden gevuld met een automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer het opnieuw wordt uitgevoerd. Zie de sectie Herhaal bare [kopieën](#repeatable-copy) voor meer informatie. |Kolom naam van een kolom met het gegevens type binary (32). |No |
| sqlWriterStoredProcedureName |De naam van de opgeslagen procedure die definieert hoe bron gegevens worden toegepast in de doel tabel, bijvoorbeeld om upsert of trans formatie te gebruiken met uw eigen bedrijfs logica. <br/><br/>Houd er rekening mee dat deze opgeslagen procedure **per batch wordt geactiveerd**. Als u een bewerking wilt uitvoeren die slechts één keer wordt uitgevoerd en niets hoeft te doen met bron gegevens, bijvoorbeeld verwijderen/truncate, gebruikt u de `sqlWriterCleanupScript` eigenschap. |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |
| sqlWriterTableType |Geef de naam op van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Als u de Kopieer activiteit uitvoert, worden de gegevens die in een tijdelijke tabel worden verplaatst, met dit tabel type beschikbaar. De opgeslagen procedure code kan vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |De naam van een tabel type. |No |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar SQL Server
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). In de volgende voor beelden ziet u hoe u gegevens kopieert van en naar SQL Server en Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** vanuit een wille keurige bron worden gekopieerd naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Voor beeld: gegevens kopiëren van SQL Server naar Azure Blob
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [OnPremisesSqlServer](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [SqlServerTable](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [SqlSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een tijd reeks gekopieerd van een SQL Server tabel naar een Azure-Blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**SQL Server gekoppelde service**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Gekoppelde Azure Blob Storage-service**

```json
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
**Invoer gegevensset SQL Server**

In het voor beeld wordt ervan uitgegaan dat u in SQL Server een tabel ' MyTable ' hebt gemaakt en een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens. U kunt een query uitvoeren op meerdere tabellen binnen dezelfde data base met één gegevensset, maar er moet één tabel worden gebruikt voor de tabel naam van de gegevensset typeProperty.

Als u ' Extern ' instelt, informeert Data Factory service dat de gegevensset zich buiten het data factory bevindt en wordt deze niet geproduceerd door een activiteit in de data factory.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
**Pijp lijn met Kopieer activiteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van deze invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
In dit voor beeld is **sqlReaderQuery** opgegeven voor de SqlSource. De Kopieer activiteit voert deze query uit op basis van de SQL Server database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt). De sqlReaderQuery kan verwijzen naar meerdere tabellen in de data base waarnaar wordt verwezen door de invoer gegevensset. Het is niet beperkt tot de tabel die is ingesteld als de tabel naam van de gegevensset typeProperty.

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de sectie structuur zijn gedefinieerd, gebruikt om een SELECT-query te maken die wordt uitgevoerd op basis van de SQL Server Data Base. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

Zie de sectie [SQL-bron](#sqlsource) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door SqlSource en BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Voor beeld: gegevens kopiëren van een Azure-Blob naar SQL Server
In het volgende voor beeld ziet u:

1. De gekoppelde service van het type [OnPremisesSqlServer](#linked-service-properties).
2. De gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en SqlSink.

In het voor beeld worden elk uur gegevens van een tijd reeks gekopieerd van een Azure-Blob naar een SQL Server tabel. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**SQL Server gekoppelde service**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Gekoppelde Azure Blob Storage-service**

```json
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
**Invoer gegevensset voor Azure Blob**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte Year, month en Day van de begin tijd en de bestands naam maakt gebruik van het uur gedeelte van de begin tijd. met de instelling ' Extern ': ' waar ' wordt de Data Factory-service informeert dat de gegevensset extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**Uitvoer gegevensset SQL Server**

In het voor beeld worden gegevens gekopieerd naar een tabel met de naam ' MyTable ' in SQL Server. Maak de tabel in SQL Server met hetzelfde aantal kolommen als u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Pijp lijn met Kopieer activiteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van deze invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Verbindingsproblemen oplossen
1. Configureer uw SQL Server om externe verbindingen te accepteren. Start **SQL Server Management Studio**, klik met de rechter muisknop op **Server** en klik op **Eigenschappen**. Selecteer **verbindingen** in de lijst en Schakel **externe verbindingen met de server toestaan in**.

    ![Externe verbindingen inschakelen](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Zie [de configuratie optie voor de RAS-server configureren](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) voor gedetailleerde stappen.
2. Start **SQL Server Configuration Manager**. Vouw **SQL Server netwerk configuratie** voor de gewenste instantie uit en selecteer **protocollen voor MSSQLSERVER**. U ziet de protocollen in het rechterdeel venster. Schakel TCP/IP in door met de rechter muisknop op **TCP/IP** te klikken en op **inschakelen** te klikken.

    ![TCP/IP inschakelen](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zie [een server netwerkprotocol in-of uitschakelen](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) voor meer informatie en alternatieve manieren om het TCP/IP-protocol in te scha kelen.
3. Dubbel klik in hetzelfde venster op **TCP/IP** om het venster **TCP/IP-eigenschappen** te openen.
4. Schakel over naar het tabblad **IP-adressen** . Schuif omlaag om de sectie **IPAll** weer te geven. Noteer de **TCP-poort**(de standaard waarde is **1433**).
5. Maak een **regel voor de Windows Firewall** op de computer om binnenkomend verkeer via deze poort toe te staan.
6. **Verbinding controleren**: Gebruik SQL Server Management Studio van een andere computer om verbinding te maken met de SQL Server met behulp van een volledig gekwalificeerde naam. Bijvoorbeeld: " \<machine\> . \<domain\> . Corp. \<company\> . com, 1433. "

   > [!IMPORTANT]
   > 
   > Zie [gegevens verplaatsen tussen on-premises bronnen en de Cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) voor gedetailleerde informatie.
   > 
   > Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.


## <a name="identity-columns-in-the-target-database"></a>Identiteits kolommen in de doel database
In deze sectie wordt een voor beeld gegeven van het kopiëren van gegevens uit een bron tabel zonder id-kolom naar een doel tabel met een identiteits kolom.

**Bron tabel:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Doel tabel:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

U ziet dat de doel tabel een identiteits kolom heeft.

**JSON-definitie van bron gegevensset**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Zie voor een voor beeld van het aanroepen van een opgeslagen procedure van SQL sink in een Kopieer activiteit van een pijp lijn de [opgeslagen procedure voor SQL-Sink aanroepen in het artikel Kopieer activiteit](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-sql-server"></a>Type toewijzing voor SQL Server
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert de Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar & van SQL Server, worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa.

De toewijzing is hetzelfde als de SQL Server gegevens type toewijzing voor ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Herhaal bare kopie
Bij het kopiëren van gegevens naar SQL Server Data Base, voegt de Kopieer activiteit standaard gegevens toe aan de Sink-tabel. Als u in plaats daarvan een UPSERT wilt uitvoeren, raadpleegt u het artikel [Herhaal bare write-to-SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.